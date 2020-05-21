# 파일 서버에서 파일 삭제하기

지난 시간에 파일서버에 파일을 저장하고 url을 통해 접근 가능하게 만들어 보았다.  
그런데 문제가 있단걸 발견했다.

## 문제

1. 현재 데이터베이스에 파일의 경로가 저장되어있다.
2. 만약 파일의 경로가 저장된 문서를 삭제한다면?
3. 디비에는 파일의 경로가 사라졌으니 파일을 접근 못할것이라 생각하겠지만 NO!!!❌  
   아직 파일 서버에는 그 파일이 그대로 저장되어있고 url을 통해 여전히 접근이 가능한 상태이다.
4. 이렇게 된다면 만약 사진을 올렸다 삭제를했는데 url로 접근하면 삭제가 안되고 사진을 볼수 있다는 큰 문제가 발생한다.

## 해결법

- 이에 대한 해결법은 다음과 같다.

1. 삭제의 요청이 들어온다.
2. 데이터베이스에서 해당 파일의 경로의 값을 가져온다.
3. 파일 서버에 저장된 파일을 삭제한다.
4. 데이터베이스에서도 문서를 삭제한다.

## 코드

다음은 코드의 일부분이다.

```typescript
import { Context } from "koa";
import Router from "koa-router";

const deleteFile = (path: string) => {
  // 파일을 삭제하는 함수
  fs.unlink(`./public/${path}`, (err) => {
    // fs.unlink()로 파일을 삭제할 수 있고, 두번재 인자값 콜백함수는 에러가 있을시에 매개변수로 에러를 보낸다.
    if (err) console.error(err);
  });
};

router.delete("/post/:id", async (ctx: Context) => {
  const { id } = ctx.params;
  try {
    const post: any = await Post.findById(id).exec();
    if (!post) {
      ctx.status = 404;
      return;
    }
    const pathList = post.files;
    if (pathList.length) {
      for (let i = 0; i < pathList.length; i++) {
        await deleteFile(pathList[i]);
      }
      post.remove();
      return (ctx.status = 204);
    } else {
      post.remove();
      ctx.status = 204;
    }
  } catch (e) {
    ctx.throw(500, e);
  }
});
```

이렇게 하면 파일을 삭제할때 정상적으로 파일 서버와 데이터베이스 모두에서 삭제가 된다.
