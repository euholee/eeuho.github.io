---
layout: post
title: "위메프 고객센터 챗봇 프로토타입(위메프 지원용)"
data: 2019-10-18
categories: etc
---


<iframe width="100%" height="auto" src="/data/wemep_chatbot_demo.html" frameborder="0"></iframe>

위메프의 서비스 중 제 역량으로 개선해볼 수 있을 만한 부분을 찾아보던 중 자주 묻는 질문(FAQ)과 1:1 문의가 눈에 들어왔습니다. 모든 문의를 사람이 1:1로 대응하는 것도 고객 입장에서 굉장한 만족도를 느낄 수 있으나, 위메프는 이용자가 많은 서비스인 만큼 챗봇을 활용하면 CS 인력 리소스를 좀더 효율적으로 활용하면서도 고객 대응시간을 줄일 수 있지 않을까 생각했고, 다른 지원자와 차별된 모습을 모여드리고 싶어 위메프의 FAQ 내용들을 트레이닝셋으로 활용하여 직접 챗봇 프로토타입을 작성하였습니다.

---

## 1. 기술 조사

이미 분명 챗봇을 트레이닝하고 서비스할 수 있도록 플랫폼을 제공해주는 서비스가 있을 것이라고 생각했고, 쉽게 Google의 'Dialog Flow'라는 서비스를 찾았습니다.
데이터셋만 있다면 누구나 손쉽게 챗봇을 만들고 트레이닝하여 서비스할 수 있고, 적은 데이터셋으로도 프로토타입 정도는 가능하다는 여러 포스트들을 읽고 챗봇 플랫폼으로 'Dialog Flow'를 선정하였습니다.

---

## 2. raw dataset 얻기

'위메프 파트너'에서 개발자용 api가 제공하고 있는 것 같았지만, 1:1 문의 내용 데이터를 제공하는지는 알 수 없었고 해당 토큰을 얻기 위해 허가도 받아야하기에 시간이 부족하여, 데이터셋은 특별한 토큰 등의 인증 없이 데이터를 얻을 수 있는 위메프 고객센터의 자주 묻는 질문(FAQ) 데이터를 이용했습니다.

![Screenshot from 2019-10-18 04-03-42](https://user-images.githubusercontent.com/54487021/67039647-b657bf80-f15c-11e9-9502-2b4c0befde9a.png)

크롬 개발자 도구에서 해당 api를 확인했고, html 파일이 리스폰스로 오는 것을 확인했습니다. (리스폰스로 html 파일이 올 줄은 몰랐습니다.)

![Screenshot from 2019-10-18 04-08-57](https://user-images.githubusercontent.com/54487021/67039894-4a298b80-f15d-11e9-8abe-842583aec1d1.png)

```
$ curl http://www.wemakeprice.com/customer_center_faq/list_tab/0 >> rawdata
```

우선 curl 명령어를 통해 해당 리스폰스를 저장하여 raw dataset을 얻었습니다.

---

## 3. 데이터 전처리

![Screenshot from 2019-10-18 04-17-01](https://user-images.githubusercontent.com/54487021/67040291-287cd400-f15e-11e9-9660-0f34f4cd44a7.png)

html 파일에서 'faq_bottom_n'의 id를 가진 div에 질문이 들어가고, 'msg'의 class를 가진 div에 답변이 들어가는 것을 확인했고, 그 두 가지가 들어간 소스만 추출하였습니다.

![Screenshot from 2019-10-18 04-20-54](https://user-images.githubusercontent.com/54487021/67040592-c2dd1780-f15e-11e9-9fa1-b6aad6895fc2.png)
```
$ grep -E 'faq_bottom_|msg' rawdata >> parsed_data
```

이후 제가 주로 사용하는 우분투의 gedit을 활용하여 아래와 같은 training set을 얻었습니다.

![Screenshot from 2019-10-15 17-11-19](https://user-images.githubusercontent.com/54487021/67040845-4a2a8b00-f15f-11e9-913d-e6cf2d944486.png)

---

## 4. 챗봇 생성

Dialog Flow의 계정을 생성한 후, 아래와 같이 챗봇 프로젝트를 생성했습니다.

![Screenshot from 2019-10-16 16-26-19](https://user-images.githubusercontent.com/54487021/67041182-0d12c880-f160-11e9-9525-37aa34d3c01d.png)

---

## 5. 처리된 데이터로 챗봇 학습

다큐먼트에 따르면 챗봇은 에이전트(자연어를 이해하는 모듈)와 인텐트(학습 문구, 작업 및 매개변수, 응답)로 이루어집니다.
챗봇을 생성하면서 에이전트를 생성하였으니 이제 인텐트만 정해주면 됩니다. 그런데 총 받은 데이터셋이 356개로 일일이 모두 학습 문과, 작업 및 매개변수, 응답을 정의하기엔 시간이 부족했습니다. 과감히 학습 문구(FAQ의 Question)와 응답(FAQ의 Answer)만을 지정하고, Dialog Flow의 rest-api를 이용하여 챗봇을 학습시켰습니다.

우선 수동으로 하나만 실행하여 샘플 curl 명령어를 얻은 후,

![Screenshot from 2019-10-18 04-37-28](https://user-images.githubusercontent.com/54487021/67041638-06388580-f161-11e9-91d2-aa9857358545.png)

Dialog Flow에서 제공하는 API 다큐먼트와 금방 얻은 샘플을 이용하여 curl 명령어를 작성하여 테스트해보고, 트레이닝 셋에서 html 태그를 제거하고 Dialog Flow restapi를 통해 챗봇의 인텐트를 생성하는 리눅스 쉘 스크립트를 작성했습니다.

```
question[1]="고객센터에서 전화상으로 본인 확인 후 대리 결제가 가능한가요?"
answer[1]="<div class='msg'><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px font-weight: bold color: rgb(255, 0, 0)'>√ 텔레마트 고객센터를 통해 구매(결제) 대행 가능</span></div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'><br></div><span style='font-family: 돋움, dotum, sans-serif font-size: 12px'>ㆍ전화주문 이용 서비스 약관 및 개인정보수집 동의한 고객님께서는 위메프 내 전 상품 서비스 이용 가능합니다.</span><div><span style='font-family: 돋움, dotum, sans-serif font-size: 12px'>&nbsp (단, 컬처/여행, 롯데백화점 등 무통장입금 제외딜은 불가)</span><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'>ㆍ무통장 입금 결제수단만 가능합니다.</div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'>ㆍ텔레마트 고객센터 : 1644-4555 | 평일 09시~18시 (점심시간 13시~14시)</div></div> <span class='etc_msg'>답변이 충분하지 않으셨다면, 1:1문의를 이용해 주세요. <a href='/mypage/qna'><strong>1:1문의하기></strong></a></span></div>"

question[2]="내가 게시한 공개형 게시물을 삭제하려면 어떻게 해야 하나요? [자기게시물 접근배제 요청]"
answer[2]="<div class='msg'><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-height: 16px color: rgb(255, 0, 0
) font-weight: bold'>√ 자기게시물 접근 배제 요청 가능</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px li
ne-height: 16px color: rgb(255, 0, 0) font-weight: bold'><br></span></p><p class='MsoNormal'>방송통신위원회의 [인터넷 자기 게시물 접근 배제 요청권 가이드
라인]에 따라&nbsp</p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-height: 16px'>이용자 본인이 인터넷 상에
게시한 게시물에 대한 타인의 접근 배제를 요청하실 수 있습니다.</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size:
 12px line-height: 16px'><br></span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-height: 16px'>공개형
게시물(예: Q&ampA, 광고에 활용된 게시물) 삭제에 대하여 안내 드립니다.</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif fo
nt-size: 12px line-height: 16px'><br></span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-height: 16px
font-weight: bold'>Q&ampA 문의 사항</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-height: 16px'>
마이페이지 &gt 상품Q&ampA &gt 게시글 선택 &gt 삭제를 통해 직접 삭제 가능</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif
 font-size: 12px line-height: 16px'><br></span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-height: 16
px font-weight: bold'>광고에 활용된 본인 게시물</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-hei
ght: 16px'>본인 게시물 확인 후 삭제 신청 가능(위메프 회원 여부 확인)</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif fon
t-size: 12px line-height: 16px'><br></span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12px line-height: 16px f
ont-weight: bold'>회원 탈퇴 건 및 본인확인 불가 건의 경우</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif font-size: 12p
x line-height: 16px'>1:1게시판 문의 후, 별도 안내에 따라 삭제 처리 가능</span></p><p class='MsoNormal'><span style='font-family: 돋움, dotum, sans-serif
font-size: 12px line-height: 16px'>본인이 아닌 대리자의 경우 대리인 지정서 추가 접수 필요</span></p><p class='MsoNormal'><span style='font-family: 돋움,
dotum, sans-serif font-size: 12px line-height: 16px'><br></span></p><p class='MsoNormal'>직접 삭제가 어려운 경우 &nbsp1:1문의로 접수해 주시면 빠르게 처리
 도움 드리도록 하겠습니다.</p> <span class='etc_msg'>답변이 충분하지 않으셨다면, 1:1문의를 이용해 주세요. <a href='/mypage/qna'><strong>1:1문의하기></str
ong></a></span></div>"

...

question[356]="주문하면 배송기간이 얼마나 걸리나요?"
answer[356]="<div class='msg'><div><span style='font-family: 돋움, dotum, sans-serif font-size: 12px font-weight: bold color: rgb(255, 0, 0)'>√ 상품페이
지 &gt 상품별 출고 일정 확인</span></div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'><br></div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'>ㆍ구매 상품 별로 출고일정이 상이하기 때문에, 해당 상품페이지 내 기재된 상품 출고일정을 참고해주시면 됩니다.</div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'>ㆍ통상적으로 상품 출고 후 영업일기준 1~2일이내 상품을 받아보실 수 있습니다.&nbsp</div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'>&nbsp &nbsp(단, 택배사 사정에 따라 1~2일정도 추가로 지연될 수 있습니다.)</div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'>ㆍ가구, 대형가전 등 부피가 큰 상품은 업체에서 직접 배송예정일을 입력하고 있습니다.&nbsp</div><div style='font-family: 돋움, dotum, sans-serif font-size: 12px'>ㆍ자세한 배송일정은 상품 Q&ampA게시판으로 문의해주시면 판매업체에서 답변을 드리고 있습니다.</div> <span class='etc_msg'>답변이 충분하지 않으셨다면, 1:1문의를 이용해 주세요. <a href='/mypage/qna'><strong>1:1문의하기></strong></a></span></div>"
```

```
for i in {1..356}
do
	echo 'sending intent test'${i}':' ${question[i]}
	n='faq'${i}
	q=${question[i]}
	a=$(echo ${answer[i]} | html2text -utf8)
	curl 'https://api.dialogflow.com/api/intents?lang=ko' -H 'Accept: application/json, text/plain, */*' -H 'X-GOOG-ACCESS-TOKEN: '${access_token} -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Safari/537.36' -H 'Authorization: Bearer '${dev_token} -H 'X-GOOG-ID-TOKEN: '${id_token} -H 'Sec-Fetch-Mode: cors' -H 'Content-Type: application/json;charset=UTF-8' --data-binary '{"name":"'${n}'","auto":true,"contexts":[],"templates":[],"responses":[{"parameters":[],"resetContexts":false,"affectedContexts":[],"messages":[{"type":0,"speech":"'"${a}"'"}],"speech":[],"defaultResponsePlatforms":{}}],"conditionalFollowupEvents":[],"source":null,"priority":500000,"cortanaCommand":{"navigateOrService":"NAVIGATE","target":""},"events":[],"followupEvent":null,"endInteraction":true,"userSays":[{"isTemplate":false,"data":[{"text":"'"${q}"'"}],"count":0,"id":null,"updated":null}]}'
done
```

위 스크립트를 실행한 터미널 화면입니다.

![Screenshot from 2019-10-18 03-15-16](https://user-images.githubusercontent.com/54487021/67159222-d82f8d00-f37c-11e9-87e5-1b21040a53b8.png)

---

## 6. 테스트 화면

(이 포스트 맨 위에서 직접 테스트하실 수 있습니다.) <button onclick="document.body.scrollTop = 0; document.documentElement.scrollTop = 0;"> 맨 위로 가기 </button>

![Screenshot from 2019-10-18 03-36-00](https://user-images.githubusercontent.com/54487021/67041958-c1f9b500-f161-11e9-9c08-41e28ac6c664.png)
![Screenshot from 2019-10-18 03-36-22](https://user-images.githubusercontent.com/54487021/67041993-d63db200-f161-11e9-8372-79b82ff03b62.png)
![Screenshot from 2019-10-18 03-37-21](https://user-images.githubusercontent.com/54487021/67042101-1bfa7a80-f162-11e9-953b-2e7f055fd89f.png)
![Screenshot from 2019-10-18 03-37-37](https://user-images.githubusercontent.com/54487021/67041960-c1f9b500-f161-11e9-8d9d-230a8119da10.png)

---

## 7. 결론

이 프로토타입은 저의 AI를 활용한 개발 능력 등을 보여드리려는 것이 아닌, 백엔드 엔지니어로서 저의 개발에 대한 관심을 보다 객관적으로 보여드리고 싶었고, 리눅스 커맨드 친숙도나 쉘 스크립트 작성 등을 보여드리고 싶어서 제작되었습니다. 하지만 급하게 작성하다보니 API 문서를 충분히 참고하지 못했고, 챗봇에 대한 깊은 이해 없이 프로토타입을 작성하여 살짝 아쉬운 느낌이 있습니다.

이 프로토타입을 제작하면서 어려웠던 점은 의외로 raw data를 전처리과정이었습니다. 의외로 적절한 커맨드를 찾는데 시간이 많이 소요되었습니다. 프로토타입 제작에는 문서 작성을 제외하고 약 8시간 정도 소요되었고, 챗봇이 무엇인지 배우면서 실제로 써볼 수 있었던 기회였던것 같습니다.

---

## 8. 개선 가능 사항

제 예상보다 챗봇이 똑똑했지만, 시간 문제로 인텐트마다 작업 및 매개변수를 설정하지 못했습니다. 또 트레이닝셋의 길이가 너무 적었습니다. 만약 작업 및 매개 변수를 설정해주고, 실제 1:1 문의 데이터를 통해 트레이닝한다면 보다 똑똑한 챗봇의 성능을 기대할 수 있을 것 같습니다.

감사합니다.
