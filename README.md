# GUZZI 프로젝트

<br/>

## 목차

1. [프로젝트 설명](#1-프로젝트-설명)
2. [기술 스택](#2-기술-스택-Technique-Used)
3. [아키텍처](#3-아키텍처)
4. [DB 구성](#4-DB-구성)
5. [API-명세](#5-API-명세)
6. [성능 테스트](#6-성능-테스트)
7. [트러블 슈팅](#7-트러블-슈팅)

<br/><br/>

# `1. 프로젝트 설명`

## Directory Structure

```
📂 backend
    📂 guzzi
    ├── 📂 controller
    |   └── 📄 list.js
    ├── 📂 data
    |   └── 📄 list.js
    ├── 📂 db
    |   └── 📄 database.js
    ├── 📂 router
    |   └── 📄 lists.js
    ├── 📂 schema
    |   ├── 📄 list.js
    |   └── 📄 validate.js
    ├── 📄 swagger.js
    ├── 📄 server.js
    ├── 📄 app.js
    ├── 📄 package.json
    ├── 📄 README.md
    └── 📄 .prettierrc.json
```

<br/><br/>

<p align="right"><a href="#목차">⬆ 목차로 돌아가기</a></p>

# `2. 기술 스택 (Technique Used)`

## Language

| <img alt="javascript" src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/99/Unofficial_JavaScript_logo_2.svg/140px-Unofficial_JavaScript_logo_2.svg.png" height=100> |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                                      [Javascript](https://developer.mozilla.org/ko/docs/Web/JavaScript)                                                       |

## Server Management Tools

| <img alt="nodemon" src="https://user-images.githubusercontent.com/13700/35731649-652807e8-080e-11e8-88fd-1b2f6d553b2d.png" height=100> |
| :------------------------------------------------------------------------------------------------------------------------------------: |
|                                               [nodemon](https://github.com/remy/nodemon)                                               |

## Server(back-end)

| <img alt="nodejs" src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Node.js_logo.svg/220px-Node.js_logo.svg.png" height=100> | <img alt="express" src="https://upload.wikimedia.org/wikipedia/commons/6/64/Expressjs.png" height=100> |
| :---------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------: |
|                                                 [Nodejs v16.17.0](https://nodejs.org/ko/)                                                 |                               [Express v4.18](https://expressjs.com/ko/)                               |

## Database

| <img src="https://i.namu.wiki/i/lKYgsxax0w1O9u-Vt6thK_IdIv3MRGGXQJGA6SjkF5dORNfkV9QQlJ909916bhWWetwRhgiaBYvljSYG2v4YdC5xJ0vqzQkjdZOZMQD43avXpgVPJEblz85mXwblsJCiLnhBXoKMwTUypMxzbTkaVg.svg" height=100> |
| :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                                                                     [MySQL](https://www.mysql.com/)                                                                                     |

## 사용된 오픈소스(Used Open Source)

- backend
  - [ajv](https://github.com/ajv-validator/ajv)
  - [cors](https://github.com/expressjs/cors)
  - [dotenv](https://github.com/motdotla/dotenv)
  - [helmet](https://github.com/helmetjs/helmet)
  - [morgan](https://github.com/expressjs/morgan)
  - [uuid](https://github.com/uuidjs/uuid)
  - [jsdoc](https://github.com/jsdoc/jsdoc)
  - [prettier](https://github.com/prettier/prettier)
  - [swagger-jsdoc](https://github.com/Surnet/swagger-jsdoc)
  - [swagger-ui](https://github.com/swagger-api/swagger-ui)

<br/>

<p align="right"><a href="#목차">⬆ 목차로 돌아가기</a></p>

# `3. 아키텍처 시각화`

<details open="open">
  <ul style="list-style: none;">
    <li> ᐅ GET </li>
	<table align="center">
		<tr>
			<td align="center"><b>채팅 리스트 페이지별 요청</b></td>
		</tr>
		<tr>
			<td align="center">
				<img src="./image/getPage.png">
			</td>
		</tr>
		<tr>
			<td align="center">
				<ul style="list-style: none;">
          <li>Page 최솟값 : 1</li>
          <li>Page 숫자 Validation Check</li>
          <li>DB에서 받아온 데이터 Validation Check</li>
        </ul>
			</td>
		</tr>
	</table>
	<table align="center">
		<tr>
			<td align="center"><b>채팅 리스트 메시지 아이디별 요청</b></td>
		</tr>
		<tr>
			<td align="center">
				<img src="./image/getMsgid.png">
			</td>
		</tr>
		<tr>
			<td align="center">
				.
			</td>
		</tr>
	</table>
    <li> ᐅ POST  </li>
	<table align="center">
		<tr>
			<td align="center"><b>채팅 메시지 추가</b></td>
		</tr>
		<tr>
			<td align="center">
				<img src="./image/post.png">
			</td>
		</tr>
		<tr>
			<td align="center">
			  .
			</td>
		</tr>
	</table>
	    <li> ᐅ PUT </li>
	<table align="center">
		<tr>
			<td align="center"><b>채팅 메시지 수정</b></td>
		</tr>
		<tr>
			<td align="center">
				<img src="./image/put.png">
			</td>
		</tr>
		<tr>
			<td align="center">
				.
			</td>
		</tr>
	</table>
	    <li> ᐅ DELETE </li>
    	<table align="center">
		<tr>
			<td align="center"><b>채팅 메시지 삭제</b></td>
		</tr>
		<tr>
			<td align="center">
				<img src="./image/delete.png">
			</td>
		</tr>
		<tr>
			<td align="center">
				.
			</td>
		</tr>
	</table>
	
  </ul>
</details>

<br/>

<p align="right"><a href="#목차">⬆ 목차로 돌아가기</a></p>

# `4. DB 구성`

## ![✔] 4.1 다른건 못해도 최소한 API (컴포넌트) 테스트는 써라

<br/>

<p align="right"><a href="#목차">⬆ 목차로 돌아가기</a></p>

# `5. API 명세`

## ![✔] 5.1. Swagger

**핵심요약:** 모니터링은 고객이 문제를 발견하기 전에 먼저 발견하는 게임이다. 모니터링에는 분명히 전례가없는 중요성을 부여해야한다. 솔루션에 너무 많은 기능들이 들어가 있을 가능성이 있으므로 확인해야만하는 기본 항목을 내부적으로 정의하고 나서 추가적인 기능들을 살펴보고 필요한 기능들이 모두 들어있는 솔루션을 선택하라. 아래의 'gist'를 클릭하면 솔루션 개요를 볼 수 있다

**그렇게 하지 않을 경우:** 오류 === 고객의 실망. 간단하다

🔗 [**자세히 보기: 모니터링!**](./sections/production/monitoring.md)

<br/>

<p align="right"><a href="#목차">⬆ 목차로 돌아가기</a></p>

# `6. 성능 테스트`

<div align="center">
<img src="https://img.shields.io/badge/OWASP%20Threats-Top%2010-green.svg" alt="54 items"/>
</div>

## ![✔] 6.1. linter 보안 규칙 수용

<a href="https://www.owasp.org/index.php/Top_10-2017_A1-Injection" target="_blank"><img src="https://img.shields.io/badge/%E2%9C%94%20OWASP%20Threats%20-%20A1:Injection%20-green.svg" alt=""/></a> <a href="https://www.owasp.org/index.php/Top_10-2017_A7-Cross-Site_Scripting_(XSS)" target="_blank"><img src="https://img.shields.io/badge/%E2%9C%94%20OWASP%20Threats%20-%20XSS%20-green.svg" alt=""/></a>

**핵심요약:** [eslint-plugin-security](https://github.com/nodesecurity/eslint-plugin-security)와 같은 보안 관련 linter 플러그인을 사용하여 보안 취약점과 문제점을 가능한 한 빨리 잡아라. 이것은 eval 사용, 자식 프로세스 호출, string literal(예: 사용자 입력)을 쓰는 모듈 불러오기 같은 보안 취약점을 잡는데 도움이 될 수 있다. 보안 linter가 잡는 코드를 보려면, 아래의 '자세히 보기'을 클릭해라.

**그렇게 하지 않을 경우:** 개발과정에서는 이해하기 쉬운 보안 약점이었음에도 상용환경에서는 주요쟁점이 된다. 또, 프로젝트가 일관된 보안 프렉티스를 따르지 않아, 취약점이 노출되거나 민감한 정보가 원격 저장소에 유출될 수 있다.

🔗 [**자세히 보기: Lint rules**](./sections/security/lintrules.md)

<br/>

<p align="right"><a href="#목차">⬆ Return to top</a></p>

# `7. 트러블 슈팅`

<br/><br/>

## ![✔] 7.1. 이벤트 루프를 막지 말아라

**핵심요약:** CPU 집약적인 과제들은 거의 단일 스레드로 된 이벤드 루프를 블로킹하고 전용 스레드나 프로세스, 혹은 컨텍스트에 따라 그 외 다른 기술에 떠넘기므로 피하라.

**그렇게 하지 않을 경우:** 이벤트 루프가 블로킹되면 Node.js는 다른 요청을 처리할 수 없게 되어 동시성 (concurrent) 사용자들을 지체하게 한다. **사용자 3000명이 응답을 기다리고 있고, 콘텐츠도 제공될 준비가 되어있는데, 단 하나의 요청이 서버가 결과물을 발송하지 못하도록 블로킹 할 수 있다**

🔗 [**자세히 보기: Do not block the event loop**](./sections/performance/block-loop.md)

<br /><br /><br />

## ![✔] 7.2. Lodash같은 user-land 유틸 대신 네이티브 자바스크립트 메소드를 택해라

**핵심요약:** 네이티브 메소드 대신 `lodash` 나 `underscore` 같은 유틸 라이브러리를 쓰는 것은 불필요한 의존성이나 성능 저하를 야기할 수 있기에 보통 페날티가 붙는다.
새로운 V8 엔진과 함께 새로운 ES 기준이 도입되고부터 네이티브 메소드가 유틸리티 라이브러리보다 50% 더 능률적이라는 것을 명심해라.

**그렇게 하지 않을 경우:** 기본적으로 **이미** 내장된 코드를 쓰거나 코드를 몇줄 더 써서 파일을 몇개 더 써야 하는 것을 막을 수 있었음에도 불구하고 더 비능률적인 프로젝트를 유지해야 할 것이다.

🔗 [**자세히 보기: Native over user land utils**](./sections/performance/nativeoverutil.md)
