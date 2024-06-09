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

![image1](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/08%20Firebase%EB%A1%9C%20%EC%82%AC%EC%A7%84%20%EA%B3%B5%EC%9C%A0%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/1.png)
> Firebase Console

- 프로젝트 만들기 버튼을 누르고 프로젝트 이름을 입력하세요. 여기서 입력할 프로젝트 이름은 고유하지 않아도 상관없기 때문에 여러분 마음대로 입력해도 됩니다. Firebase에 중복된 프로젝트 이름이 이미 등록되어 있다면 자동으로 고유 문자열을 뒤에 붙여서 프로젝트 이름을 고유하게 해줍니다. 여기에서는 public-gallery라고 입력하겠습니다.
- 계속 버튼을 누르면 Google 애널리틱스를 사용할지 물어볼 텐데 이 프로젝트에서는 필요하지 않으므로 비활성화한 뒤 프로젝트 만들기 버튼을 누르세요. 잠시 기다리면 프로젝트가 생성됩니다.

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/08%20Firebase%EB%A1%9C%20%EC%82%AC%EC%A7%84%20%EA%B3%B5%EC%9C%A0%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/2.png)
> Firebase에서 새 프로젝트 생성하기 

- 이제 Firebase에 프로젝트 정보를 입력하고, 연동해야 합니다.

### 안드로이드에 Firebase 적용하기

- Firebase 페이지를 보면 다음과 같이 ‘앱에 Firebase를 추가하여 시작하기’라는 문구와 함께 버튼들이 있습니다. 두 번째 버튼인 안드로이드 버튼을 누르세요.

![image3](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/08%20Firebase%EB%A1%9C%20%EC%82%AC%EC%A7%84%20%EA%B3%B5%EC%9C%A0%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/3.png)
> 안드로이드에 Firebase 추가하기

- Android 패키지 이름에는 com.publicgallery\<닉네임\>을 입력하세요. \<닉네임\> 부분에는 아까 프로젝트를 생성할 때 맨 뒤에 넣었던 여러분의 닉네임을 넣으면 됩니다. 참고로 프로젝트의 패키지 이름은 android/app/build.gradle 파일에서 applicationId 부분에서 확인할 수 있습니다.
- 두 번째 항목인 앱 닉네임에는 PublicGallery를 입력하세요.
- 세 번째 항목인 디버그 서명 인증서 SHA-1은 프로젝트 경로에서 다음 명령어를 입력해 확인할 수 있습니다.

```
keytool -j-Duser.language=en -list -v -alias androiddebugkey -keystore ./android/app/debug.keystore
```

> keytool 언어 설정
> 
> 이 명령어를 보면 -J-Duser.language=en이라는 옵션을 추가해 키에 대한 정보를 영어로 보여주게 했습니다. 시스템 설정이 한국어로 되어 있다면 기본적으로는 한국어로 설명이 나옵니다.
> 
> 언어 설정을 영어로 지정한 이유는 OpenJDK 8의 keytool에서 더 이상 MD5 지문을 보여주지 않는데, 한국어 설명에는 반영되지 않아 지문의 값이 한 줄 밀려서 SHA1이 있을 자리에 SHA256 값이 나타나는 버그가 있기 때문입니다.

- 명령어를 입력하면 키 비밀번호를 물어볼 텐데 공백 상태로 바로 Enter를 누르세요. 그러면 다음과 같은 내용이 출력됩니다.

```
Alias name: androiddebugkey
Creation date: Jan 1, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, OU=Android, O=Unknown, L=Unknown, ST=Unknown, C=US
Issuer: CN=Android Debug, OU=Android, O=Unknown, L=Unknown, ST=Unknown, C=US
Serial number: 232eae62
Valid from: Wed Jan 01 07:35:04 KST 2014 until: Wed May 01 07:35:04 KST 2052
Certificate fingerprints:
       SHA1: 5E:8F:16:06:2E:A3:CD:2C:4A:0D:54:78:76:BA:A6:F3:8C:AB:F6:25
       SHA256: FA:C6:17:45:DC:09:03:78:6F:B9:ED:E6:2A:96:2B:39:9F:73:48:F0:BB:6F:89:9B:83:32:66:75:91:03:3B:9C
Signature algorithm name: SHA1withRSA (weak)
Subject Public Key Algorithm: 2048-bit RSA key (3)
Version: {10}
```

- 인증서 지문의 SHA1 부분을 복사해 붙여넣으세요. 세 항목을 모두 입력한 뒤 앱 등록 버튼을 누르면 구성 파일 다운로드 섹션이 활성화될 것입니다.

![image4](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/08%20Firebase%EB%A1%9C%20%EC%82%AC%EC%A7%84%20%EA%B3%B5%EC%9C%A0%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/4.png)
> 구성 파일 다운로드

- google-services.json 파일을 다운로드해 android/app 경로에 저장하세요.
- 저장한 뒤 다음을 누르고, 페이지에 적힌 가이드대로 Firebase SDK를 추가합니다. android/build.gradle 파일을 열어서 dependencies 부분에 다음 코드를 추가하세요. 기존 코드에서는classpath를 추가할 때 괄호와 큰따옴표를 사용했는데요. 괄호를 생략해도 되고 작은따옴표를 사용해도 상관없습니다.

> android/build.gradle - dependencies

```groovy
dependencies {
  classpath("com.android.tools.build:gradle:4.2.1")
  classpath 'com.google.gms:google-services:4.3.10'
  // NOTE: Do not place your application dependencies here; they belong
  // in the individual module build.gradle files
}
```

-  android/app/build.gradle 파일의 최상단에 다음 코드를 추가하세요.

> android/app/build.gradle

```groovy
apply plugin: "com.android.application"
apply plugin: 'com.google.gms.google-services'

...

dependencies {
  implementation platform('com.google.firebase:firebase-bom:28.4.0')
  
  ...
  
}

...

```

- 같은 파일에서 defaultConfig를 찾은 뒤 다음 라인을 추가하세요.

> android/app/build.gradle

```groovy
defaultConfig {
  applicationId "com.publicgalleryjohndoe"
  minSdkVersion rootProject.ext.minSdkVersion
  targetSdkVersion rootProject.ext.targetSdkVersion
  versionCode 1
  versionName "1.0"
  multiDexEnabled true
}
```

- 추가한 뒤에는 페이지에서 다음 버튼을 눌러 다시 콘솔 페이지로 이동하세요.

### React Native Firebase 
- React Native Firebase는 리액트 네이티브에서 Firebase를 쉽게 사용할 수 있게 하는 라이브러리입니다. 구글에서 공식 지원하는 라이브러리는 아니지만, Invertase라는 신뢰할 수 있는 기업에서 관리하고, 품질이 높으며 문서화도 잘 되어 있습니다.
-  이 라이브러리의 공식 문서는 다음 링크를 참조하세요. [https://rnfirebase.io](https://rnfirebase.io)
- 다음 명령어로 라이브러리를 설치합니다.

```
yarn add @react-native-firebase/app @react-native-firebase/auth @react-native-firebase/firestore @react-native-firebase/Storage
```
- 이 라이브러리 중 app은 Firebase를 적용할 때 반드시 필요한 라이브러리
- auth는 회원 인증을 위해, firestore는 실시간 데이터베이스를 위해 필요
- storage는 추후 이미지를 업로드할 때 필요한 라이브러리
- 설치가 끝나면 npx pod-install 명령어를 실행하세요.
- 만약 설치 과정에서 다음과 같은 오류가 발생하면,

```
[!] CocoaPods could not find compatible versions for pod "Firebase/CoreOnly":
```

- ios 디렉터리로 이동한 후 다음 명령어를 입력하세요.

```
pod install --repo-update
```

## 화면 구성 이해하기

- 이번에 만들 PublicGallery 앱에는 화면 총 9개, 탭 1개, 스택 3개를 만들 것입니다. 
- 이번 프로젝트는 사용하는 화면이 꽤 많기 때문에 지난 프로젝트처럼 화면을 위한 컴포넌트들을 한꺼번에 미리 만들지 않고, 개발 과정에서 하나씩 생성하겠습니다.
- 각 스택, 탭, 화면의 역할은 다음과 같습니다.

![image5](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/08%20Firebase%EB%A1%9C%20%EC%82%AC%EC%A7%84%20%EA%B3%B5%EC%9C%A0%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/5.png)
> 화면 설계

- RootStack: 프로젝트의 최상위 스택입니다.
- MainTab: 로그인하면 보일 화면입니다.
  - HomeStack: 첫 번째 탭인 홈의 스택입니다.
- FeedScreen: 포스트들의 목록을 볼 수 있는 화면입니다.
- PostScreen: 하나의 포스트를 볼 수 있는 화면입니다.
- ProfileScreen: 특정 사용자의 프로필을 볼 수 있는 화면입니다.
  - MyProfileStack: 두 번째 탭인 프로필의 스택입니다.
- MyProfileScreen: 자신의 프로필을 볼 수 있는 화면입니다.
- PostScreen: 하나의 포스트를 볼 수 있는 화면입니다. HomeStack과 컴포넌트를 공유합니다.
  - UploadScreen: 사진을 업로드하는 화면입니다.
  - ModifyScreen: 포스트의 문구를 수정하는 화면입니다.
  - SettingScreen: 설정 화면입니다.
  - SignInScreen: 로그인 화면입니다.
  - WelcomeScreen: 회원가입 후 프로필 사진과 이름을 설정하는 화면입니다.


# 회원 인증 기능 구현하기 

- 가장 먼저 구현할 기능은 바로 회원 인증입니다. Firebase 덕분에 회원 인증을 간단히 구현할 수 있습니다. Firebase에서는 구글, 페이스북 등의 계정을 사용한 소셜 계정 인증, 이메일/비밀번호 또는 전화번호를 통한 인증을 쉽게 구현할 수 있는데요. PublicGallery 앱에서는 이메일/비밀번호 인증을 사용해보겠습니다.
- Firebase 콘솔을 열고 좌측 사이드바에서 Authentication을 선택하고, 화면에 나타나는 시작하기 버튼을 누르세요. 잠시 기다리면 회원 인증 기능이 활성화됩니다. 회원 인증 기능이 활성화되면 Sign-in method 탭을 열어보세요. 그리고 이메일/비밀번호를 선택하고 사용 설정을 활성화하세요. 활성화하고 하단의 저장 버튼을 눌러야 합니다.

![image6](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/08%20Firebase%EB%A1%9C%20%EC%82%AC%EC%A7%84%20%EA%B3%B5%EC%9C%A0%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/6.png)
> 이메일/비밀번호 로그인 활성화

- 다음으로 로그인에 사용할 SignInScreen 화면 컴포넌트를 만들어봅시다.

> screens/SignInScreen.js

```jsx
import React from 'react';
import {StyleSheet, Text} from 'react-native';
import {SafeAreaView} from 'react-native-safe-area-context';

function SignInScreen() {
  return (
    <SafeAreaView style={styles.fullscreen}>
      <Text style={styles.text}>PublicGallery</Text>
    </SafeAreaView>  
  );
}

const styles = StyleSheet.create({
  fullscreen: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    fontSize: 32,
    fontWeight: 'bold',
  },
});

export default SignInScreen;
```

- 이 컴포넌트는 중앙에 PublicGallery 텍스트를 보여줍니다. 헤더가 존재하지 않는 화면이기 때문에 iOS에서 상태 바 영역을 침범하지 않도록 SafeAreaView를 사용해야 합니다.
- 컴포넌트를 만든 다음에는 이 화면을 RootStack에 등록하세요.

> screens/RootStack.js

```jsx
import React from 'react';
import {createNativeStackNavigator} from '@react-navigation/native-stack';
import SignInScreen from './SignInScreen';

const Stack = createNativeStackNavigator();

function RootStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen 
        name="SignIn"
        component={SignInScreen}
        options={{headerShown: false}}
      />
    </Stack.Navigator>
  );
}

export default RootStack;
```

- 이제 yarn ios와 yarn android 명령어를 입력해 시뮬레이터를 실행하세요.

![image7](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/08%20Firebase%EB%A1%9C%20%EC%82%AC%EC%A7%84%20%EA%B3%B5%EC%9C%A0%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/7.png)
> SignInScreen 초기 화면

## 회원 인증을 위한 UI 준비하기

- 이 화면에 인풋 두 개와 버튼을 보여주겠습니다. 이 과정에서 테두리가 있는 인풋인 BorderInput 컴포넌트와 임의의 스타일을 가진 CustomButton 컴포넌트를 만들겠습니다. 이 UI들을 한 번만 사용한다면 굳이 새로 컴포넌트를 만들 필요가 없겠지만, 재사용할 것이므로 따로 분리해주는 것입니다.
- 프로젝트의 최상위 디렉터리에 components 디렉터리를 만들고 다음 컴포넌트들을 작성하세요.

> components/BorderedInput.js

```jsx
import React from 'react';
import {StyleSheet, TextInput} from 'react-native';

function BorderedInput({hasMarginBottom}) {
  return <TextInput style={[styles.input, hasMarginBottom && styles.margin]} />;
}

const styles = StyleSheet.create({
  input: {
    borderColor: '#bdbdbd',
    borderWidth: 1,
    paddingHorizontal: 16,
    borderRadius: 4,
    height: 48,
    backgroundColor: 'white',
  },
  margin: {
    marginBottom: 16,
  }
});

export default BorderedInput;
```

- 이 컴포넌트에서 hasMarginBottom 값이 true라면 하단에 여백을 지정해줬습니다. 다음 컴포넌트도 hasMarginBottom Props로 하단 여백을 지정할 수 있고, onPress와 title로 버튼을 클릭했을 때 실행할 함수와 버튼의 이름을 지정할 수 있습니다.

> components/CustomButton.js

```jsx
import React from 'react';
import {StyleSheet, View, Pressable, Text, Platform} from 'react-native';

function CustomButton({onPress, title, hasMarginBottom}) {
  return (
    <View style={[styles.block, styles.overflow, hasMarginBottom && styles.margin]}>
      <Pressable
        onPress={onPress}
        style={({pressed}) => [
          styles.wrapper,
          Platform.OS === 'ios' && pressed && {opacity: 0.5},
        ]}
        android_ripple={{
          color: '#ffffff',
        }}>
        <Text style={[styles.text]}>{title}</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  overflow: {
    borderRadius: 4,
    overflow: 'hidden',
  },
  wrapper: {
    borderRadius: 4,
    height: 48,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#6200ee',
  },
  text: {
    fontWeight: 'bold',
    fontSize: 14,
    color: 'white',
  },
  margin: {
    marginBottom: 8,
  }
});

export default CustomBotton;
```

- iOS에서 버튼을 누르면 투명도가 조정되고, 안드로이드에서 버튼을 누르면 물결 효과가 나타납니다. 컴포넌트를 다 만들었다면 SignInScreen에서 사용해보세요.