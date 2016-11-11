---
layout: post
title: "텍스트 & 하이퍼링크 관련 HTML 태그"
#description: "html-css"
date: 2012-05-22
tags: [html]
comments: true
share: true
---

# 텍스트 & 하이퍼링크 관련 태그

> [Do it! HTML5+CSS3 웹표준의 정석](http://book.naver.com/bookdb/book_detail.nhn?bid=7309491) (고경희 저. 이지스퍼블리싱. 2013) 을 공부하며 책 내용 중 일부를 요약.

## 텍스트를 묶어서 처리하는 태그들
``` html
<p>Text</p>
```
- 텍스트를 표시할 때 주로 사용하는 태그
- `block`태그 (앞, 뒤 줄바꿈 발생)

``` html
<blockquote cite="http://google.com">인용</blockquote>
<q cite="http://google.com">따옴표인용</q>
```
- `<blockquote>` 태그는 `block`태그. 좌우 `margin`추가
- `<q>` 태그는 `inline`태그. 좌우 `따옴표`추가
- `cite` 속성을 통해 인용 사이트 주소 기록

``` html
<pre>공      백</pre>
```
- `html`문서에서 작성한 그대로를 브라우저에 표시. 다른 태그의 경우 연속해서 띄워쓰기를 하여도 한 개의 공백만 표시


## 텍스트 관련 태그들
``` html
여기가 가장 <mark>중요</mark> 합니다.
```
- 형광펜 효과(HTML5부터 포함)
- 기본은 `background-color: yellow` 이지만, css를 통해 변경

``` html
<time datetime="2016-12-25">올해 크리스마스</time>에 나는 나홀로집에 를 다시 보겠습니다.
```
- 웹 문서에 직접적으로 표시되는 날짜와 시간이 아닌 기계나 도구를 위한 날짜시간정보

|  속성  |      설명       |
| :--: | :-----------: |
| YYYY |      연도       |
|  MM  |   월(01~12)    |
|  DD  |   일(01~31)    |
|  T   |  시간과 날짜 구분자   |
|  hh  | 24시간제로 표시한 시간 |
|  mm  |       분       |
|  ss  |       초       |

``` html
<strong>의미상 진하게(강조)</strong>
<b>그냥 진하게</b>
<em>의미상 기울임(강조)</em>
<i>그냥 기울임</i>
<dfn>용어 정의 기울임</dfn>
```
- 의미나 문맥상 강조하는 내용(브라우저가 강조의 의미로 인식해야 하는)은 `<strong>`과 `<em>` 태그 사용.
- 뜻없이 굵게나 기울임은 `<b>`와 `<i>` 태그를 사용.

``` html
<p>텍스트 단락 중<span style="color:blue">일부분</span>만 특정 스타일을 적용하려고 할 때</p>
```
- 태그 자체로는 아무 의미 없음. 텍스트 단락 안에서 줄바꿈 없이 일부 텍스트에만 스타일을 적용하려할 때 사용
- `<font>` 태그는 표준에서 제외되었음

## 목록 관련 태그들
``` html
<ul>
	<li>List Item-1</li>
	<li>List Item-2</li>
	<li>List Item-3</li>		
</ul>
```
- 순서 없는 목록 (unorded-list)
- 각 리스트 아이템`(li)` 앞에 블릿이 붙음. `list-style-type` 속성으로 변경 가능. (HTML5부터는 css로 변경가능) `circle`, `disc`, `square`, `none`

``` html
<ol>
	<li>List Item-1</li>
	<li>List Item-2</li>
	<li>List Item-3</li>		
</ol>
```
- 순서 있는 목록 (orded-list)
- 각 리스트 아이템`(li)` 앞에 숫자가 붙음. `type` 속성으로 변경 가능. `1`, `a`, `A`, `i`, `I`
- `start` 속성으로 중간 번호부터 시작 가능. `reversed` 속성으로 번호를 역순으로 매기기 가능

``` html
<dl>
	<dt>용어(term)</dt>
	<dd>설명(description)</dd>
</dl>
```
- 정의 목록 (definition-list)
- 제목과 그에 대한 설명으로 이루어지는 정의 목록

> 참고하면 좋은 자료: [목록(ul ol dl)의 올바른 활용 문제] (http://egloos.zum.com/njpaiks/v/2407349)

## 표 관련 태그들
웹표준, 웹접근성 관련하여 `<table>` 태그가 완전히 배척 되고, `<div>`로 대체되는 추세. 그 원인은 [웹표준-웹접근성을-고려한-HTML-코딩-작업시-규칙](http://ji80903.tistory.com/entry/웹표준-웹접근성을-고려한-HTML-코딩-작업시-규칙)을 참고. 하지만 표를 그릴 때는 `<table>` 태그를 올바르게 사용하는게 맞음. (같이 보면 좋은 글 : [지나친 테이블 배제](https://hyeonseok.com/soojung/webstandards/2009/11/12/554.html))

``` html
<table>
    <caption>테이블 제목</caption>
    <thead>
        <tr>
            <th>제목1</th>
            <th>제목2</th>
            <th>제목3</th>
            <th>제목4</th>
        </tr>
    </thead>
    <tfoot>
        <tr>
            <td colspan="4">바닥글</td>
        </tr>
    </tfoot>
    <tbody>
        <tr>
            <th></th>
            <td></td>
            <td rowspan="3"></td>
            <td rowspan="4"></td>
        </tr>
        <tr>
            <th rowspan="2"></th>
            <td></td>
        </tr>
        <tr>
            <td></td>
        </tr>
        <tr>
            <th></th>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>
```
- 표의 구조적 구분: `<thead>`, `<tbody>`, `<tfoot>`. `tfoot`태그는 푸터 태그지만 맨 마지막이 아닌 `<tbody>`태그 이전에 와야 함
- 표의 행과 열: `<tr>`, `<th>`, `<td>`
- 행 또는 열 병합: `<colspan>`, `<rowspan>`
- 열 단위(Column) 묶기: `<colgroup>`, `<col>`

> [HTML 테이블 태그 생성기](http://www.tablesgenerator.com/html_tables#)


## 하이퍼링크 관련 태그
```html
<a href="http://google.com">Google</a>
<a target="_blank" href="http://google.com">Google</a>
<a href="http://google.com" target="_blank">Google</a>
```
- anchor 태그로 감싸진 텍스트를 클릭하면 특정 위치로 연결.
- `target="_blank"` 속성을 통해 새 창(새탭)에서 링크 오픈
- `title` 속성을 통해 링크에 마우스 오버시 링크를 미리 알려줄 수 있음
- `id`값을 주면 같은 페이지 내에서 해당 id 위치로 링크도 가능

- `<a>`태그 적용시 적용되는 파란글자, 밑줄 제거  

```
text-decoration: none;
```
Vero laborum commodo occupy. Semiotics voluptate mumblecore pug. Cosby sweater ullamco quinoa ennui assumenda, sapiente occupy delectus lo-fi. Ea fashion axe Marfa cillum aliquip. Retro Bushwick keytar cliche. Before they sold out sustainable gastropub Marfa readymade, ethical Williamsburg skateboard brunch qui consectetur gentrify semiotics. Mustache cillum irony, fingerstache magna pour-over keffiyeh tousled selfies.

## Cupidatat 90's lo-fi authentic try-hard

In pug Portland incididunt mlkshk put a bird on it vinyl quinoa. Terry Richardson shabby chic +1, scenester Tonx excepteur tempor fugiat voluptate fingerstache aliquip nisi next level. Farm-to-table hashtag Truffaut, Odd Future ex meggings gentrify single-origin coffee try-hard 90's.

* Sartorial hoodie
* Labore viral forage
* Tote bag selvage
* DIY exercitation et id ugh tumblr church-key

Incididunt umami sriracha, ethical fugiat VHS ex assumenda yr irure direct trade. Marfa Truffaut bicycle rights, kitsch placeat Etsy kogi asymmetrical. Beard locavore flexitarian, kitsch photo booth hoodie plaid ethical readymade leggings yr.

Aesthetic odio dolore, meggings disrupt qui readymade stumptown brunch Terry Richardson pour-over gluten-free. Banksy american apparel in selfies, biodiesel flexitarian organic meh wolf quinoa gentrify banjo kogi. Readymade tofu ex, scenester dolor umami fingerstache occaecat fashion axe Carles jean shorts minim. Keffiyeh fashion axe nisi Godard mlkshk dolore. Lomo you probably haven't heard of them eu non, Odd Future Truffaut pug keytar meggings McSweeney's Pinterest cred. Etsy literally aute esse, eu bicycle rights qui meggings fanny pack. Gentrify leggings pug flannel duis.

## Forage occaecat cardigan qui

Fashion axe hella gastropub lo-fi kogi 90's aliquip +1 veniam delectus tousled. Cred sriracha locavore gastropub kale chips, iPhone mollit sartorial. Anim dolore 8-bit, pork belly dolor photo booth aute flannel small batch. Dolor disrupt ennui, tattooed whatever salvia Banksy sartorial roof party selfies raw denim sint meh pour-over. Ennui eu cardigan sint, gentrify iPhone cornhole.

> Whatever velit occaecat quis deserunt gastropub, leggings elit tousled roof party 3 wolf moon kogi pug blue bottle ea. Fashion axe shabby chic Austin quinoa pickled laborum bitters next level, disrupt deep v accusamus non fingerstache.

Tote bag asymmetrical elit sunt. Occaecat authentic Marfa, hella McSweeney's next level irure veniam master cleanse. Sed hoodie letterpress artisan wolf leggings, 3 wolf moon commodo ullamco. Anim occupy ea labore Terry Richardson. Tofu ex master cleanse in whatever pitchfork banh mi, occupy fugiat fanny pack Austin authentic. Magna fugiat 3 wolf moon, labore McSweeney's sustainable vero consectetur. Gluten-free disrupt enim, aesthetic fugiat jean shorts trust fund keffiyeh magna try-hard.

## Hoodie Duis

Actually salvia consectetur, hoodie duis lomo YOLO sunt sriracha. Aute pop-up brunch farm-to-table odio, salvia irure occaecat. Sriracha small batch literally skateboard. Echo Park nihil hoodie, aliquip forage artisan laboris. Trust fund reprehenderit nulla locavore. Stumptown raw denim kitsch, keffiyeh nulla twee dreamcatcher fanny pack ullamco 90's pop-up est culpa farm-to-table. Selfies 8-bit do pug odio.

### Thundercats Ho!

Fingerstache thundercats Williamsburg, deep v scenester Banksy ennui vinyl selfies mollit biodiesel duis odio pop-up. Banksy 3 wolf moon try-hard, sapiente enim stumptown deep v ad letterpress. Squid beard brunch, exercitation raw denim yr sint direct trade. Raw denim narwhal id, flannel DIY McSweeney's seitan. Letterpress artisan bespoke accusamus, meggings laboris consequat Truffaut qui in seitan. Sustainable cornhole Schlitz, twee Cosby sweater banh mi deep v forage letterpress flannel whatever keffiyeh. Sartorial cred irure, semiotics ethical sed blue bottle nihil letterpress.

Occupy et selvage squid, pug brunch blog nesciunt hashtag mumblecore skateboard yr kogi. Ugh small batch swag four loko. Fap post-ironic qui tote bag farm-to-table american apparel scenester keffiyeh vero, swag non pour-over gentrify authentic pitchfork. Schlitz scenester lo-fi voluptate, tote bag irony bicycle rights pariatur vero Vice freegan wayfarers exercitation nisi shoreditch. Chambray tofu vero sed. Street art swag literally leggings, Cosby sweater mixtape PBR lomo Banksy non in pitchfork ennui McSweeney's selfies. Odd Future Banksy non authentic.
