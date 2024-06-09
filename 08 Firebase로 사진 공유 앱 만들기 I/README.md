> 데이터를 온라인에 올려서 여러 사용자가 함께 사용할 수 있는 앱을 개발해보겠습니다. 이 과정에서 Firebase라는 모바일 앱 개발 플랫폼을 사용할 것입니다. 일반적으로 앱에서 사용할 데이터를 온라인으로 가져오려면 백엔드를 따로 개발해야 합니다. 만약 여러분이 백엔드 개발을 할 수 있다면 앱에서 사용할 API 서버를 만들고, 연동하면 됩니다. 하지만 백엔드 개발을 몰라도 Firebase를 사용하면 구현하고 싶은 기능들을 구현할 수 있습니다.
 
> Firebase는 BaaS(Backend as a Service)입니다. 백엔드를 서비스로 제공하죠. 별도로 서버를 개발하지 않아도 앱 내에서 회원 인증, 데이터 쓰기/읽기, 이미지 업로드 등 다양한 기능을 구현할 수 있습니다. 그렇다고 Firebase가 만능은 아닙니다. 어떤 기능은 Firebase로 구현하기 어려울 수도 있습니다. 그리고 앱의 사용자가 늘어나고 데이터의 처리량이 커지면 API 서버를 직접 개발했을 때보다 비용이 많이 발생할 수 있습니다.

> 여러분이 MVP(Minimum Viable Product, 최소 기능 제품)를 만들거나, 간단한 앱을 만든다면 Firebase를 사용하는 것을 추천합니다. 하지만 오랫동안 유지보수하는 것을 계획하고, 앱 사용자가 많아질 거라고 예상한다면 앱에서 사용할 서버를 직접 구축하는 것을 권장합니다.

> 이번에 만들 프로젝트에서는 Firebase를 사용합니다. 그리고 다음에 만들 프로젝트에서는 Firebase가 아닌 이미 만들어진 API 서버와 연동해 앱을 만들어봅니다. 참고로 Firebase는 무료로 사용할 수 있지만 사용량이 늘어나면 요금을 결제해야 할 수도 있습니다.

> 이번에 만들 PublicGallery 프로젝트는 이메일/비밀번호 인증 시스템을 사용합니다. 사용자는 원하는 문구와 함께 사진을 업로드할 수 있습니다. 그리고 피드에서 사진 목록을 확인하거나, 사용자 프로필 화면으로 이동해 해당 사용자가 올린 사진들을 모아서 볼 수 있습니다.

- 이 장에서는 다음 내용을 다룹니다.
    - Firebase 연동하기
    - 회원 인증 구현하기
    - 사진 선택 및 업로드하기 

# 프로젝트 준비하기

- 리액트 네이티브 프로젝트를 생성하고 Firebase를 연동해봅시다. 먼저 다음 명령어를 사용해 프로젝트를 생성하세요.

```
npx react-native init PublicGalley<닉네임> --version 0.70
```

- 프로젝트를 생성할 때 이름 뒷부분에 여러분의 닉네임을 알파벳으로만 입력하세요(예, PublicGalleryYonggyo). 
- Firebase는 프로젝트의 패키지 이름으로 프로젝트를 구분하는데, 많은 분이 PublicGallery라고 만든다면 프로젝트 패키지가 겹쳐서 추후 Firebase를 적용할 때 오류가 발생하기 때문입니다.
- 패키지 이름을 변경할 수 있긴 하지만, 번거로운 편이므로 프로젝트를 만들 때 뒷부분에 여러분의 닉네임을 넣어주세요.

## 내비게시션과 아이콘 설정하기

- 프로젝트를 생성했으면 프로젝트 디렉터리로 이동해 내비게이션 관련 라이브러리와 아이콘 라이브러리를 설치하세요.

```
cd PublicGallery<닉네임>
yarn add @react-navigation/native react-native-screens react-native-safe-area-context @react-navigation/native-stack @react-navigation/bottom-tabs react-native-vector-icons
```

- 설치 후에는 npx pod-install 명령어를 실행해주세요.
- 라이브러리를 적용하겠습니다. screens 디렉터리를 만들고, 그 안에 RootStack.js 파일을 생성해 다음과 같이 작성하세요.

> screens/RootStack.js

```jsx
import React from 'react';
import {createNativeStackNavigator} from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

function RootStack() {
  return <Stack.Navigator>{/* 화면 추가 예정 */}</Stack.Navigator>;
}

export default RootStack;
```

- App.js에 있던 코드를 모두 지우고 다음과 같이 입력하세요.

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import RootStack from './screens/RootStack';

function App() {
  return (
    <NavigationContainer>
      <RootStack />    
    </NavigationContainer>
  );
}

export default App;
```

- 이제 내비게이션 적용은 끝났습니다. react-native-vector-icons를 적용할 차례입니다. 앞에서 다이어리 앱을 만들 때와 동일하게 MaterialIcons를 사용하겠습니다.
- 다음 파일들을 수정해주세요.

> ios/PublicGallery\<닉네임\>/Info.plist

```xml
...

  <key>UIViewControllerBasedStatusBarAppearance</key>
  <false/>
  <key>UIAppFonts</key>
  <array>
      <string>MaterialIcons.ttf</string>
  </array>
</dict>
</plist>
```

> android/app/build.gradle 

```groovy
...

project.ext.vectoricons = [
        iconFontNames: [ 'MaterialIcons.ttf' ]
]

apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
```


## Firebase 적용하기

- Firebase를 적용하려면 우선 다음 페이지에서 구글 계정으로 로그인해야 합니다.
- [http://firebase.google.com](http://firebase.google.com)
- 로그인한 뒤 우측 상단의 콘솔로 이동 버튼을 누르면 다음과 같은 화면이 나올 것입니다.

- 프로젝트 만들기 버튼을 누르고 프로젝트 이름을 입력하세요. 여기서 입력할 프로젝트 이름은 고유하지 않아도 상관없기 때문에 여러분 마음대로 입력해도 됩니다. Firebase에 중복된 프로젝트 이름이 이미 등록되어 있다면 자동으로 고유 문자열을 뒤에 붙여서 프로젝트 이름을 고유하게 해줍니다. 