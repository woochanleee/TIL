# koa-body를 이용한 파일(이미지, 동영상) 처리(Using with koa-router)

## 들어가기 전

nodejs koa에서 이미지와 동영상 등 파일들을 처리하기위헤 있는 폼데이터(form-data)를 처리하는 방법에 대해 알아보고 정리하였다.

### koa-body란?

먼저 나는 koa-body라는 라이브러리(모듈?)을 사용했다. 사실 이것을 사용하기 전에 multer, formidable, koa-multer, koa-formidable 등 여러 종류를 시도해 보았는데 typescript와 호환이 안되거나 내가 영어 실력이 딸려서 이해하지 못해 formdata를 다루는데 성공하지 못하여서 포기하고 koa-body를 쓰게 됐다.

### 사용법

설명하기 전 [koa-body npm주소](https://www.npmjs.com/package/koa-body)에서 자세한 api를 볼수 있으니 참고 바란다. 또 나는 `koa-router`에 적용하는 방법을 사용했지만 Koa(app)에 적용하는 방법도 많으니 따로 찾아보면 쉽에 찾을수 있을것이다.

> 설치

```
yarn add koa-body
```

를 통해 설치를 먼저 해준다. 신기하게도 ex) types/koa-body 와같이 typescript 개발환경에서 type을 따로 설치 안해줘도 작동이 가능하다. 이유는 잘 모르겠다.

> 코드

```
import { Context } from 'koa';
import Router from 'koa-router';
import koaBody from 'koa-body'; // koa-body 불러오기

const rotuer = new Router();

router.post(
  '/post',
  koaBody({
    multipart: true,
  }),),
  (ctx: Context) => {
    const { title, body } = ctx.request.body; // formdata의 기본적인 text필드들은 ctx.request.body에 값이 들어오고
    const file = ctx.request.files.files; // file형식은 ctx.request.files에 들어온다. ctx.request.files.[files] 여기서 마지막 files는 내가 키값으로 설정해준 값이다. ex) new form().append('files', ~) 와 같이 키 값을 줬기 때문에 위와같이 접근한것이다.
    let files = [];
    for (let i = 0; i < file.length; i++)
      files.push(fs.readFileSync(file[i].path)); // 나는 file들을 formdata로 읽어와 buffer로 데이터베이스에 저장했다.

    /*
  	  여기부터
    */
    const post = new Post({
      title,
      body,
      tags,
      files,
      isPrivate,
    });
    try {
      await post.save();
      ctx.body = post;
    } catch (e) {
      ctx.throw(500, e);
  }
  /*
    여기까지는 mongoose, mongodb관련된 코드인데 무시해도 된다.
  */
 }
)
```

![](https://images.velog.io/images/_uchanlee/post/efe1abcc-b3cf-40ba-bfc5-0b2c8fcf5ec4/image.png)
npm 문서에 보면 multipart와 formidable 옵션이 있는데 둘다 multipart를 사용가능한것 처럼 보이는데 차이를 모르겠다. 나는 multipart가 익숙해서 이걸 옵션에 사용했다.

> 성공

![](https://images.velog.io/images/_uchanlee/post/f589a327-4190-44a6-baaf-31ef15e761e8/image.png)
성공적으로 mongodb에 바이너리코드의 버퍼(?)가 저장된것을 볼수 있다.

## 느낀점

오늘 하루는 formdata를 처리하기위해 5시간을 삽질한것 같다. 이렇게 한번 해결해 냈으니 다음에도 잘 해결할것이라 믿는다.

fomdata를 처리하는 방법을 알아봤는데 file을 다루는 것은 어느 분야에서든 어려운것 같다. 자주 접하면서 익숙해져가는게 답인것 같다.
