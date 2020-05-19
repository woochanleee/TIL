# CORS

역시나 cors문제가 발생하였다. 그래서 koa에서 cors를 간단하게 허용하는 방법을 정리해 본다.

## 사용법

나는 `@koa/cors`를 사용해서 해결했다.

```
import Koa from 'koa';
import cors from '@koa/cors';

const app = new Koa();

app.use(bodyparser()); // 이건 client에서 요청한데이터를 파싱하기위한 설정이다.
app.use(cors()); // 이와 같이 app에 미들웨어를 적용해주면 끝이다.
// 이 위 두 코드들은 아래 코드보다 위에 있는데 route를 등록하기 전에 먼저 해줘야 한다고 한다. 이유는 나도 모르겠다. 그렇게 안하면 에러가 난다.

app.use(router.routes()).use(router.allowedMethods());
```
