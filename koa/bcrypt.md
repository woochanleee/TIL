# bcrypt 사용법

[참고 - 리액트를 다루는 기술 개정판]  
사용자의 비밀번호를 데이터베이스에 저장할 때 플레인(아무런 가공도 하지 않은) 텍스트로 저장하면 보안상 위험하다.  
그래서 nodejs koa에서 bcrypt를 적용하는 방법에 대해 소개하겠다.

## 특징

- bycrypt라이브러리는 단방향 해싱 함수를 지원한다.

## 사용법

먼저 bcrypt를 설치하자.

```
yarn add bcrypt
```

```typescript
import mongoose, { Schema } from "mongoose";

const UserSchema = new Schema({
  username: String,
  hashedPassword: String, // 암호화한 비밀번호
});

const User = mongoose.model("User", UserSchema);
export default User;
```

먼저 위와 같은 모델, 스키마가 있다고 하자. 그러면 아래와 같이 적용할 수 있다. typescript 개발환경에서 주의할 사항은 Schema의 스태틱 메서드와 인스턴스 메서드를 설정해줄때 인터페이스를 선언하고 적용해주어야 한다. 자세한 코드는 아래서 보자. 그냥 할시에 function a does not exist... 와 같은 에러가 발생할것이다.

```typescript
import mongoose, { Schema, Document, Model } from "mongoose";
import bycrypt from "bycrypt";

const UserSchema: Schema = new Schema({
  username: String,
  hashedPassword: String, // 암호화한 비밀번호
});

interface IUserDocument extends Docuemnt {
  // User Docuemnt를 정의해준다.
  username: String;
  hashedPassword: String;
}

interface IUser extends IUserDocuemnt {
  // User 인터페이스를 정의해준다. 메서드 정의 부분
  setPassword: (password: string) => void;
}

interface IUserModel extends Model<IUser> {
  // User 모델의 인터페이스 정의해준다. 스태틱 메서드 부분
  findByUserName: () => object;
}

UserSchema.method("setPassword", async function (password) {
  // 메서드 선언 법
  const hash = await bcrypt.hash(password, 10); // hash함수를 통해 비밀번호를 10자리로 해싱한다.
  this.hashedPassword = hash; // arrow function말고 function으로 선언해 Schema의 비밀번호를 this로 초기화해준다.
});

UserSchema.static("findByUserName", function () {
  // static 메소드 선언법
  return this.findOne({ username });
});

const User: IUserModel = mongoose.model<IUser, IUserModel>("User", UserSchema); // 제네릭으로 User(User 모델로 findOne()을 통해 얻은 리턴값 등)인터페이스와 UserModel(mongoose.model로 생긴 model) 인터페이스를 설정해준다.
export default User;
```

이렇게 bcrypt를 이용하여 비밀번호를 해싱하는 법에 대해 살펴보았다. 데이터베이스에 저장할때 위험성을 알고 편리하다고 느꼈다.
