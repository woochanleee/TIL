# mongoose 2단 SubDocument 사용법(with koa)
몽구스로 블로그의 대댓글 스키마를 구현하다가 난관에 빠져 몇시간 삽질을하고 2단subdocument를 사용하는법을 찾아냈다. 내가 **_'이렇게 하면 되려나?'_** 하고 찾은 방법이기때문에 이게 정답이라고 말할순 없다. 그냥 단순 참고용으로 확인 바라고 다르게 사용하는 방법이 있다면 댓글로 남겨주면 정말 감사하겠다. 그럼 시작해보도록 하겠다.

나와 같은 문제를 겪거나 해결법이 필요해서 오신 분들을 [해결 코드](#모델-생성2)로 가서 확인해보고 문제를 해결하기를 추천드린다.
## 정의
> 📖 subDocument가 뭔데?

먼저 이것에 대해 알기 전 MongoDB에서 스키마를 디자인 하는 방식에 대해 살펴보고 지나가야 한다.

### MongoDB 특징 및 서브스키마
1. 몽고디비는 기존 RDBMS(관계형데이터베이스)와 스키마를 디자인하는 방식이 완전 다르다.
1.1 RDBMS에서 데이터베이스를 설계하면 다음과 같을 것이다. ~~_정확하지 않음_~~![대충](https://images.velog.io/images/_uchanlee/post/be45e7a8-7d14-4768-9dd7-765367a7a950/image.png)
1.2 하지만 몽고디비는 NoSQL기반므로 NoSQL에서는 모든 것을 문서 하나에 넣는다. 아래와 같다.
```
    {
    	_id: ObjectId,
        title: String,
        body: String,
        username: String,
        createdDate: Date,
        comments: [
        	{
            	_id: ObjectId,
                text: String,
                createdDate: Date,
         	},
        ],
    }
 ```
 2. comments와 같이 문서 내부에 또다른 문서가 위치할 수 있는데, 이를 서브다큐먼트(subdocument)라고 한다.
 3. 서브다큐먼트 또한 일반 문서를 다루는 것처럼 쿼리할수 있다.
 4. 문서 하나에는 최대 16MB의 데이터를 넣을수 있는데, 100자 댓글 데이터가 약 0.24KB를 차지한다. 16MB는 16.384KB이니 문서 하나에 댓글 데이터를 약 68,000개 넣을수 있다. 서브다큐먼트에서 이 용량을 초과할 가능성이 있다면 컬레션을 불리시키는 것이 좋다. 
 [참고 - 리액트를 다루는 기술 개정판]
 
## 1단 subschema 구현
먼저, 문서 내에 서브스키마를 하나 넣는 경우를 구현해 보겠다.
하나만 넣을 경우엔 굉장히 간단하다. [몽구스 subdocument 공식문서](https://mongoosejs.com/docs/subdocs.html)
공식 문서에 나와있듯이 스키마를 구현할땐 다음과 같이 하면 된다.

### 스키마 생성
```
import mongoose from "mongoose";

const { Schema } = mongoose;

const CommentSchema = new Schema({
  text: String,
  publishedDate: {
    type: Date,
    default: Date.now,
  },
});

const PostSchema = new Schema({
  title: String,
  body: String,
  publishedDate: {
    type: Date,
    default: Date.now,
  },
  comments: [CommentSchema] // comments를 CommentSchema타입의 배열로 설정해줌, 여기가 서브 스키마
});

const Post = mongoose.model("Post", PostSchema);

export default Post;
```
이것처럼 댓글은 여러개가 올수 있으니 CommentSchema타입의 배열로 설정해 준 모습을 볼수 있다.

### 모델 생성

스키마를 만들었으니 이제 모델을 만들고 직접 값을 저장한뒤 데이터베이스에 저장해줘야 한다.
그럼 어떻게 하는지 알아보도록 하자. 다음 코드는 코드의 일부분이다.

```
import Router from 'koa-router';
import Post from "";
import { Context } from "koa";

/*
  POST /comments
  {
      text: '우와 좋은 정보 감사합니다!',    
  }
*/
const router = new Router();
router.post('/comments', async (ctx: any) => {
  const { id } = ctx.params;
  const { text } = ctx.request.body;
  const post: any = new Post(); // 먼저 그냥 model을 하나 생성해준다.

  const commentDoc = post.comments.create({ text }); // ✨핵심✨ comments에서 create함수를 사용해서 CommentSchema 스키마의 모델(?) 아무튼 값을 담을수 있는 문서가 하나 생긴다. 아직 정확히는 모르지만 스키마 타입의 변수라면 create함수가 있는것 같다. 그래서 post.create도 가능할것 같다고 본다.
  const newComments = [...postDoc.comments].concat(commentDoc);
  await Post.findOneAndUpdate(
    { id },
    {
      comments: newComments,
    },
    {
      new: true,
    }
  );
    ctx.body = commentDoc;
  } catch (e) {
    ctx.throw(500, e);
  }
});


```
자 이렇게 서브스키마가 하나 있는 경우에 모델을 생성해 보았다.
**그러면 서브스키마 안에 서브스키마가 있는 경우는?**

## 2단 서브스키마 구현
서브 스키마내부에 서브스키마 타입의 변수가 또 있는 경우를 해결해 보겠다.

### 스키마 생성
먼저 스키마의 구조는 다음과 같이 있다고 하자.

```
import mongoose from "mongoose";

const { Schema } = mongoose;

const RecommentSchema = new Schema({
  text: String,
  publishedDate: {
    type: Date,
    default: Date.now,
  },
});

const CommentSchema = new Schema({
  text: String,
  publishedDate: {
    type: Date,
    default: Date.now,
  },
});

const PostSchema = new Schema({
  title: String,
  body: String,
  publishedDate: {
    type: Date,
    default: Date.now,
  },
  comments: {
    type: [CommentSchema],
    default: {
      recomments: [],
    },
  }, // 이부분이 달라졌다. default 값을 선언안해두니 create()부분에서 recomments가 존재하지않다고 생성이 불가하였다.
});

const Post = mongoose.model("Post", PostSchema);

export default Post;


```
여기서 주의깊게 봐야할 부분은 PostSchema의 comments부분인데, 처음에 나는 `comments: [ComentSchema]`
라고만 설정해 주었는데 이렇게 하니까 create부분에서 comments의 값이 []로 빈 배열이여서 recomments를 생성하질 못했다. _~~갑자기 글을 쓰다 궁금해졌는데 그냥 schema를 import해와서 schema.create를 하면 문서가 만들어지나? 갑자기 궁금하네 아시는분 댓글좀 달아주세요!~~_
그냥 못참아서 직접 해봤는데 사진과 같다고한다. 음.. 왜 안되는지 모르겠다. 스키마 타입의 변수에만 create함수가 있는건가..? 흠....
![](https://images.velog.io/images/_uchanlee/post/5cd641d6-629a-4cf4-ae8a-f302b13afe6b/image.png)

### 모델 생성2
그럼 이제 2단으로 구성되어있는 서브스키마를 어떻게 모델로 생성하는지 알아보도록 하자.
```
import Post from "../../../../models/post";
import { Context } from "koa";
import Router from 'koa-router';

const router = new Router();
/*
  POST comments/:comment_id/recomment
  {
      text: '이게 되네...',    
  }
*/
router.post('/comments', async (ctx: any) => {
  const { id } = ctx.params;
  const { text } = ctx.request.body;
  const post: any = new Post(); // 아까와 같이 문서(모델?)를 만들어준다.
  try {
    const recommentDoc = post.comments[0].recomments.create({ text }); // 🎇여기 핵심, post는 지금 디폴트로 값이 지정되있는것들만 값이 있는 상태인데 아까 comments의 디폴트 값으로 recomments를 넣어두었기 때문에 comments[0].recomments로 접근해서 create함수를 사용해서 모델(문서?)를 생성해준다.🎇
    await Post.findOneAndUpdate(
      { id },
      {
        recommentDoc
      },
    );
    ctx.body = recommentDoc;
  } catch (e) {
    ctx.throw(500, e);
  }
};

```

## 마무리

오늘 삽질을 하다가 우연히 해결하게 된 몽구스에서 서브스키마 안에 서브스키마를 넣는 방법을 정리해 보았다. 구글링도 엄청 많이 해봤는데 keyword: subdocument.id()함수 - 뭔진 이해 못함, $함수? - 배열 안에 있는 서브스키마를 선택할때 사용한다고 한것 같음, $push - 이것도 관련되서 찾아보다가 발견, 등 등 새로운 개념들을 보았는데 너무 어려웠다. 공식 문서만 보고 개발하기엔 아직 한계가 있는것 같다. (영어 잘하자...)

이렇게 오늘 하루 몽구스 쿼리문에 대해도 많이 습득하고 스키마 짜는 것에대해서도 배워갔다. 백엔드는 너무 어렵다!!! 조금 더 열심히 공부해야겠다는 마음가짐이 생겼다.
