---
title: "Mint My Own NFT with Ethereum and Klaytn"
date: 2022-05-09T10:08:24+09:00
draft: false
category: "Blockchain"
---

## NFT란?

대체 불가능 토큰(Non-fungible token, NFT)이란 블록체인 기술을 이용해서 디지털 자산의 소유주를 증명하는 가상의 토큰(token)이다. 그림, 영상 등의 디지털 파일을 가리키는 주소를 토큰 안에 담음으로서 그 고유한 원본성 및 소유권을 나타내는 용도로 사용된다. 즉, 일종의 가상 진품 증명서.

## OpenSea에 Metamask 연결

이 글에서는 이더리움과 클레이튼 가상화폐를 사용하여 판매하기 위한 방법을 소개하겠다. 먼저 이더리움을 하기 위해서는 metamask 지갑이 필요한데, 지갑 생성 방법은 건너뛰겠다.  
OpenSea 프로필 페이지에 들어가면 어떤 지갑을 연결하고 싶은지 나오는데, MetaMask를 클릭한다.

![img](img/how-to-mint-nft/1.png)

![img](img/how-to-mint-nft/2.png)

Next를 눌러서 지갑을 연결한다.

## 이더리움 NFT 발행

사이트 오른쪽 위에 Create를 누르면 아래와 같은 서명 요청 창이 뜬다.

![img](img/how-to-mint-nft/3.png)

이제 Create 페이지에서 작품, 작품 이름, 작품 설명을 넣고 Create 버튼을 누르면 된다. 이렇게 해서 내가 민팅한 NFT는 [이곳에 있다.](https://opensea.io/assets/0x495f947276749ce646f68ac8c248420045cb7b5e/88988240129995691731219668518355291049649786232460414333766223904845552680961)

## 그럼 판매는?

![img](img/how-to-mint-nft/4.png)

우측 상단에 sell 버튼을 누르면, 몇 이더리움에 작품 판매를 할지 입력할 수 있다. 다만 이 과정에서는 지갑에 이더리움이 존재해야 하기 때문에 거래소에서 이더리움을 구매하고 진행하도록 하자. 참고로 OpenSea에 처음으로 NFT 작품 판매를 할 때는 수수료가 들어가는데, 이더리움 특성상 gas fee가 많이 나와서 약 15만원 정도의 비용이 발생한다. OpenSea 수수료는 처음 팔 때만 부과되고 그 이후에는 공제된다. 이 글에서는 거래소에서 가상화폐를 구매하고 작품을 판매하는 과정은 기록하지 않겠다.

## KrafterSpace에 Kaikas 연결

KrafterSpace란 카카오 자회사 그라운드X가 운영하는 국내 NFT 마켓플레이스다. OpenSea처럼 판매를 하지는 못하지만 NFT 작품 민팅만 할 수 있어서, KrafterSpace에 작품을 올리고 OpenSea에 지갑 연동을 시켜주어야 한다. 이렇게 하는 이유는 클레이튼을 이용하여 NFT 민팅을 하기 위함인데, 클레이튼은 gas fee가 저렴하기 때문에 작품 판매 시 부담이 없다.

![img](img/how-to-mint-nft/5.png)

Kaikas란 Klaytn을 사용하기 위한 지갑 프로그램이다. Kaikas 지갑 생성 후 KrafterSpace에 연결하고 우측 상단에 발행하기를 클릭한다.

![img](img/how-to-mint-nft/6.png)

작품, 작품 이름, 설명을 입력하고 NFT 발행 버튼을 클릭한다.

## OpenSea에 Kaikas 연결

MetaMask 연결과 동일한 방식으로 Kaikas를 연결해주자. 그러면 아까 전에 MetaMask를 이용해서 올렸던 게정과는 다른 계정이 만들어진다.

![img](img/how-to-mint-nft/7.png)

## 클레이튼 NFT 발행

로그인이 되면 자동으로 KrafterSpace에 올렸던 NFT가 OpenSea에도 불러와진다.

![img](img/how-to-mint-nft/8.png)

작품명 위에 KrafterSpace 표시를 확인할 수 있다. 작품 sell 버튼을 누르면 클레이튼이 표시되고, 클레이튼을 활용하여 판매를 할 수 있게 되었다. gas fee는 60원밖에 부과되지 않는다.

![img](img/how-to-mint-nft/9.png)

이렇게 이더리움 기반 NFT를 민팅하고 싶으면 MetaMask, 클레이튼은 Kaikas로 로그인 하여 나만의 NFT 작품을 자유자재로 민팅할 수 있다.
