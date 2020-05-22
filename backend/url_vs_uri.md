# url과 uri의 차이

지나치듯 항상 들리는 개념 url과 uri가 무엇일까?  
그때그때 검색을 통해 이해하곤 했지만 다시 생각하면 까먹어서 정리를 해보았다.

## 정의

> URL

- URL은 (Uniform Resource Locator)로 통합 자원 주소를 의미한다.
- 파일의 위치 실제 경로를 뜻한다.

> URI

- URI는 (Uniform Resource Identifier)로 통합 자원 식별자를 의미한다.
- 해당 리소스를 식별할 수 있는 식별 정보를 뜻한다.

## 차이점

1. `https://www.url.com/index.html`과 같은 주소가 있다 가정하자.  
   위 주소는 `.html`로 끝나듯이 실제 파일의 주소를 의미한다.

2. `https://www.uri.com/posts`과 같은 주소가 있다 가정하자.  
   위의 주소는 **파일을 요청**하는것이 아니라 `posts`로 식별자를 요청한것이다. 서버에 `posts`라는 파일은 없다.

- 간단히 말하자면 URL은 **파일의 주소**를 의미하고 URI는 **파일을 식별할 수 있는 식별 정보**라고 생각하면 쉽다.

## 부가 설명

- `https://www.url.com/posts.html?id=1`과 같은 주소가 있다고 하면,  
  `...html` 까지는 url이고 `?id=1`부분은 url이 아니다.  
  내가 알고싶은 정보(id는 1)를 얻기위해 식별자를 추가한 것이다.

- URN(Uniform Resource Name)이라는 개념이 또 있는데, URN은 자원의 위치에 영향을 받지 않는 유일한 이름이다. 만약 자원의 위치가 바뀌더라도 문제없이 작동한다.

- ![이해가 쉬운 사진](https://t1.daumcdn.net/cfile/tistory/2416C94158D62B9E11)
