
# 설치 및 적용하기 

- 새로운 리액트 네이티브 프로젝트를 생성해 react-navigation을 설치하고 사용 방법을 배워보겠습니다. 우선 새 프로젝트를 만들어주세요.

```
npx react-native init LearnReactNavigation --version 0.70 
```

## 의존 패키지 설치하기

- 새로 생성한 프로젝트 디렉터리로 이동한 다음, yarn을 사용해 @react-navigation/native 모듈을 설치하세요.

```
cd LearnReactNavigation
yarn add @react-navigation/native
```

- react-navigation이 의존하는 라이브러리가 몇 가지 있습니다. 의존 라이브러리도 설치해주세요.

```
yarn add react-native-screens react-native-safe-area-content
```

- ios 디렉터리에 들어가서 Pod를 설치해주세요(맥OS를 사용하지 않는다면 생략해도 됩니다).

```
cd ios 
pod install
```

- 설치가 완료됐습니다.

##  라이브러리 적용하기

- 리액트 네이티브 프로젝트에 라이브러리를 적용하기 위해서는 @react-navigation/native에서 NavigationContainer 컴포넌트를 불러와 사용해 앱 전체를 감싸주어야 합니다. 
- App 컴포넌트를 열어 파일 내용을 모두 지우고 다음과 같이 코드를 작성하세요

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';

function App() {
    return <NavigationContainer>{/* 네비게이션 설정 */}</NavigationContainer>;
}

export default App;
```

- 리액트 네이티브 프로젝트를 만들었을 때 기본적으로 들어있는 내용은 불필요하므로 모두 지웠습니다. 
- NavigationContainer 컴포넌트 간에는 내비게이션 설정을 JSX로 선언할 수 있습니다.

---
# 기본적인 사용법 

- 웹 페이지는 <a href="https://google.com">구글</a> 같은 태그를 사용해 링크를 클릭하면 해당 주소로 이동하고, 이동 후에는 뒤로가기 버튼을 눌러서 뒤로 이동하고, 다시 앞으로가기 버튼을 눌러서 앞으로 이동할 수 있어요. 웹 브라우저에 탑재된 History 기능이 있어 가능한 일입니다. History는 스택(Stack) 자료구조를 사용해 구현되어 있습니다.
- 스택 자료구조는 새로운 항목을 추가할 때 맨 위에 쌓이고, 맨 위에 있는 항목을 추출할 수 있습니다. 새로운 주소로 이동할 때는 push하고, 뒤로 갈 때는 pop하는 것이죠. 리액트 네이티브 앱에서는 화면을 전환할 때 브라우저의 History와 비슷한 사용성을 제공하기 위해 네이티브 스택 내비게이터(Native Stack Navigator)를 사용합니다.

---
# 다양한 내비게이터

---
# 내비게시연 Hooks