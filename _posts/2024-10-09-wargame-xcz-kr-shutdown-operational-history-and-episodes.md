---
layout: post
language: KO
title: "워게임 XCZ.KR 서비스 종료: 운영 기록 및 에피소드"
date: 2024-10-09 00:00:00
description: '해킹/보안 교육 목적의 워게임 XCZ.KR 서비스 종료를 앞두며, 운영 기록 및 겪었던 에피소드들을 정리했습니다.'
tags:
- wargame
- xcz.kr
categories:
- miscellaneous
---
# 워게임 XCZ.KR 서비스 종료: 운영 기록 및 에피소드
![XCZ.KR 로고](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-logo.png)

## 1. Introduction
2012년 10월 21일 오픈 이후 12년간 유지해왔던 워게임 사이트인 XCZ.KR의 서비스를 종료하고, 이에 대한 운영 기록과 기억에 남는 에피소드들을 정리해보려고 한다.
사실, 본 서비스의 개발이나 운영을 엄청나게 잘했다거나, 자랑할만한 기술이 들어갔다거나, 특별한 성과가 있던것은 아니지만 오늘날 이러한 글을 적는 이유는 본 XCZ.KR 서비스와 관련된 데이터 및 기억들을 남겨두고 싶기도하고, 아직까지 서비스에 관심을 가지고 이용해 주시는 분들도 계시기에, 마지막을 빌어 이용자 분들과 사이트 운영에 도움을 준 모든이들에게 감사의 인사와 관련된 이야기들도 남겨두고 싶어서 작성하게 되었다.

## 2. 런칭 준비
### 2.1. 서비스 운영 배경
사실, 처음 서비스를 개발했던 배경은 뚜렷한 목적성을 가지고 시작했던것은 아니였다.
어릴적 개발/해킹 분야를 좋아하던 나로서는, 내가 상상하던 프로그램이 있으면 이를 구현하는 개발을 했었고, CTF에도 참여하며 해킹을 하고 있었기에, 공부의 목적이나 어떤 이득을 위해 시작하기보다는 그 당시 상황과 내가 좋아하는 것을 한다는점에 맞물려 재미의 동기가 크게 작용해 시작하게된 프로젝트였다.
거기에 더 나아가 마침, 당시에는 국내에 공부할 수 있는 자료나 워게임 사이트 등이 많지 않았기에, "이러한 서비스가 많아지면 좋겠다, 만들 수 있을것 같은데?" 라는 막연한 생각으로 시작했던것 같다.

### 2.2. 서비스 런칭 과정
사이트 오픈을 위해서는 웹 사이트를 구축하는 것과, 문제를 출제하는 것 두 가지를 준비해야했다.

#### 2.2.1. 웹 사이트 구축
당시의 나는 Docker, Kubernetes, Framework, CI/CD, Git, OOP (Object-oriented programming) 등의 개발 환경이나 방법론 등에 친숙한 상황이 아니였고 (docker, k8s 등이 발표 되지 않은 시점이기도 하고...), 그러한 것들을 공부해 진행할 생각을 하기엔 경험치가 적고 주변 시야도 좁았기에, 학생 수준에서 알고있는 지식으로만 개발을 착수했었다.
이에, 프론트엔드는 HTML, Javascript (jQuery), CSS를 사용했고 백엔드는 CTF의 웹 문제에서 많이 접해 친숙하던 Apache, PHP, MySQL을 사용해 개발을 진행했다.
지금 코드를 보면 조악한 부분들과 보안적으로도 취약한 코드들도 많이 보이지만, 그 당시에는 그것이 무엇인지도 제대로 몰랐고, 그냥 재미로 시작한 프로젝트였기에, 그런것들은 크게 신경쓰지 않았던것 같다.

부끄럽지만, 아래는 문제를 풀고 나온 정답 코드를 (Flag; 플래그) 인증하는데 사용했던 `auth.php` 코드 일부이다.
SQL raw query를 사용하거나, 하드코딩된 플래그를 사용하고, 동일한 로직을 반복해서 작성해 기본적인 DRY (Don't Repeat Yourself) 원칙에 위배되는 등 개발, 보안적으로 부족한 점이 많은 코드였다. 😅

{% highlight php %}
<?php
...
function stest($a){
  $row = mysql_fetch_array(mysql_query("SELECT * FROM `score` where ..."));
  ...
}

...
if(stest('1') == '0'){ // Not Sovled
	if($auth == "{FLAG1_REDACTED}"){
		add('1');
		score('100');
		exit ('ok1');
	}
}

if(stest('2') == '0'){ // Not Sovled
	if($auth == "{FLAG2_REDACTED}"){
		add('2');
		score('100');
		exit ('ok2');
	}
}
...
?>
{% endhighlight %}

코드 퀄리티는 비록 낮았을지라도, 사이트의 기능이 온전히 동작하게끔 완성했던 기능들은 아래와 같았다.
- 계정 관련 기능 (회원가입, 로그인, 프로필, 계정 정보 변경, 보안 기능 - SQLi/XSS 방어, 비밀번호 해쉬, 솔트 등)
- 문제 관련 기능 (문제 목록, 문제 상세 보기, 정답 인증)
- 커뮤니티 기능 (댓글, 랭킹, SNS 공유)
- 기타 (히스토리, 캐러셀과 같은 FE 디테일)

코드 퀄리티가 낮아서였을까, 기능이 적어서였을까, 아마 둘 다 였겠지만 개발 기간은 학교를 다니면서도 1주일 정도로 오래걸리지 않았고, 이후 문제를 출제하고 사이트를 오픈하는데까지 2주 정도의 시간이 걸렸던것 같다. 아래는 이렇게 개발한 XCZ.KR의 메인 페이지 스크린샷이다.
![XCZ.KR 메인 페이지](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-mvp-index.png)

#### 2.2.2. 문제 출제
최초 문제 출제는 필자 외에도 XCZ.KR을 공식적으로 함께 런칭했고, 함께 공부 했던 친구가 (unlimit) 포렌식, 고전 암호, 네트워크 등 다양한 분야의 출제를 많이 도와주어 웹, 리버싱, 암호학, 네트워크, 포렌식, 모바일 등 다양한 분야에서 20개의 문제를 출제해 런칭을 진행할 수 있었다. 👍

이렇게 출제한 문제들은 사실, 엄청 어려운 수준은 아니였고 당시 청소년 해킹대회에 출제될 수준의 문제들이였는데, 그 당시에는 이런 문제들에 쉽게 접근할 수 있는 기회가 많이 없었기에, 입문으로 연습해보고자 하시는 분들이 많이 참여해주었던것 같다. 초기 문제들 중 하나를 (Prob 18: Web Basic) 예로 들어보면, 아래와 같이 난독화된 PHP코드를 해제 및 이해하고, 이에 알맞는 HTTP 요청을 서버에 보내어 플래그를 획득하는 문제도 있었다. (오해하지 않도록 첨언하면, 이러한 문제는 12년전 당시에도 쉬운 수준의 문제였다.)
> 풀이 방법 ➔ `Cookie: c=password, GET: g=giveme, POST: p=keyplz!` 파라미터를 가진 HTTP 요청을 만들어 서버에 보내면 플래그를 획득할 수 있다.

{% highlight php %}
<?php
$key = "Congratulations!</br>Key is ??????????????????";
$liillillilliliili = @$_COOKIE['c'];
$liillillliiiliili = @$_GET['g'];
$lilllillliiiliili = @$_POST['p'];
if(empty($liillillilliliili) || empty($liillillliiiliili) || empty($lilllillliiiliili)){exit ('wrong T.T');}
$lililillliiiliili = $liillillliiiliili . $liillillilliliili;
$lilililliiiiliill = $lilllillliiiliili;

function test($a){
$b = '';
    for($i=0; $i < strlen($a); $i++){
        $b = $b . ' - ' . ord(substr($a,$i,1));
    }
    return $b;
}
if(test($lililillliiiliili)==" - 103 - 105 - 118 - 101 - 109 - 101 - 112 - 97 - 115 - 115 - 119 - 111 - 114 - 100" || test($lilililliiiiliill) == " - 107 - 101 - 121 - 112 - 108 - 122 - 33"){
    echo $key;
}
else{
    echo 'wrong T.T';
}
?>
{% endhighlight %}

초기 20개 문제 외에도, 운영하면서 총 36개의 문제를 출제했는데 가장 쉬운 문제는 1,606명의 풀이자가, 가장 어려운 문제는 14명의 풀이자가 있었다.
가장 쉬운 문제는 (Prob 1: End Of Image) 헥스에디터로 파일을 열면 플래그가 보이는 단순한 문제였고, 가장 어려운 문제는 친구가 (setuid0) 찬조 형태로 출제해주었던 리버싱 문제였다 (Prob 31: Chaos). 아래는 전체 문제 목록 스크린샷이다.

![XCZ.KR 문제 목록](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-prob-list.png)

## 3. 운영 에피소드
사실 본 워게임 서비스의 메인 컨텐츠가 문제 풀이이기에, 이와 관련된 에피소드들이 많다. 그 중에서도 가장 기억에 남는 에피소드들을 일부 정리해보려고 한다.

### 3.1. XCZ.KR 오픈 1주년 이벤트
2013년 10월 21일, 서비스 오픈 1주년을 맞이하면서 해당 시기에 맞춰 문제를 출제하고 모든 문제를 가장 빠르게 풀이한 3명의 이용자에게 상품을 제공하는 이벤트를 진행했었다. 아래는 해당 이벤트를 홍보하기 위해 만들었던 배너이다. (PPT로 홍보물을 만들었는데, 그것과는 별개로 시상 내역이... 😇)
![XCZ.KR 1주년 이벤트 홍보 배너](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-event.png)

본 이벤트에서는 총 4 개의 문제를 출제했는데, 각 문제의 설명 및 간략한 풀이 방법은 다음과 같다:
- [Web] (Prob 33: IU): 주어진 PHP 파일의 암호화 로직을 분석해 관리자 세션을 만들어낸뒤, 블랙박스 환경에서 Broken Authentication 취약점으로 관리자 비밀번호를 변경해 플래그를 획득하는 문제이다.
- [Reversing] (Prob 34: Get the key, If You Can): .NET 으로 개발된 프로그램을 디컴파일하여, 로직에 맞는 해시를 브루트포싱해 플래그를 획득하는 문제이다.
- [Forensic] (Prob 35: ZIP): 임의로 깨트린 ZIP 파일을 스펙에 맞게끔 복구해 플래그를 획득하는 문제이다.
- [Forensic] (Prob 36: File Deleted): 윈도우 디스크 이미지 파일에서 의심 파일을 확인해 시간 및 볼륨 시리얼값을 획득하는 문제이다.

당시 기준 쉽게 풀리지 않도록 난이도를 조절해 냈던 문제들이었는데, 출제한지 하루도 지나지 않아 4문제를 모두 풀이한 1위 참여자가 (anhc0vy) 나타나 꽤나 놀라웠고, 이후 수시간 내로 2위 (jinmo123), 일주일 내로 3위 (zxcv22) 참여자까지 나타났다. 이렇게 이벤트를 성공적으로 마무리하나 했으나, 몇 가지 이슈로 인해 세 명 모두에게 상금 전달과정이 순탄치 않았다. 😅

- 1위 (anch0vy) 에게는, 개인적 이슈로 인해 상금을 바로 전달하지 못했고, 1개월 정도 늦게 문화상품권을 전달하게 되었다.
- 2위 (jinmo123) 에게는, 이벤트 얘기를 하던 중 의도치 않은 풀이법을 (XCZ.KR 사이트의 취약점 발견) 전달받아 얘기의 주제가 그쪽으로 새게 되었다. 이건 두 번째 에피소드에서 더 자세히 다루도록 하겠다.
- 3위 (zxcv22) 에게는, 아직도 연락이 닿지 않아 상품 전달을 하지 못했다. (혹시나, 이 글을 보고계시는 zxcv22님께서는 당시 계정에 등록 하셨던 이메일을 저에게 인증해주시면 지금이라도...)

결과적으로, 규모도 작고 미흡한점도 많았던 이벤트에 이슈도 여럿 있었지만, 나름 개최부터 마무리까지 완수했던 최초이자 마지막 XCZ.KR 행사였기에 기억이 남기도하고, 관심을 가지고 참여해주신 모든 분들께 다시 한번 감사의 인사를 전하고 싶어 본 에피소드를 소개했다. ([Link. 이벤트 상위 30명 목록](https://web.archive.org/web/20241006211138/http://xcz.kr/START/event.php))

### 3.2. XCZ.KR 사이트 SQL Injection 해킹 사건
앞서 사이트 개발에서 언급했던 것처럼, XCZ.KR은 성숙하게 개발된 코드도 아니였으며 보안적으로 취약한 코드들이 많았고, 이러한 근본적인 이유로 하여금 장기적인 관점에서 문제가 발생할 소지가 충분히 있었다. 이처럼 잠재적인 문제점을 방치하던 와중에, 1주년 이벤트의 웹 문제를 (Prob 33: IU) 출제하게 되었고, 참여자가 (jinmo123) 문제를 풀이하는 과정에서 의도치 않았던 SQL injection 취약점을 발견해 제보해주었던 에피소드이다.

먼저, 해킹 사건의 발단이 되었던, 33번 웹 문제는 아래와 같은 IU 팬사이트를 해킹하는것을 목표로하는 문제였다.
![XCZ.KR Prob 33\: IU 문제](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-prob-33.png)

본 문제는 아래와 같이 주어진 암호 로직을 분석하면, 유저가 아이디를 (UID; UserID) 임의로 조작할 수 있는 형태로 설계가 되어있었다.
{% highlight php %}
<?php
function DecryptUID($a){
	srand();
	$s_vector_iv = mcrypt_create_iv(mcrypt_get_iv_size(MCRYPT_3DES, MCRYPT_MODE_ECB), MCRYPT_RAND);
	$s_key = "RED_SHOES_".ip2long($_SERVER['REMOTE_ADDR']);
	$de_str = pack("H*", $a);
	$out_str = mcrypt_decrypt(MCRYPT_3DES, $s_key, $de_str, MCRYPT_MODE_ECB, $s_vector_iv);
	$CArray = explode("_",$out_str);
	for($i = 0; $i < sizeof($CArray)-1; $i++){@$b .= chr($CArray[$i]^ (sizeof($CArray)-1) ^ 516);}
	return @$b;
}
?>
{% endhighlight %}

다만, 유저 계정을 단순히 ADMIN 으로 인식되게끔하여 풀이하는 것이 아니라, ADMIN으로 쿠키를 변경한 이후 관리자 페이지에 들어가 비밀번호를 재검증해야만 플래그를 획득할 수 있었다. 즉, UserID를 단순히 ADMIN으로 조작하는것 외에도, 비밀번호를 변경할 수 있는 다른 취약점을 연계하여 풀이하는 것이 원래 의도한 풀이이다. 이러한 상황에서, 어떻게 취약점이 발생할지 상상이 되는가? 다음 컨텍스트들을 추가적으로 놓고 고려해보면 충분히 SQL injection 취약점이 발생함을 예상할 수 있다.
- XCZ.KR 서비스는 raw query를 활용하고 있다.
- PHP 구버전에서는 magic_quotes_gpc 보호기법을 활성화시켜 서버에 들어온 GET, POST, COOKIE 파라미터 중 `'` 문자열을 `\'`로 이스케이프 처리해 SQL injection을 완화하고있다. (그렇기 때문에, 굳이 추가적인 필터링을 진행할 필요가 없다. 오히려 중복으로 이스케이프 처리를 진행할 경우, 잘못된 로직 혹은 멀티바이트 버그로 인해 SQL injection 취약점이 발생할 가능성이 추가로 생기곤했다.)
- 유저의 입력값으로부터 UserID를 받아와서, 이를 DecryptUID 함수를 통해 복호화한 뒤 SQL query에 사용하고 있다. (즉, 이 때 유저의 입력값은 평문이 아닌 암호화된 값이다. 따라서, magic_quotes_gpc에 영향을 받지 않는다.)

아래는 본 문제에서, 로그인 여부를 확인하기 위해 사용된 로직이며 여기서 `DecryptUID()` 함수의 반환값을 조작해 SQL injection 취약점을 트리거시킬 수 있었다.
{% highlight php %}
<?php
...
if(@$_COOKIE['userid'] != ""){
  $query = "SELECT * FROM `member` where user_id='". DecryptUID(@$_COOKIE['userid']) . "'";
  $row = mysql_fetch_array(mysql_query($query));
  if($row['user_id'] == "") { exit("No Hack!"); }
}
...
?>
{% endhighlight %}

이미 SQL injection이 발생한것만으로도 의도하지 풀이를 (문제 페이지 내 관리자 비밀번호를 획득) 할 수 있는 상황이 발생했는데, 여기서 상황이 더 안좋아질 수 있을까? 놀랍게도 다음과 같은 배경들도 함께 있었다 😭: 
- XCZ.KR 서비스는 문제 서버와 메인서버를 분리하는 아키텍쳐링이 되어있지 않았고 (데이터베이스 서버 포함), DB 계정을 분리하는 것조차 이뤄지고 있지 않다.
- SELECT 구문 말고도, UPDATE 혹은 DELETE 등의 쿼리를 실행하는 모든 부분에서 위와 같은 형태로 취약한 코드 패턴을 사용하고 있었다. (즉, DB 수정/삭제 등도 가능한 상태였다는 뜻이다.)

이처럼 지금 생각해보면 말도 안되게 심각한 상황이였는데, 취약점 제보 덕분에 빠르게 조치를 취할 수 있게 되어 큰 사고로 이어지지 않았다.
결과적으로, 이 사건은 내게있어 취약점 대응의 중요성, 잠재적인 위협의 제거, 그리고 이 뿐만아니라 기술적인 교훈 등 많은것들을 배우게해준 귀중한 경험이였다. 
마지막으로, 본 에피소드를 마무리하기전 취약점 제보를 통해 서비스를 안전하게 운영할 수 있도록 도와준 jinmo123님께 다시 한번 감사의 인사를 전하고 싶다. 😊

### 3.3. 포렌식 문제 플래그 변경 사건
문제를 출제해두고 시간이 지나다보면, 기술적인 이슈로 인해 문제에 이슈가 생기는 경우가 간혹 있다. 예를 들면, XSS 공격을 해야하는 문제에서 크로미움 기반의 봇이 동작하는 경우, 크롬 브라우저 자체 1-day 취약점으로 풀이자가 서버 쉘권한을 획득할 수 있게 된다거나, 문제 환경 구성에서 사용하는 라이브러리에서 취약점이 발견되어 의도치 않은 형태로 플래그를 획득할 수 있는 등의 상황이다. (이러한 경우, 문제의 플래그가 변경되는 것이 아니라 의도하지 않은 풀이가 추가되는 것 뿐이기에, 문제 셋팅에서 기술적으로 업데이트 조치를 취해 원래 의도대로 만들어주면 된다.) 하지만, 이번 에피소드에서는 기술적 이슈가 아닌, 현실 세계의 이슈로 인해 문제의 플래그가 변경되는 사건이 발생했기에 이를 소개해보고자 한다.

먼저, 본 에피소드의 발단이 되었던 "Prob 22: Who's Notebook?" 문제의 설명은 아래와 같다.
> 내 친구 A는 어느날 출근길에 누군가 잃어버린 것 같은 노트북을 발견한다.
> A는 이 노트북을 주인에게 찾아주고 싶지만 찾을 방법을 몰라서 포렌서인 나에게 노트북을 맡기게된다.
> 이 노트북의 주인을 찾아주자.  
> &nbsp;  
> 인증키 형식 : 출발지_거쳐가는곳(1곳)_최종도착지  
> 인증키는 모두 대문자로, 띄어쓰기무시  
> 예) PLACE1_PLACE2_PLACE3

문제 파일을 다운로드 받게되면, FTK Imager에서 열 수 있는 랩탑의 디스크 덤프 (AD1; AD segmented file) 파일이 주어지는데, 해당 파일을 열어보면 바탕화면에 GPX 파일이 (GPS exchange format; GPS 위도/경도를 저장하는 XML 형태의 파일 포맷) 존재함을 확인할 수 있다. 이 GPX 파일을 열어보면, 노트북을 잃어버린 사람이 여행한 경로가 기록되어있는데, 이를 통해 출발지, 거쳐가는 곳, 최종 도착지를 알아내어 인증키를 만들어내면 플래그를 획득할 수 있는 문제이다. 아래는 해당 파일의 스크린샷이다.
![XCZ.KR Prob 22\: Who's Notebook? 문제](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-prob-22.png)

파일을 확인해보면 출발지, 경유지, 도착지 각각 좌표가 다음과 같음을 확인할 수 있다:
- 출발지: [37.5437947027528, 126.950969696045](https://maps.app.goo.gl/TC2nq19a1BM4Mc9x5)
- 경유지: [37.5595475633873, 126.803297996521](https://maps.app.goo.gl/UaZnizcqsqRMPBhRA)
- 도착지: [33.5051616442472, 126.496796607971](https://maps.app.goo.gl/SZH7DKAVDzWTPPP97)

이에 GPS 경로를 따라 출발지로는 공덕역, 경유지로는 김포국제공항, 그리고 마지막 도착지를 입력해 인증키를 만들면 된다. 하지만, 문제는 여기서 발생했다.
보통 지하철 역, 공항과 같은 공공 인프라 시설의 경우 위치가 변경되거나 이름이 변경되는 경우가 흔치 않다. 다만, 마지막 도착지는 공공 인프라 시설이 아니라 개인 사업장이였는데, 본 문제를 출제함에 있어 개인 사업장의 폐업 및 이전이 빈번하게 발생함을 고려하지 못해 많은 풀이자들이 문제를 풀었음에도 불구하고, 인증이 되지 않는 상황을 발생시켜버렸다.

이에 이슈가 있었던 분들의 많은 문의가 들어왔는데, 그 때서야 상황을 인지하고 기존의 도착지였던 "세븐일레븐"이 폐업함에 따라, 뒤편에 자리잡고있던 (거리상 3m 이내) "샤프렌터카" 사업장을 복수 정답으로 변경하고, 문의가 들어올때마다 상황을 설명드리고 수동으로 인증하는 등의 방식으로 운영을 하게 되었다. 😭 (본 문제로 하여금 곤혹을 겪은 모든 분들에게 다시 한번 사과의 말씀을 드리고 싶습니다... 🙏🙏🙏) 결론적으로, 이는 문제 출제 당시 상황 및 기술적 이슈를 고려해야하는 점도 있지만, 장기적인 유지보수 측면에서 고려해야할 점도 많다는 것을 다시 한번 깨닫게 해준 에피소드였다.

그리고, 번외로 이번 기회에 네이버, 카카오 등 국내 지도 서비스의 거리뷰를 통해 2012년부터 지금까지의 도착지 좌표의 사업장 정보를 확인해 보았는데, 12년간 무려 4번이나 사업장 정보가 바뀌었음을 확인했다. 아래는 기간별 사업장 정보를 편집한 내용이다. (참고로, 본 문제의 출제일은 2012년 10월 23일이며, 현재는 복수 정답이였던 렌터카 업체도 사라진 상태로 보인다.)
- 2011년 ~ 2013년: 세븐일레븐
- 2013년 ~ 2018년: 유모자 대여 사업 (사업장 A)
- 2018년 ~ 2018년: 수 개월 폐업 시기
- 2018년 ~ 2024년: 유모차 대여 사업 (사업장 B)
![XCZ.KR Prob 22\: Who's Notebook? 문제 사업장 정보](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-prob-22-roadview.png)

### 3.4. 에피소드를 마치며
이렇게 XCZ.KR을 운영하면서 겪었던 에피소드들을 정리해보았는데, 사실 이 에피소드들 외에도, 사이트 운영중 인입된 문의사항, SNS 운영 기록, 홍보물 제작, 수 년 뒤 사회생활을 하며 만난 이들이 본 사이트를 이용하며 나에게 전해준 이용후기들, 직장 동료가 (juno) 지금와서 더 쉽고 기막힌 방법으로 문제를 풀이하는 모습을 구경하는 등 재밌고 고마웠던 얘기들도 많은데, 이는 기억에 소중히 남겨 두려고 한다.

결과적으로, 나 스스로는 XCZ.KR을 통해 개발, 운영, 보안 등 다양한 분야에서 배운 점들이 많았고, 이를 통해 성장할 수 있었던 것 같다. 혹여나, 이처럼 서비스 개발이나 운영, 새로운 경험을 망설이는 분들이 있다면 이러한 에피소드 공유로 하여금 간접적으로 조금이나마라도 되면 좋겠다. 😊

## 4. 자주 받았던 질문
사이트를 운영하면서, 그리고 종료를 주변인들에게 얘기하며 받았던 질문들을 정리해보고자 한다.

1. 사이트 이름이 왜 XCZ.KR 인지?
- 사이트 정체성과는 관련이 없다. 사이트 이름은 단순해야 한다고 생각했기에, 키보드 왼쪽 아래에 위치해 쉽게 입력할 수 있는 XCZ와 당시 가장 짧고 많이 사용했던 KR TLD (Top level domain)를 조합해 XCZ.KR로 결정했다.
2. 사이트 운영에 따른 수익금이 있었는지?
- 없다. 광고도 달지 않았고, 홍보 요청이나 개인적 연락 등이 온 것들은 있었지만 진행하지 않았다. 오히려, 서버 컴퓨팅 운영 비용만 꾸준히 지출하고 있었다.
3. 사이트 운영을 종료하더라도, 문제를 계속 풀어볼 수 있도록 할 것인지?
- 계획이 없다. 출제했던 문제들이 오래된 만큼 현재 기술과 달라지거나 사장된 부분도 많고, 현 시점 CTF 혹은 워게임 출제 경향과 다른 부분이 많기 때문이다. (현재 필자가 소속된 Theori에서 운영중인 [드림핵](https://dreamhack.io/) 사이트에 필자가 제작한 교안이나, 실습 문제들도 있고, 더욱이 최신 기술들을 반영한 좋은 문제들이 많으니 드림핵에서 학습하는 것을 추천한다.)
4. 운영을 종료하게 된 이유는?
- 본 서비스는 교육의 (실습 컨텐츠 풀이) 목적이 가장 크다고 생각하는데, 기술 동향이 자주 바뀌고 생겨나는 보안이라는 분야에서 질 좋은 교육 컨텐츠를 지속적으로 제공하지 못하는 서비스를 유지하는것이 크게 학습자들에게 도움이 되기 어렵다고 판단했다. 오히려, 이를 대체할 수 있는 좋은 서비스들이 많이 생겨났기에 학습자들이 그러한 서비스로 이동해 더 폭발적인 성장을 하기를 바란다. 또한, (이미 사용자들이 많이 넘어갔기에) 본 사이트 트래픽이 꾸준히 있긴 하더라도 예전만큼 나오지 않는것을 감안해 현재 이용자들도 이동하기를 바라며 서비스를 종료하고자 한다.
5. 서비스 이용자는 얼마나 있었는지?
- 총 6,000 명 이상의 가입자가 있었다. 다만, 가입절차가 까다롭지 않았기에 허위계정도 많았을거라고 생각한다. 매 월 수 십명의 신규 유저가 가입하고, 수 십명의 이용자들이 문제를 풀고 인증했으며, 꾸준히 메일이나 메신저를 통해 문의도 들어왔다. 누군가는 본 서비스를 활용해 계속 배우고 성장하고 있으며, 도움이되고 있었다는 것으로 이해했고, 이것이 내가 유지보수는 하지 못하는 상황에서도 운영 종료를 미루게된 가장 큰 이유이다.

## 5. 결론
2012년 10월 12일부터 12년간 운영해온 워게임 사이트 XCZ.KR의 서비스를 2024년 11월 15일부로 종료하려 한다.
어린 나이에 시작한 서비스였기에 미숙한 점도 많고 유치한 부분도 있었지만, 그래도 오랜 시간 이렇게 운영해온 것은 나름대로 큰 경험이었고, 이를 통해 기술적 성장뿐 아니라 다양한 경험들을 할 수 있었다. 한편, 운영과 관련해서는 유지보수를 중단한 지 오래되었기 때문에, 질 좋은 교육 콘텐츠를 제공이라는 목적을 꾸준히 실현하지 못해 아쉬움이 남는다. 그럼에도 불구하고, 매 순간 새롭게 또, 꾸준히 관심을 가져주고 찾아와준 이용자들이 있었기에 그들에게 조금이나마 도움이 될 수 있었던 점은 서비스를 유지해왔던 나름의 의미를 만들어준것 같다.

XCZ.KR을 꾸준히 사용하고 있던 이용자분들은, 앞으로도 XCZ.KR이 아닌 [드림핵](https://dreamhack.io/)을 통해 공부해 폭발적인 성장을 이루셨으면 좋겠고, 서비스 개발/운영 혹은 새로운 도전을 망설이시는 분들께도 이 글이 조금이나마 도움이 되면 좋겠다.

마지막으로, 런칭 및 운영을 함께해준 (unlimit), 문제 출제 (setuid0, win32virus), 이벤트 적극 참여 (anch0vy, jinmo123, zxcv22), 취약점 제보 (jinmo123), 그리고 사이트 [랭킹](https://web.archive.org/web/20240423075141/http://xcz.kr/START/rank.php)에 등록될 정도로 적극적으로 참여해주신 분들을 포함해, 한번이라도 관심을 가져주신 모든 이용자 분들께도 다시 한번 심심한 감사의 말씀을 전하고싶다.

XCZ.KR 오픈 당시 친구에게 (PorcaM) 받았던 축전 이미지로 마무리하겠습니다 ㅎㅎ...  
다시 한번 감사드립니다. 😊
![XCZ.KR 오픈 축전](/assets/img/posts_img/2024/wargame-xcz-kr-shutdown-operational-history-and-episodes/xcz-kr-slider-img.jpg)