# Request Body 검증하기

예를 들어 게시글 쓰기, 삭제, 수정, 등 api에서 전달 받은 요청 내용을 검증하는 방법을 정리하겠다.  
[참고-리액트를 다루는 기술 개정판]

## 검증해야 하는 이유?

- 우리가 만약에 게시글을 만드는 api가 있다고 가정하자.
- 게시글을 작성할때는 title, body, 등 필수적인 값을 모두 절달받아야 한다.
- 그런데 클라이언트가 값을 빼먹고 요청을 한 경우 제목이 없는 게시글이 생성되는 대참사가 발생한다.
- 그래서 이와 같이 값을 빼먹고 요청을 했을때 400 오류를 발생해야 한다.

## 사용법

전달된 객체를 검증하기 위해 각 값을 모두 if 문으로 해결할수도 있지만 이를 수월하게 해 주는 라이브러리인 [Joi](https://github.com/hapijs/joi)를 사용하겠다.

### 설치

```
yarn add  joi
```

커맨드를 통해 설치를 해주자.

### 코드 적용

아래 코드의 일부 예시가 있다.

```typescript
import { Context } from 'koa';
import router from 'koa-router';
import Joi from 'joi';

const router = new Router();

router.post('/posts', async (ctx: Conter) => {
    const schema = Joi.object().keys({
        // 객체가 다음 필드를 가지고 있음을 검증한다.
        title: Joi.string().required(), // required()가 있으면 필수 항목
        body: Joi.string().required(),
        tags: Joi.array()
          .items(Joi.string())
          .required(), // 문자열로 이루어진 배열
    })

    // 검증하고 나서 검증 실패인 경우 에러 처리
    const result = Joi.validate(ctx.request.body, schema);
    if (result.error) {
        ctx.status = 400; // Bad Request
        ctx.body = result.error;
        return;
    }
    ...
});

```

## 마무리

오늘 nodejs koa 환경에서 Joi 라이브러리를 통해 request body를 검증하는 방법에 대해 살펴보았다. 이를 프로젝트에 적용하면 매우 편리할것 같고 오류를 잘 잡아줄것 같다. 이렇게 편리한 라이브러리들은 앞으로도 자주 사용해야 겠다.
