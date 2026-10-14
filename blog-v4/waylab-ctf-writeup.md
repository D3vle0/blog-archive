# Waylab CTF Writeup


## WAYLAB CTF

교내 ITLAB 중 하나인 WAYLAB에서 1학년 친구들이 준비한 CTF이다.
문제 난이도는 쉬운 편이다.
Rank: 5th place (2791pts)

![rank](https://cdn.discordapp.com/attachments/802579615268732958/868857637775753307/unknown.png)

## PWN

### race [250pts]

![pwn](https://cdn.discordapp.com/attachments/681373386211065859/868834658069139506/unknown.png)

대충 숫자를 같게 맞추다보니 풀렸다 (?)

`WAYLAB{e3asy!e3asy!e3asy_Rac3_C0nDiti0n}`

## REV

### smallandbig [50pts]

![rev](https://cdn.discordapp.com/attachments/802579615268732958/868384760416829470/unknown_2.png)

ida 로 열면 플래그가 나온다.

`wAylAB{CRypTOloVINg}`

## FOR

### L00k @t h3x [40pts]

![for](https://cdn.discordapp.com/attachments/802579615268732958/868385812276973568/unknown.png)

`WAYLAB{C4N_Y0u_S33_m3}`

### there's something hidden [150pts]

![for](https://cdn.discordapp.com/attachments/802579615268732958/868855717770170378/unknown.png)

IEND 뒤에 zip 파일이 숨어있다.

`WAYLAB{D0_YoU_Kn0W_B1nW41K?}`

### flag [200pts]

이 파일은 비밀번호가 걸린것 처럼 보이는 압축 파일이다. 따라서 정석 풀이대로라면 hex값을 수정 해야하지만, 의도치 않게 mac 에서는 압축이 그냥 풀린다.

`WAYLAB{C3ntr@l_Direct0ry_1s_g0oD}`

### Open The Stegosaurus [200pts]

문제 이름을 보아 OpenStego를 활용한 steganography 문제임을 알 수 있다.

![for](https://cdn.discordapp.com/attachments/802579615268732958/868444915229671454/unknown.png)

![for](https://cdn.discordapp.com/attachments/802579615268732958/868444996989243482/unknown.png)

비밀번호를 입력하지 않고 그냥 추출할 수 있다.

![for](https://cdn.discordapp.com/attachments/802579615268732958/868445073090691072/unknown.png)

hex 값을 보면 png 파일 관련 청크가 있는데 헤더만 손상되어 있다.

![for](https://cdn.discordapp.com/attachments/802579615268732958/868445126920372224/unknown.png)

따라서 png 파일 헤더인 `89 50 4E 47 0D 0A 1A 0A` 로 바꿔주고 열면 플래그가 나온다.

![for](https://cdn.discordapp.com/attachments/802579615268732958/868445156418920448/f_o_r_t_n_e_c_o_k_e.png)

`WAYLAB{0p3n1ng_St3g0_1s_funny}`

### 잃어버린 내 안드로이드 롬 파일을 찾아서 [200pts]

압축 풀면 WAYLAB CTF 폴더가 있는데, 그 안에는 apk 파일이 들어있다.
설치하고 실행하면 플래그가 나온다.

![for](https://cdn.discordapp.com/attachments/802579615268732958/868390610892107816/Screenshot_2021-07-24-16-13-55-415_com.example.waylabctf.jpg)

`WAYLAB{this_was_little_hard}`

## MISC

### DISCORD [1pt]

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868856687350661220/867997688170414120.png)

Discord server emoji에 숨어있다.

`WAYLAB{Di5c0rd_w17h_3m0ji}`

### PAINT [70pts]

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868393341040095242/unknown.png)

그림판으로 색칠하면 된다.

`WAYLAB{WH173_MINU5_!}`

### baldi [80pts]

```python
from pwn import *

p=remote("waylab.kr", 3134)
p.recvuntil("-------\n")
p.recvline()
print("===== WAYLAB CTF baldi Solver =====")
for i in range(1, 501):
    p.recvline()
    if i % 50 == 0:
        print(f"{i}/500")
    exp = (p.recv().decode('utf-8'))[:-3]
    p.sendline(str(eval(exp)%4294967296))
    for j in range(3):
        if i == 500:
            print(p.recvline())
            p.interactive()
        p.recvline()
```

`WAYLAB{Funny_Ma7h_7im3_wi7h_ba1di}`

### Fortune Message [100pts]

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868395720988852284/unknown.png)

Stegsolve

`WAYLAB{@_Me55ag3_1n_@_F0r7un3_C00k13}`

### 응애 [150pts]

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868396806709587998/unknown.png)

응애
응애
응애
응애
응애
...
하는 ppt 파일이 주어진다.
97 슬라이드에 ball=64라고 적힌 것을 보아 100 슬라이드에 적힌 문자열을 base64 decode 하면 된다.

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868396855195750400/unknown.png)

각각 `50`, `slide` 이므로 50번째 슬라이드를 보자.

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868397944284512276/unknown.png)

아무것도 없기 때문에 xml 데이터를 까보자.
`.pptx` 확장자를 `.zip` 으로 바꾸고 압축을 푼 뒤 `ppt/slides/slide50.xml` 을 본다.

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868398814409670696/unknown.png)

`WAYLAB{this_was_easy_yeah?}`

### Weather Score [200pts]

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868399292803592222/unknown.png)

ppt 파일의 제목과 주어진 숫자들을 보아 Short Weather Codes 임을 알 수 있다.

```python
cp = [9, 21, 24, 6, 24, 28, 9, 21, 24, 11, 20, 10, 10, 14, 26, 14, 17, 25]
for i in cp:
    print(chr(93-i), end="")
```

`WAYLAB{THEWEATHERISSOCOLD}`

### National anthem [250pts]

애국가 음악 파일이 주어지는데, 맨 마지막 부분에 노이즈가 들어가있다.
소리가 spectrogram 으로 분석해야 하는듯한 노이즈여서 audacity로 열어주었다.

![misc](https://cdn.discordapp.com/attachments/802579615268732958/868407135011217408/unknown.png)

`WAYLAB{3a57_53afo0d}`

## CRYPTO

### AB [100pts]

`ABABABBBABAAAAABABABBAABABAABBAAABAAAAABABAAAABA{ABAAAAABABAAAABA_ABBBABAAAABBAAAA_ABAAAABAAABBAAABABBABBBAABAAAAAAABBBAABAABBBBAAB_ABBBABAAAABBAAAA_AABBABABABBBABAAABBBAABAABBABAABABBABBBAABBAABBB_ABBABAABAABBABAB_ABBAABBBAABBAAAAAABBAAAAABBAABAA}`

처음에 암호문 보고 bacon cipher 인줄 알았는데 페이크였다.  
A는 0, B는 1로 치환한 후 8글자씩 끊어서 bin to str 하면 된다.

### one_by_one_forward [150pts]

```py
flag = open("./flag.txt", 'r').read()

flag = flag.encode()
hex_val = flag.hex()

f_l = hex_val[0] + hex_val[-1]
hex_val = hex_val[1:-1]

flag_hexArr = []
for i in range(0, len(hex_val), 2):
    flag_hexArr.append(hex_val[i:i+2])
    
flag_hexArr.append(f_l)
encrypted_flag = ''.join(flag_hexArr)
print(encrypted_flag)
```

위의 코드를 실행시켰을때 결과값이 `741594c41427b4833785f346c67305231356d5f336e4330646575d` 라고 한다.
역연산 코드를 짜기 귀찮아서 규칙성에 집중을 해보았다.

1. 플래그의 첫 부분은 `WAYLAB{` 일테니 `WAYLAB{aaa}` 라는 문자열을 위의 코드에 넣어보았다.
결과는 `741594c41427b61616175d`, 문제에서 주어진 값과 비교하자면 앞부분의 `741594c41427b` 와 뒤의 `75d` 가 일치하기 때문에 복잡하지는 않은 알고리즘으로 생각할 수 있다.

2. `hex_val = flag.hex()` 은 플래그를 str to hex 하는 것인데, 그 값과 최종 연산 값을 비교해본다. str to hex 한 값은 `5741594c41427b6161617d`, 최종 값은 `741594c41427b61616175d` 이다.
-> 플래그를 str to hex 하고, 뒤에서 두번째 문자열을 맨 앞에 집어넣는다.

따라서 플래그를 str to hex 한 값은 `5741594c41427b4833785f346c67305231356d5f336e433064657d` 이므로 답은 아래와 같다.

```py
cp = "741594c41427b4833785f346c67305231356d5f336e4330646575d"
last = cp[-1]
cp = cp[:len(cp)-1]
last2 = cp[-1]
cp = cp[:len(cp)-1]
cp = last2 + cp + last
print(bytes.fromhex(cp).decode('utf-8'))
```

`WAYLAB{H3x_4lg0R15m_3nC0de}`

### Low_or_High [350pts]

```py
from RSAwienerHacker import *
from pwn import *
from gmpy2 import *
import json

p=remote("waylab.kr", 1002)
for i in range(4):
    p.recvline()

def wiener(cp):
    e = cp["e"]
    n = cp["N"]
    c = cp["c"]
    d = hack_RSA(e,n)
    s = str(hex(pow(c, d, n)))[2:]
    res=bytes.fromhex(s).decode('utf-8')
    return res

def lowExponent(cp):
    c = cp["c"]
    with local_context() as ctx:
        ctx.precision = 3000
        m = iroot(c, 3)[0]
        s = str(hex(int(m)))[2:]
        res = bytes.fromhex(s).decode('utf-8')
        return res

def getAttackMethod(i):
    method = p.recvline().decode('utf-8')
    print(f"[*] {i+1}. {method[:-1]}")
    cp = json.loads(p.recvline().decode('utf-8'))
    if "Wiener" in method:
        res = wiener(cp)
    else:
        res = lowExponent(cp)
    print(f"[+] result = {res}")
    p.recv()
    p.sendline(res)
    if i==10:
        p.interactive()
    p.recvline()
    p.recvline()
    print()
        
if __name__ == "__main__":
    print("===== WAYLAB CTF RSA Solver =====")
    for i in range(11):
        getAttackMethod(i)
```

wiener's attack 은 [이곳에서 참고했다](https://github.com/pablocelayes/rsa-wiener-attack/blob/master/RSAwienerHacker.py).

![crypto](https://cdn.discordapp.com/attachments/802579615268732958/868417016430747668/unknown.png)

`WAYLAB{Easy_RSAH4CK_1snt_1t?}`

