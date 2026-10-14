---
title: "Hugo로 만든 정적 사이트 암호화하기 🔒"
date: 2025-12-08    
draft: false
categories: ["Coding"]
tags: ["hugo", "encryption"]
cover:
  image: img/encrypt-hugo-page/1.jpg
  caption: ""
ShowToc: true
TocOpen: true
---

## hugo-protector

Hugo 블로그에서 민감한 콘텐츠를 AES-256-GCM으로 암호화하고, 방문자가 비밀번호를 입력하면 브라우저에서 직접 복호화하여 보여주는 라이브러리다.

## 왜 만들었나?

Hugo와 같은 정적 사이트 생성기를 사용하면 빠르고 간단하게 정적 웹사이트를 만들 수 있다. 하지만 정적 사이트는 동적 사이트에 비해 기능이 제한적일 수 있다. 특히 자바스크립트를 통해서 서버와 통신을 한다던지, 로그인 기능을 제공하는 것은 어렵다. Hugo로 블로그를 운영하면서 특정 콘텐츠는 비공개로 유지하고 싶을 때가 있다. Hugo는 정적 사이트 생성기이기 때문에 서버 측 인증을 구현하기 어렵고, git에 소스를 저장할 때 민감한 내용이 평문으로 남으면 보안상 문제가 된다. 이때 필요한 것이 네이버 블로그, 티스토리 등에서 쓰이는 비밀 글 기능이다. Hugo 블로그의 **모든 코드를 public으로 공개하고 싶고, commit history에는 비밀 글과 관련된 내역을 남기고 싶지 않은 사람들을 위해** [hugo-protector](https://github.com/k-atusa/hugo-protector)를 개발하게 되었다.

## 프로젝트 구조

```
hugo-protector/
├── bin/
│   └── hugo-protector.js      # CLI 진입점
├── src/
│   ├── cli.js                 # CLI 로직 (옵션 파싱, 입출력)
│   └── encryption.js          # AES-256-GCM 암호화/복호화 핵심 로직
├── static/
│   └── hugo-protector/
│       └── protector.js       # 브라우저 런타임 (Web Crypto API)
├── layouts/
│   ├── shortcodes/
│   │   └── protector.html     # 부분 콘텐츠 보호 shortcode
│   └── partials/
│       └── protector/
│           └── full_page.html # 전체 페이지 보호 partial
└── setup.sh                   # 원클릭 설치 스크립트
```

---

## 코드별 상세 설명

### 1. `src/encryption.js` - 암호화 핵심 모듈

Node.js의 `crypto` 모듈을 사용하여 AES-256-GCM 암호화를 수행한다.

```javascript
const crypto = require('node:crypto');

const DEFAULT_ITERATIONS = 310_000;  // PBKDF2 반복 횟수 (보안 강도)
const SALT_LENGTH = 16;              // Salt 길이 (bytes)
const IV_LENGTH = 12;                // 초기화 벡터 길이 (bytes)
const KEY_LENGTH = 32;               // 256비트 키 (bytes)
const ALGORITHM = 'aes-256-gcm';     // 사용 알고리즘
```

#### `deriveKey(password, salt, iterations)`
비밀번호로부터 암호화 키를 유도한다. PBKDF2-SHA256 알고리즘을 사용하며, 기본 310,000회 반복으로 무차별 대입 공격을 어렵게 만든다.

```javascript
const deriveKey = (password, salt, iterations) =>
  crypto.pbkdf2Sync(password, salt, iterations, KEY_LENGTH, 'sha256');
```

#### `encryptText(plaintext, password, options)`
평문을 암호화하여 base64 인코딩된 JSON 페이로드를 반환한다.

```javascript
const encryptText = (plaintext, password, options = {}) => {
  const iterations = Number(options.iterations) || DEFAULT_ITERATIONS;
  const salt = crypto.randomBytes(SALT_LENGTH);   // 매번 새로운 salt
  const iv = crypto.randomBytes(IV_LENGTH);       // 매번 새로운 IV
  const key = deriveKey(password, salt, iterations);

  const cipher = crypto.createCipheriv(ALGORITHM, key, iv);
  const ciphertext = Buffer.concat([cipher.update(plaintext, 'utf8'), cipher.final()]);
  const authTag = cipher.getAuthTag();            // GCM 인증 태그 (무결성 검증)

  const payload = {
    v: 1,
    alg: 'AES-256-GCM',
    iter: iterations,
    salt: toBase64(salt),
    iv: toBase64(iv),
    ct: toBase64(ciphertext),
    tag: toBase64(authTag)
  };

  return Buffer.from(JSON.stringify(payload), 'utf8').toString('base64');
};
```

**핵심 포인트:**
- Salt와 IV는 매번 랜덤 생성되어 같은 평문+비밀번호라도 다른 암호문이 생성된다.
- GCM 모드의 authTag로 암호문 변조를 탐지할 수 있다.
- 페이로드에 복호화에 필요한 모든 메타데이터가 포함되어 있어 독립적으로 복호화가 가능하다.


### 2. `src/cli.js` - 명령줄 인터페이스

터미널에서 암호화를 수행할 수 있는 CLI를 제공한다.

#### 주요 옵션
```
-i, --input <file>          파일에서 평문 읽기
-t, --text <string>         문자열 직접 입력
-p, --password <value>      비밀번호 직접 전달 (테스트용)
    --password-file <file>  파일에서 비밀번호 읽기 (권장)
-m, --mode <shortcode|page> 출력 형식 (기본: shortcode)
  --format <raw|helper>   raw=페이로드만, helper=사용 스니펫
  --shortcode-format <html|markdown> shortcode helper에 format 속성 포함
  --prompt <text>         shortcode helper에 prompt 속성 포함
  --button <text>         shortcode helper에 button 속성 포함
  --hint <text>           shortcode helper에 hint 속성 포함
```

#### `parseArgs(argv)` - 인자 파싱
명령줄 인자를 파싱하여 객체로 변환한다. `--text`, `--password` 같은 플래그와 값을 매칭한다.

#### `readPassword(args)` - 비밀번호 읽기
우선순위: `--password` → `--password-file` → env `HUGO_PROTECTOR_PASSWORD`

```javascript
const readPassword = args => {
  if (args.password) return args.password;
  if (args.passwordFile) {
    return fs.readFileSync(path.resolve(args.passwordFile), 'utf8').trim();
  }
  if (process.env.HUGO_PROTECTOR_PASSWORD) {
    return process.env.HUGO_PROTECTOR_PASSWORD;
  }
  throw new Error('Password not provided.');
};
```

#### `renderHelper(mode, payload)` - 출력 포맷팅
mode에 따라 Hugo에서 바로 사용할 수 있는 스니펫을 생성한다.

<!-- #### 사용 예시

```bash
# 단일 문자열을 암호화해 shortcode 스니펫을 얻는다
npx hugo-protector encrypt \
  --text "<p>비밀 단락</p>" \
  --password-file .pwd

# 마크다운 원문을 암호화하고, CLI에서 UI 관련 속성을 함께 포함한다
npx hugo-protector encrypt \
  --text "## 내부 공유\n- 항목1" \
  --password-file .pwd \
  --shortcode-format markdown \
  --prompt "팀 비밀번호" \
  --button "열기" \
  --hint "슬랙 공지 참고"

# 전체 페이지용 페이로드를 생성한다
npx hugo-protector encrypt \
  --input public/secret-page.html \
  --password-file .pwd \
  --mode page
``` -->

### Shortcode 옵션과 Markdown 렌더링

`layouts/shortcodes/protector.html`은 암호문을 `<div data-hugo-protector-*>`로 감싸고 런타임에서 읽을 수 있는 메타데이터를 포함한다. shortcode 속성은 다음과 같다.

- `payload` (필수): CLI가 출력한 base64 문자열
- `prompt` (선택): 비밀번호 입력 폼 레이블
- `button` (선택): 제출 버튼 텍스트
- `hint` (선택): 입력 폼 하단에 노출되는 힌트 문구
- `format` (선택): `html` 또는 `markdown`. 기본값은 `html`이며, `markdown`으로 지정하면 복호화된 원문을 런타임이 즉시 HTML로 변환한다.

CLI에서 `--shortcode-format markdown`을 지정하면 helper 출력에 `format="markdown"` 속성이 자동으로 포함되므로, 스니펫을 별도로 수정할 필요가 없다. 동일하게 `--prompt`, `--button`, `--hint` 플래그를 사용하면 비밀번호 폼 카피 문구를 CLI 수준에서 바로 세팅할 수 있다.

마크다운 변환은 헤딩, 목록, 인라인 강조(**bold**, *italic*), 코드 블록, 인라인 링크 정도를 다룬다.  

shortcode가 복호화되면 `## 제목`은 `<h2>제목</h2>`로, `- 항목`은 `<ul><li>항목</li></ul>`로 렌더링된다. HTML을 직접 암호화하고 싶다면 `format`을 생략하거나 `html`로 두면 된다.


### 3. `bin/hugo-protector.js` - CLI 진입점

```javascript
#!/usr/bin/env node
const { run } = require('../src/cli');

run().catch(error => {
  console.error(`[hugo-protector] ${error.message}`);
  process.exit(1);
});
```

`package.json`의 `bin` 필드와 연결되어 `npx hugo-protector` 명령으로 실행된다.


### 4. `static/hugo-protector/protector.js` - 브라우저 런타임

방문자의 브라우저에서 실행되어 암호문을 복호화한다. Web Crypto API를 사용한다.

#### 초기화 및 환경 검사
```javascript
(function () {
  if (!window.crypto || !window.crypto.subtle) {
    console.error('[hugo-protector] Web Crypto API is not available.');
    return;
  }
  const encoder = new TextEncoder();
  const decoder = new TextDecoder();
  const subtle = window.crypto.subtle;
  // ...
})();
```

#### `ensureStyles()` - 스타일 자동 주입
복호화된 콘텐츠의 줄바꿈이 보존되도록 CSS를 동적으로 추가한다.

```javascript
const ensureStyles = () => {
  if (document.getElementById('hugo-protector-styles')) return;
  const style = document.createElement('style');
  style.id = 'hugo-protector-styles';
  style.textContent = '[data-hugo-protector-mode="block"]{white-space:pre-wrap;word-break:break-word;}';
  document.head.appendChild(style);
};
```

#### `deriveKey(password, salt, iterations)` - 키 유도 (브라우저)
Web Crypto API의 PBKDF2를 사용하여 서버 측과 동일한 방식으로 키를 유도한다.

```javascript
const deriveKey = async (password, salt, iterations) => {
  const keyMaterial = await subtle.importKey(
    'raw',
    encoder.encode(password),
    'PBKDF2',
    false,
    ['deriveKey']
  );

  return subtle.deriveKey(
    { name: 'PBKDF2', salt, iterations, hash: 'SHA-256' },
    keyMaterial,
    { name: 'AES-GCM', length: 256 },
    false,
    ['decrypt']
  );
};
```

#### `decryptPayload(payloadBase64, password)` - 복호화
base64 페이로드를 파싱하고 AES-GCM으로 복호화한다.

```javascript
const decryptPayload = async (payloadBase64, password) => {
  const payload = parsePayload(payloadBase64);
  const salt = base64ToUint8Array(payload.salt);
  const iv = base64ToUint8Array(payload.iv);
  const ciphertext = base64ToUint8Array(payload.ct);
  const tag = base64ToUint8Array(payload.tag);

  const combined = concatUint8Arrays(ciphertext, tag);
  const key = await deriveKey(password, salt, payload.iter || 310000);

  const plaintextBuffer = await subtle.decrypt(
    { name: 'AES-GCM', iv, tagLength: 128 },
    key,
    combined
  );
  return decoder.decode(plaintextBuffer);
};
```

#### `createForm(options)` - 비밀번호 입력 폼 생성
동적으로 비밀번호 입력 UI를 생성한다.

```javascript
const createForm = (options = {}) => {
  const wrapper = document.createElement('div');
  wrapper.className = 'hugo-protector-form';

  const form = document.createElement('form');
  const label = document.createElement('label');
  label.textContent = options.prompt || 'Enter password';

  const input = document.createElement('input');
  input.type = 'password';
  input.placeholder = options.placeholder || 'Password';

  const submit = document.createElement('button');
  submit.textContent = options.buttonText || 'Unlock';

  const message = document.createElement('div');
  message.className = 'hugo-protector-message';
  // ...
  return { wrapper, form, input, message };
};
```

#### `mountBlock(el)` - 부분 콘텐츠 마운트
`data-hugo-protector-mode="block"` 속성을 가진 요소를 찾아 비밀번호 폼으로 교체하고, 제출 시 복호화를 수행한다.

```javascript
const mountBlock = el => {
  const payload = el.getAttribute('data-hugo-protector-payload');
  // ... 폼 생성 및 마운트 ...

  form.addEventListener('submit', async event => {
    event.preventDefault();
    try {
      const plaintext = await decryptPayload(payload, password);
      el.innerHTML = plaintext;  // 복호화된 내용으로 교체
    } catch (error) {
      renderError(message, 'Unable to decrypt payload');
    }
  });
};
```

#### `mountFullPage(el)` - 전체 페이지 마운트
전체 페이지 보호 시 오버레이를 표시하고, 복호화 성공 시 `<main>` 영역에 콘텐츠를 주입한다.

#### `init()` - 자동 초기화
페이지 로드 시 자동으로 보호된 블록들을 찾아 마운트한다.

```javascript
const init = () => {
  ensureStyles();
  initBlocks();
  initFullPage();
};

if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', init);
} else {
  init();
}
```

#### 전역 API
```javascript
window.HugoProtector = {
  decryptPayload,   // 수동 복호화 가능
  refresh: init     // 동적 콘텐츠 로드 후 재초기화
};
```

---

## 보안 고려사항

- **비밀번호 강도**: 짧은 비밀번호는 무차별 대입 공격에 취약하다.
- **HTTPS 필수**: HTTP에서는 비밀번호가 네트워크에 노출될 수 있다.
- **클라이언트 사이드 한계**: 결국 브라우저에서 복호화되므로, 비밀번호를 아는 사람은 평문을 볼 수 있다.
- **Git 히스토리**: 한 번이라도 평문을 커밋했다면 히스토리에 남으므로, 처음부터 암호문만 커밋하도록 주의한다.

## 설치 및 사용법

### 원클릭 설치
Hugo 블로그 디렉토리 루트에서:
```bash
bash <(curl -s https://raw.githubusercontent.com/k-atusa/hugo-protector/main/setup.sh)
```

### 수동 설치
1. `static/hugo-protector/protector.js`를 Hugo 블로그 디렉토리로 복사
2. `layouts/shortcodes/protector.html` 복사
3. `layouts/partials/protector/full_page.html` 복사
4. `baseof.html`에 스크립트 태그 추가:
   ```html
   <script defer src="{{ "hugo-protector/protector.js" | relURL }}"></script>
   ```

### 콘텐츠 암호화

```bash
git clone https://github.com/k-atusa/hugo-protector
cd hugo-protector
npm install
```

CLI는 `npx hugo-protector encrypt [옵션]`으로 실행한다. `--text`/`--input`으로 평문을 입력하고, `--password-file` 또는 `--password`로 비밀번호를 입력한다. 아래 플래그들을 조합하면 Hugo 숏코드 출력과 비밀번호 폼 UI를 CLI에서 한 번에 설정할 수 있다.

- `--mode <shortcode|page>`: 숏코드 또는 full-page front matter helper(기본 `shortcode`).
- `--format <raw|helper>`: `raw`는 페이로드만, `helper`는 Hugo에 붙여넣을 수 있는 스니펫을 출력(기본 `helper`).
- `--shortcode-format <html|markdown>`: 원문이 HTML인지 마크다운인지 지정(기본 `html`).
- `--prompt`, `--button`, `--hint`: 보호 폼의 레이블/버튼/힌트 문구를 즉시 정의한다.

```sh
npx hugo-protector encrypt 
  --text "**굵은 텍스트** *기울임꼴 텍스트*"
  --password 1234
  --mode shortcode
  --format helper
  --prompt "비밀번호"
  --button "제출"
  --hint "여기에 1234를 입력하세요."
```

출력된 스니펫은 아래처럼 Markdown 렌더링 플래그와 폼 문구를 포함한다.

{{< protector payload="eyJ2IjoxLCJhbGciOiJBRVMtMjU2LUdDTSIsIml0ZXIiOjMxMDAwMCwic2FsdCI6IkdhUHJmSnZjSVNWeHZyWlNGQWhpanc9PSIsIml2IjoiVUZGNzEzNUs2cUNSWTNKaCIsImN0IjoiMU5hcDRxRTRwcy92WTNLVmRCNzZSQkR5MVNPaU1TcnkvdW84cTQyc0hXaklVT3NOT1AwMDRkbmdLZXpnIiwidGFnIjoiV25PMGZTU1FQbVAvcmhkRFl3V0FjQT09In0=" format="markdown" prompt="비밀번호" button="제출" hint="여기에 1234를 입력하세요." >}}

전체 페이지 보호에는 `--mode page`를 사용하여 front matter snippet을 만들고 대상 레이아웃에서 `protectors/full_page.html` partial을 호출하면 된다.

```bash
npx hugo-protector encrypt \
  --input page-content.html \
  --password-file .pwd \
  --mode page \
  --format helper
```

> [!WARNING] 전체 페이지 암호화 workflow
> 1. Hugo로 렌더링된 최종 HTML을 CLI 입력으로 사용해 암호화 페이로드를 생성한다.  
> 2. 생성된 페이로드를 front matter의 `protector_full_page_payload`에 추가하고 레이아웃에서 `protector/full_page.html` partial을 호출한다.  
> 3. 배포본에는 암호문과 스크립트만 남고, 방문 시 전면 오버레이가 비밀번호를 요구한 뒤 복호화된 HTML을 `<main>` 등에 삽입해 렌더링한다.
> 
> 블로그 markdown 코드까지 암호화하는 것이 아니라, Hugo가 생성한 최종 HTML을 암호화 대상으로 삼아야 한다는 점에 유의한다.
---

## 마무리

Hugo Protector는 정적 사이트의 한계 내에서 콘텐츠 보호를 구현하는 실용적인 솔루션이다. 완벽한 보안은 아니지만, Git에 민감한 정보를 평문으로 저장하지 않으면서 간단한 접근 제어를 제공한다.

GitHub: https://github.com/k-atusa/hugo-protector
