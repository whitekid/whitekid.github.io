---
id: 729
title: MySQL의 aes_encrypt / aes_decrypt와 호환되는 python/ php 구현
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=729
permalink: /archives/729
dsq_thread_id:
  - 714643308
---
Nate 해킹사건 때문에 데이터 암호화를 보다 더 강화하기로 했다. 물론 [AES][1]로 하자고 그래서 하는데, 부득불 mysql의 aes\_encrypt / aes\_decrypt와 호환되게 한다고 해서 하다가 몇가지 꼼수를 부리게 되었다.

## AES

표준 암호화/ 복호화 알고리즘은 미국 어디선가 공모해서 여러가지 응모한 알고리즘 중에 rijndael을 선택하였고, 그중에 128bit 블럭 알고리즘은 표준으로 정하고 AES(Advanced Encrypt Standard)라고 이름을 붙혔다.

결국 rijndael-128 == AES 인 것이다.

rijndael 의 모드

  * ECB: 각 블럭은 서로 독립적이다. MySQL에서 쓰는 모드는 이거다.
  * CFB, CBC, CTR 등 나머지 모드는 약간 미묘한 차이가 있지만, MySQL은 ECB를 쓰기에 나중에 시간나면 공부하자.

## MySQL의 AES의 제한사항은

  * 128 비트 밖에 지원하지 못한다. 196, 256등 다른 비트를 지원하지 못하며 지원하려면 소스를 다시 컴파일해야한다.
  * 다시 컴파일 한다고 그래도 한가지 방식밖에 못쓴다. ㅡㅡ
  * initialization vector를 사용하지 않는다. mcrypt에서는 random iv를 사용해서 보안을 한층 더 강화할 수 있을 것 같은데...
  * 블럭 비트의 여유 바이트를 맞추기 위해서 데이터에 약간의 추가 데이터를 더하는데, 안의 데이터는 추가할 데이터의 길이만큼을 chr(length(pading)))을 더해서 맞춘다.
  * 패딩 길이는 0은 없다. 1부터 시작한다. 0이 없는 이유는 0이 붙으면 다시 decrypt할때 어디까지 잘라내야할 지 곤란하기 때문이다.

## 구현

  * rijndael은 그냥 [mcrypt][2]를 쓴다. MySQL은 aes 소스 자체를 포함하고 있다. 이게 mcrypt인지 아닌지는 모름..
  * AES == rijndael-128 / ecb

### Python 버전

참고로 python에서 padding은 \x00을 사용한다.

{% gist 5677992 %}


### PHP 버전

php 버전의 문제는 bin2hex는 php 버전이 있지만, 역은 undocumented다. 그래서 구현해줘야한다.

{% gist 5678001 %}


참고

  * MySQL의 AES가 128면 불안해 하는 사람(^^)들이 있다면, MySQL의 aes 함수들은 포기하고 libmcrypt를 이용해서 stored procedure를 만드는 것이 좋다.
  * 근데 굳이 MySQL의 command line에서 이것을 쓴다는 것은 글쎄 개인적인 소견으론 바람직하지 않는 것 같다.
  * MySQL에서 사용하는 것을 포기한다면 initialization vector를 사용하는 것이 좋겠다.
  * 음 다음에 데이터 암호화 한다면 아래처럼 해볼듯...
      * 랜덤 iv 생성 -> 데이터 암호화 (with salt) -> iv 자체도 암호화 -> 약간 섞어서 최종 데이터 생성 -> bin2hex

 [1]: http://en.wikipedia.org/wiki/Advanced_Encryption_Standard
 [2]: http://mcrypt.sf.net