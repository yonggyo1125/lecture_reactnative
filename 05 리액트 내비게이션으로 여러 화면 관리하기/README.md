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

## 라이브러리 적용하기

- 리액트 네이티브 프로젝트에 라이브러리를 적용하기 위해서는 @react-navigation/native에서 NavigationContainer 컴포넌트를 불러와 사용해 앱 전체를 감싸주어야 합니다.
- App 컴포넌트를 열어 파일 내용을 모두 지우고 다음과 같이 코드를 작성하세요

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";

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

![image1](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/1.png)

> 스택 자료구조

## 네이티브 스택 내비게이터

- 리액트 내비게이션 라이브러리에는 다른 상황에 사용할 수 있는 다양한 내비게이터가 있습니다.
- 그 중 네이티브 스택 내비게이터(Native Stack Navigator)는 가장 흔히 사용되며 안드로이드에서는 Fragment를, iOS에서는 UINavigationController를 사용해 일반 네이티브 앱과 정확히 동일한 방식으로 화면을 관리합니다.
- 네이티브 스택 내비게이터를 사용하기 위해서는 라이브러리를 추가적으로 설치해주어야 합니다.

```
yarn add @react-navigation/native-stack
```

- 라이브러리를 설치한 다음에는 화면으로 사용할 컴포넌트 두 개를 만들어볼 텐데 화면 전용 컴포넌트는 screens라는 디렉터리를 만들어 그 안에 저장할 것입니다.
- 이렇게 screens라는 디렉터리에 컴포넌트를 분류해 저장하면 화면 전용 컴포넌트를 모아서 관리할 수 있기 때문에 편리합니다. 참고로 반드시 screens 디렉터리를 분류할 필요는 없습니다.
- 첫 번째로 만들 컴포넌트는 HomeScreen입니다.

> screens/HomeScreen.js

```jsx
import React from "react";
import { View, Button } from "react-native";

function HomeScreen({ navigation }) {
  return (
    <View>
      <Button title="Detail 열기" />
    </View>
  );
}

export default HomeScreen;
```

- 화면 전용 컴포넌트의 이름을 지을 때 꼭 Screen으로 끝날 필요는 없지만, 이렇게 이름을 지어 놓으면 추후 중복되는 컴포넌트 이름 때문에 헷갈리는 일을 방지할 수 있습니다.
- 예를 들어, 사용자 프로필에 관련한 기능을 구현할 때 ProfileScreen 화면 전용 컴포넌트와 Profile이라는 UI 전용 컴포넌트가 둘 다 열려있어도 어떤 게 화면 전용 컴포넌트인지 에디터에서 쉽게 구분할 있습니다.
- 지금 이 컴포넌트에 ‘Detail 열기’라는 Button이 있으나 아직 버튼을 눌렀을 때 호출되는 onPress는 설정하지 않았습니다.
- 그다음 만들 컴포넌트는 DetailScreen입니다. 이 화면은 HomeScreen에 있는 버튼을 눌렀을 때 보일 화면입니다.

> screens/DetailScreen.js

```jsx
import React from "react";
import { View, Text } from "react-native";

function DetailScreen() {
  return (
    <View>
      <Text>Detail</Text>
    </View>
  );
}

export default DetailScreen;
```

- 화면 전용 컴포넌트가 모두 준비됐습니다.
- 네이티브 스택 내비게이터는 createNativeStackNavigator라는 함수를 사용해 만들 수 있습니다.
- App 컴포넌트를 다음과 같이 수정해주세요.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import HomeScreen from "./screens/HomeScreen";
import DetailScreen from "./screens/DetailScreen";

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Detail" component={DetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

- createNativeStackNavigator 함수를 사용하면 Stack이라는 객체가 만들어집니다. 이 안에 Stack.Navigator라는 컴포넌트와 Stack.Screen 컴포넌트가 들어있습니다.
- Stack.Navigator 컴포넌트는 NavigationContainer 사이에 넣어야 정상적으로 작동합니다. 그리고 Stack.Screen 컴포넌트를 사용해 각 화면을 설정할 수 있습니다.
- 이 컴포넌트에 설정된 name은 화면의 이름을 설정하는 Props입니다. 이 값은 다른 화면으로 이동하거나 현재 화면이 어떤 화면인지 조회할 때 사용합니다(반드시 대문자로 시작할 필요는 없습니다만, 공식 문서에서는 대문자로 시작하는 것을 권장(prefer)하고 있습니다).
- 코드를 보면 Stack.Navigator에 initialRouteName이라는 Props가 "Home"으로 설정되어 있습니다. 이는 네이티브 스택 내비게이터에서 기본적으로 보여줄 화면의 이름(초기 화면)을 가리킵니다. 만약 이 값을 설정하지 않으면 기본적으로 내비게이터 안에 들어있는 첫 번째 화면(이 경우에는 Home)이 보여집니다.

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/2.png)

> 네이티브 스택 내비게이터 설정 후 초기 화면

- Home 타이틀과 버튼이 나타났지만 iOS와 안드로이드에서 타이틀이 보이는 영역이 조금 다르답니다.

## 스크린 이동하기

- 스크린으로 사용된 컴포넌트는 navigation이라는 객체를 Props로 받아올 수 있습니다. 이 객체를 사용해 다음과 같이 다른 화면으로 이동할 수 있습니다.

```
navigation.navigate('Detail');
```

- HomeScreen 컴포넌트를 다음과 같이 수정해보세요.

> screens/HomeScreen.js

```jsx
import React from "react";
import { View, Button } from "react-native";

function HomeScreen({ navigation }) {
  return (
    <View>
      <Button
        title="Detail 열기"
        onPress={() => navigation.navigate("Detail")}
      />
    </View>
  );
}

export default HomeScreen;
```

- Detail 열기 버튼을 눌렀을 때 Detail 화면으로 이동하도록 설정했습니다. 파일을 저장한 후 버튼을 눌러보세요.

![image3](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/3.png)

> Detail 화면

- 기본적으로, iOS에서는 우측에서 좌측으로 나타나는 효과로 새 화면이 보일 것이고, 안드로이드에서는 아래에서 위로 나타나는 효과로 새 화면이 보일 것입니다.
- iOS와 안드로이드의 화면을 보면 이번에도 생김새가 조금 다릅니다. iOS에서는 홑화살괄호와 이전 화면의 이름이 함께 보이고, 안드로이드에서는 화살표 아이콘만 있습니다.
- 네이티브 스택 내비게이터를 사용할 때는 navigate 함수 말고도 push 함수를 사용해 화면 전환할 수 있습니다.

```jsx
<Button title="Detail 열기" onPress={() => navigation.push("Detail")} />
```

## 라우트 파라미터

- 새로운 화면을 보여줄 때 의존해야 하는 어떤 값이 있으면 라우트 파라미터를 설정합니다.
- 라우트 파라미터는 객체 타입으로 설정합니다. 예를 들어, 계정명이 'john'인 프로필 화면을 보여주고 싶다면 {username: 'john'}으로 파라미터를 설정합니다.
- id 값이 1인 데이터를 불러와서 화면에 보여주고 싶을 때는 {id: 1}과 같은 형식으로 파라미터를 설정합니다. 참고로 라우트 파라미터 객체에는 값을 1개 이상 담아도 됩니다.
- 라우트 파라미터를 설정해 화면을 전환할 때는 navigate 또는 push 함수의 두 번째 인자로 객체를 설정해줍니다.
- navigate 예시

```jsx
navigation.navigate("Detail", { id: 1 });
```

- push 예시

```jsx
navigation.push("Detail", { id: 2 });
```

- HomeScreen 컴포넌트를 다음과 같이 수정해보세요.

> screens/HomeScreen.js

```jsx
import React from "react";
import { View, Button } from "react-native";

function HomeScreen({ navigation }) {
  return (
    <View>
      <Button
        title="Detail 1 열기"
        onPress={() => navigation.push("Detail", { id: 1 })}
      />
      <Button
        title="Detail 2 열기"
        onPress={() => navigation.push("Detail", { id: 2 })}
      />
      <Button
        title="Detail 3 열기"
        onPress={() => navigation.push("Detail", { id: 3 })}
      />
    </View>
  );
}

export default HomeScreen;
```

![image4](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/4.png)

- 다음으로 DetailScreen 컴포넌트에서 라우트 파라미터를 조회해 화면에 나타내겠습니다.

> screens/DetailScreen.js

```jsx
import React from "react";
import { View, Text, StyleSheet } from "react-native";

function DetailScreen({ route }) {
  return (
    <View style={styles.block}>
      <Text style={styles.text}>id: {route.params.id}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center",
  },
  text: {
    fontSize: 48,
  },
});

export default DetailScreen;
```

- 스크린으로 사용된 컴포넌트는 navigation 외에 route라는 Props도 받아옵니다. 이 Props는 객체 타입인데요. 안에는 다음과 같은 정보가 들어있습니다.

```json
{
  "key": "Detail-vgDx8-H-8e7oao6a3xJz7",
  "name": "Detail",
  "params": { "id": 1 }
}
```

- key는 화면의 고유 ID로 새로운 화면이 나타날 때 자동으로 생성됩니다.
- name은 화면의 이름으로 App 컴포넌트에서 네이티브 스택 내비게이터를 설정할 때 지정한 이름입니다.
- params가 바로 우리가 화면 전환 시 지정한 라우트 파라미터입니다.

- 이제 Home 화면에서 버튼을 각 버튼을 눌렀을 때 화면에 다른 id가 나타나는지 확인해보세요.

![image5](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/5.png)

> 라우트 파라미터

- 이전에 화면 전환할 때 navigate 함수도 사용할 수 있고, push 함수도 사용할 수 있지만 두 함수는 차이점이 있습니다.
- DetailScreen 컴포넌트를 사용하겠습니다. 먼저 push 함수를 사용해 화면 전환해 보겠습니다.

> screens/DetailScreen.js

```jsx
import React from "react";
import { View, Text, StyleSheet, Button } from "react-native";

function DetailScreen({ route, navigation }) {
  return (
    <View style={styles.block}>
      <Text style={styles.text}>id: {route.params.id}</Text>
      <Button
        title="다음"
        onPress={() => navigation.push("Detail", { id: route.params.id + 1 })}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center",
  },
  text: {
    fontSize: 48,
  },
});

export default DetailScreen;
```

![image6](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/6.png)

> 다음 버튼

- 버튼을 누르면 새로운 화면이 스택에 쌓여가면서 화면이 전환될 것입니다. 즉, 다음 버튼을 누르면 다른 Detail 화면이 열리고, 안드로이드 하단의 뒤로가기 버튼 또는 타이틀 영역의 뒤로가기 버튼을 누르면 이전 Detail 화면이 열립니다.
- navigate는 push와 달리 새로 이동할 화면이 현재 화면과 같으면 새로운 화면을 쌓지 않고 파라미터만 변경합니다.
- 코드를 다음과 같이 변경해보세요. 그리고 앱을 완전히 리로딩(iOS에서는 X + R, 안드로이드에서는 R을 두 번)해 네이티브 스택 내비게이터를 초기화한 후 Detail 화면에 들어가서 다음 버튼을 눌러보세요.

> screens/DetailScreen.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, Button} from 'react-native';

function DetailScreen({route, navigation}) {
    return (
        <View style={styles.block}>
            <Text style={styles.text}>id: {route.params.id}</Text>
            <Button
                title="다음"
                onPress={() => navigation.navigate('Detail', {id: route.params.id + 1 })}
            />
        </View>
    );
}

...

```

- 이제 다음 버튼을 눌렀을 때 화면 전환 효과가 나타나지 않고, 다음 버튼을 여러 번 눌러도 뒤로가기 한 번이면 바로 Home 화면으로 이동할 것입니다.
- 참고로 navigate 함수는 지금 사용하고 있는 네이티브 스택 내비게이터 외에 다른 내비게이터(추후 등장)에도 있지만, push 함수는 네이티브 스택 내비게이터에서만 사용 가능합니다.

### 뒤로가기

- 뒤로가기를 할 때는 navigation.pop() 함수를 호출하면 됩니다.
- 가장 첫 번째 화면으로 이동하고 싶을 때는 navgiation.popToTop() 함수를 호출합니다.
- DetailScreen 컴포넌트를 다음과 같이 수정해주세요.

> screens/DetailsScreen.js

```jsx
import React from "react";
import { View, Text, StyleSheet, Button } from "react-native";
function DetailScreen({ route, navigation }) {
  return (
    <View style={styles.block}>
      <Text style={styles.text}>id: {route.params.id}</Text>
      <View style={styles.buttons}>
        <Button
          title="다음"
          onPress={() => navigation.push("Detail", { id: route.param.id + 1 })}
        />
        <Button title="뒤로가기" onPress={() => navigation.pop()} />
        <Button title="처음으로" onPress={() => navigation.popToTop()} />
      </View>
    </View>
  );
}

cont styles = StyleSheet.create({
   block: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
   },
   text: {
    fontSize: 48,
   },
   buttons: {
    flexDirection: 'row',
   },
});

export default DetailScreen;
```

- 뒤로가기 버튼과 처음으로 버튼은 한 줄에 나타날 수 있도록 View로 감싸고 스타일에 flexDirection 값을 'row'로 설정해줬습니다.

![image7](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/7.png)

> 뒤로가기, 처음으로 버튼

## 헤더 커스터마이징하기

- react-navigation에서는 타이틀 영역을 헤더(Header)라고 부릅니다.

### 타이틀 텍스트 변경하기

- 먼저 Home 화면의 타이틀 텍스트를 변경해보겠습니다. 영어로 Home이라고 되어 있는데 ‘홈’이라고 한글로 나타나게 변경해봅시다.
- 헤더를 커스터마이징하는 방법은 두 가지가 있는데, 첫 번째 방법은 Stack.Screen의 Props로 설정하는 것입니다. App 컴포넌트를 다음과 같이 수정해보세요.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import HomeScreen from "./screens/HomeScreen";
import DetailScreen from "./screens/DetailScreen";

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteHome="Home">
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{
            title: "홈",
          }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

- 헤더를 커스터마이징하는 두 번째 방법은 화면 컴포넌트에서 navigation.setOptions 함수를 사용하는 것입니다. HomeScreen 컴포넌트를 열어 코드를 다음과 같이 변경해보세요

> screens/HomeScreen.js

```jsx
import React, { useEffect } from "react";
import { View, Button } from "react-native";

function HomeScreen({ navigation }) {
  useEffect(() => {
    navigation.setOptions({ title: "홈" });
  }, [navigation]);
}

export default HomeScreen;
```

- 여기서 useEffect Hook을 사용했습니다. 이 Hook은 특정 값이 바뀔 때 또는 컴포넌트가 화면에 나타났을 때 우리가 원하는 작업을 할 수 있습니다.
- 현재 이 함수의 deps(두 번째 인자에 넣는 배열)에는 navigation이 들어있는데요. 이 객체가 바뀌는 일은 없지만 ESLint(자바스크립트 검사 도구) 규칙상 useEffect 내부에 사용하는 값을 꼭 deps에 넣어야 하기 때문에 포함시켰습니다.
- 이렇게 하면 컴포넌트가 처음 화면에 나타난 다음에 navgiation.setOptions 함수를 호출해 타이틀 텍스트를 변경합니다. 참고로 useEffect를 통해서 설정한 내비게이션 option은 App 컴포넌트에서 Props를 통해 설정한 option을 덮어쓰게 됩니다.
- 이번에는 Detail 화면의 타이틀 텍스트를 변경해봅시다. 이 화면의 타이틀 텍스트에는 라우터 파라미터로 받아온 id 값을 포함하며, 라우터 파라미터를 참조할 때도 방금 한 것처럼 App 컴포넌트에서 Props로 설정할 수도 있고 useEffect를 사용해 화면으로 사용하는 컴포넌트에서 직접 설정할 수도 있습니다. 다만 App 컴포넌트에서 Props로 설정할 때 파라미터를 조회하려면 객체가 아닌 객체를 반환하는 함수를 넣어줘야 합니다. 그리고 이 함수는 route와 navigation을 파라미터로 받아올 수 있습니다.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import HomeScreen from "./screens/HomeScreen";
import DetailScreen from "./screens/DetailScreen";

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{
            title: "홈",
          }}
        />
        <Stack.Screen
          name="Detail"
          component={DetailScreen}
          options={({ route }) => ({
            title: `상세 정보 - ${route.params.id}`,
          })}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

![image8](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/8.png)

> Detail 타이틀 텍스트 변경

- 이번에는 DetailScreen 컴포넌트에서 내비게이션을 설정해보겠습니다. 이전에 HomeScreen 컴포넌트를 변경한 것과 유사합니다.

> screens/DetailScreen.js

```jsx
import React, {useEffect} from 'react';
import {View, Text, StyleSheet, Button} from 'react-native';

function DetailScreen({route, navigation}) {
    useEffect(() => {
        navigation.setOptions({
            title: `상세 정보 - ${route.params.id}`,
        });
    }, [navigation, route.params.id]);

    return ...
}

...

```

- 이번에는 useEffect 내에서 route.params.id를 사용하기 때문에 deps 배열에 해당 값도 포함했습니다. App 컴포넌트에서 Detail 화면에 설정한 options를 지워도 정상적으로 타이틀 텍스트가 설정되는지 확인해보세요.

### 헤더 스타일 변경하기

- 헤더의 스타일도 세부적으로 커스터마이징할 수 있습니다. 다음은 Home 화면의 헤더 스타일을 커스터마이징하는 예시 코드입니다.
- 다음과 같이 코드를 수정해 헤더의 색상을 변경해보세요.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import HomeScreen from "./screens/HomeScreen";
import DetailScreen from "./screens/DetailScreen";

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{
            title: "홈",
            // Header 블록에 대한 스타일
            headerStyle: {
              backgroundColor: "#29b6f6",
            },
            // Header의 텍스트 버튼들 색상
            headerTintColor: "#ffffff",
            headerTitleStyle: {
              fontWeight: "bold",
              fontSize: 20,
            },
          }}
        />
        <Stack.Screen name="Detail" component={DetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

![image9](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/9.png)

> 헤더 스타일 변경

### 헤더의 좌측, 타이틀, 우측 영역에 다른 컴포넌트 보여주기

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import {createNativeStackNavigator} from '@react-navigation/native-stack';
import HomeScreen from './screens/HomeScreen';
import DetailScreen from './screens/DetailScreen';
import {View, Text, TouchableOpacity} from 'react-native';

const Stack = createNativeStackNavigator();

function App() {
    return (
        <NavigationContainer>
            <Stack.Navigator initialRouteName="Home">
                <Stack.Screen
                    name="Home"
                    ...
                />
                <Stack.Screen
                    name="Detail"
                    component={DetailScreen}
                    options={{
                        headerLeft: ({onPress}) => (
                            <TouchableOpacity onPress={onPress}>
                                <Text>Left</Text>
                            </TouchableOpacity>
                        ),
                        headerTitle: ({children}) => (
                            <View>
                                <Text>{children}</Text>
                            </View>
                        ),
                        headerRight: () => (
                            <View>
                                <Text>Right</Text>
                            </View>
                        ),
                    }}
                />
            </Stack.Navigator>
        </NavigationContainer>
    );
}

export default App;
```

- headerLeft, headerTitle, headerRight에 함수 컴포넌트를 넣어 헤더 내부 영역에 보여줄 컴포넌트를 직접 설정할 수 있습니다. 이 세 가지 옵션을 사용하면 우리가 원하는 UI를 마음대로 헤더에 보여줄 수 있습니다.
- 여기서 headerTitle에 넣은 컴포넌트를 보면 children이라는 Props를 받아오고 있는데, 현재 이 값은 화면의 타이틀을 가리킵니다

![image10](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/10.png)

> 헤더의 좌측, 타이틀, 우측 영역에 다른 컴포넌트 보여주기

- iOS와 안드로이드의 화면이 다릅니다. 안드로이드에선 좌측에 화살표 아이콘이 나타나고 있습니다. 만약 이 버튼을 없애고 싶다면, headerBackVisible 옵션을 false로 지정하면 됩니다.

> App.js - DetailScreen

```jsx
<Stack.Screen
    name="Detail"
    component={DetailScreen}
    options={{
        headerBackVisible: false,
        headerLeft: ({onPress}) => ( ...
        ...
    }}
    ...
/>
```

![image11](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/11.png)

> 안드로이드에서 화살표 아이콘 없애기

### 헤더 숨기기

- 이번에는 헤더가 없는 화면을 만들어보겠습니다. screens 디렉터리에 HeaderlessScreen.js 파일을 생성해 다음과 같이 코드를 작성하세요.

> screens/HeaderlessScreen.js

```jsx
import React from "react";
import { View, Text, Button } from "react-native";

function HeaderlessScreen({ navigation }) {
  return (
    <View>
      <Text>Header가 없네?</Text>
      <Button onPress={() => navigation.pop()} title="뒤로가기" />
    </View>
  );
}

export default HeaderlessScreen;
```

- iOS에서는 헤더가 없으면 뒤로 갈 방법이 없어서 뒤로가기를 호출하는 버튼을 추가했습니다.
- 그다음에는 App에서 화면 설정해주세요.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigation } from "@react-navigation/native-stack";
import HomeScreen from "./screens/HomeScreen";
import DetailScreen from "./screens/DetailScreen";
import { View, Text, TouchableOpacity } from "react-native";
import HeaderlessScreen from "./screens/HeaderlessScreen";

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        ...
        <Stack.Screen
          name="Headerless"
          component={HeaderlessScreen}
          options={{
            headerShown: false,
          }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

- 이제 HomeScreen 컴포넌트를 열어 Headerless 화면을 열어주는 버튼을 만들어주세요.

> screens/HomeScreen.js

```jsx
import React, {useEffect} from 'react';
import {View, Button} from 'react-native';

function HomeScreen({navigation}) {
    useEffect(() => {
        navigation.setOptions({title: '홈'});
    }, [navigation);

    return (
        <View>
            ...
            <Button
                title="Headerless 열기"
                onPress={() => navigation.push('Headerless')}
            />
        </View>
    );
}

export default HomeScreen;
```

![image12](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/12.png)

> 헤더가 없는 화면

- 안드로이드에서는 문제없이 잘 나타나는데 iOS에서는 StatusBar 영역을 침범해서 화면이 나타나고 있습니다.
- 이럴 때는 SafeAreaView 컴포넌트를 사용하면 됩니다.
- react-navigation에 react-native-safe-area-context가 내장되어 있기 때문에 react-native가 아닌 react-native-safe-area-context에서 불러와도 상관없습니다.
- HeaderlessScreen을 다음과 같이 수정해보세요.

> screens/HeaderlessScreen.js

```jsx
import React from "react";
import { View, Text, Button } from "react-native";
import { SafeAreaView } from "react-native-safe-area-context";

function HeaderlessScreen({ navigation }) {
  return (
    <SafeAreaView>
      <View>
        <Text>Header가 없네?</Text>
        <Button onPress={() => navigation.pop()} title="뒤로가기" />
      </View>
    </SafeAreaView>
  );
}

export default HeaderlessScreen;
```

![image13](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/13.png)

> SafeAreaView 적용

- 만약 헤더와 관련한 설정을 특정 화면에만 적용하지 않고, 네이티브 스택 내비게이터에서 관리하는 모든 화면에 넣고 싶다면 Stack.Navigator에 screenOptions라는 Props를 설정하면 됩니다.
- 이 Props에 넣는 값은 Stack.Screen의 options와 같습니다. 즉, 모든 화면에서 헤더를 없애고 싶다면 다음과 같이 하면 됩니다.

> App.js - Stack.Navigator

```jsx
<Stack.Navigator
    initialRouteName="Home"
    screenOptions={{
        headerShown: false,
    }}>
...

```

- 헤더를 커스터마이징할 수 있는 설정은 지금까지 설정해본 것 외에도 많습니다. 더 자세한 내용은 공식 문서를 참고
- https://reactnavigation.org/docs/native-stack-navigator#options

---

# 다양한 내비게이터

## 드로어 내비게이터

- 드로어 내비게이터(Drawer Navigator)는 좌측 또는 우측에 사이드바를 만들고 싶을 때 사용하는 내비게이터입니다.
- 사이드바를 모바일 앱에서는 드로어(Drawer)라고 부릅니다.

![image14](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/14.png)

> 드로어 내비게이터

- 내비게이터를 사용하려면 먼저 다음과 같이 라이브러리를 설치하세요.

```
yarn add @react-navigation/drawer react-native-gesture-handler react-native-reanimated
cd ios
pod install
```

- react-native-gesture-handler는 드로어 내비게이터에서 사용자 제스처를 인식하기 위하여 내부적으로 사용하는 라이브러리입니다.
- react-native-reanimated는 리액트 네이티브에 내장된 애니메이션 효과 기능보다 더욱 개선된 성능으로 애니메이션 효과를 구현해주는 라이브러리입니다.
- 설치 이후에는 yarn ios와 yarn android 명령어를 다시 입력하여 시뮬레이터를 다시 시작해주세요.
- 그다음에는 App 컴포넌트를 열어 코드를 다음과 같이 수정해주세요. 이전에 만든 HomeScreen과 DetailScreen은 더 이상 사용하지 않겠습니다. 이전에 사용한 navigation.push, navigation.pop 같은 기능들은 드로어 내비게이터에서 호환되지 않습니다.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createDrawerNavigator } from "@react-navigation/drawer";
import { View, Text, Button } from "react-native";

const Drawer = createDrawerNavigator();

function HomeScreen({ navigation }) {
  return (
    <View>
      <Text>Home</Text>
      <Button title="Drawer 열기" onPress={() => navigation.openDrawer()} />
      <Button
        title="Setting 열기"
        onPress={() => navigation.navigate("Setting")}
      />
    </View>
  );
}

function SettingScreen({ navigation }) {
  return (
    <View>
      <Text>Setting</Text>
      <Button title="뒤로가기" onPress={() => navigation.goBack()} />
    </View>
  );
}

function App() {
  return (
    <NavigationContainer>
      <Drawer.Navigator
        initialRouteName="Home"
        drawerPosition="left"
        backBehavior="history"
      >
        <Drawer.Screen name="Home" component={HomeScreen} />
        <Drawer.Screen name="Setting" component={SettingScreen} />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

> 이와 같이 편의상 파일 하나에 여러 개의 컴포넌트를 선언해도 상관없습니다. 다만 한 파일에 너무 많은 컴포넌트를 선언하면 나중에 유지보수하는 과정에서 컴포넌트 찾기가 번거로울 수도 있습니다. 서로 관련된 컴포넌트이거나, 지금처럼 연습하는 상황이 아니라면 이렇게 파일 하나에 여러 컴포넌트를 선언하지 않는 것을 권장합니다.

- 이전에 네이티브 스택 내비게이터를 사용했을 때와 사용법이 꽤 비슷합니다. createDrawerNavigator 함수를 만들어 Drawer 객체를 만듭니다. 이 안에는 Navigator와 Screen이 들어있는데 이를 컴포넌트로 사용하면 됩니다.
- Drawer.Navigator에는 initialRouteName Props를 설정했는데요. 내비게이터에서 기본적으로 보여줄 화면의 이름입니다. 만약 이 값이 없다면 맨 위에 있는 화면(Home)이 뜹니다.
- drawerPosition Props는 드로어가 나타나는 위치를 정합니다. 값을 "left" 또는 "right"로 지정할 수 있습니다. 따로 값을 지정하지 않으면 기본값은 "left"로 설정됩니다.
- backBehavior Props는 뒤로가기를 할 때 어떻게 작동할지 설정합니다. 이 Props에 지정할 수 있는 값은 다음과 같습니
  - initialRoute: 가장 첫 번째 화면을 보여줍니다.
  - order: Drawer.Screen 컴포넌트를 사용한 순서에 따라 현재 화면의 이전 화면을 보여줍니다.
  - history: 현재 화면을 열기 직전에 봤던 화면을 보여줍니다.
  - none: 뒤로가기를 수행하지 않습니다.
- 기본적으로 화면의 좌측(drawerPosition을 "right"로 했다면 우측) 끝에서 중앙으로 스와이프하면 드로어가 나타납니다. 만약 원하는 상황에 직접 드로어를 보여주고 싶다면 navigation을 화면으로 사용된 컴포넌트의 Props로 받아와서 navigation.openDrawer 함수를 호출해주면 됩니다.
- 사용자 행동(예를 들어, 버튼 클릭)에 따라 다른 화면으로 이동하고 싶을 때는 navigation.navigate 함수를 사용하고, 뒤로가기를 하고 싶을 때는 navigation.goBack 함수를 호출하면 됩니다.

### 드로어 커스터마이징하기

- 우선 드로어에 나오는 화면 이름은 네이티브 스택 내비게이터를 사용했을 때처럼 Screen 컴포넌트에 options Props를 통해 title 값을 지정하면 됩니다.

> App.js

```jsx
...

function App() {
    return (
        <NavigationContainer>
            <Drawer.Navigator
                initialRouteName="Home"
                drawerPosition="left"
                backBehavior="history"
            >
                <Drawer.Screen
                    name="Home"
                    component={HomeScreen}
                    options={{title: '홈'}}
                />
                <Drawer.Screen
                    name="Setting"
                    component={SettingScreen}
                    options={{title: '설정'}}
                />
            </Drawer.Navigator>
        </NavigationContainer>
    );
}

export default App;
```

![image15](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/15.png)

> 드로어의 화면 이름 변경

- 드로어의 스타일을 변경하고자 할 때는 다음과 같이 Drawer.Navigator 컴포넌트에 screenOptions Props를 설정하면 됩니다. 이 Props는 화면을 설정할 수 있는데 드로어에 관한 옵션은 drawer로 시작합니다. 다음 값들이 들어있는 객체를 설정하면 됩니다.
  - drawerActiveTintColor: 활성화된 항목의 텍스트 색상
  - drawerActiveBackgroundColor: 활성화된 항목의 배경색
  - drawerInactiveTintColor: 비활성화된 항목의 텍스트 색상
  - drawerInactiveBackgroundColor: 비활성화된 항목의 배경색
  - drawerItemStyle: 항목의 스타일
  - drawerLabelStyle: 항목 내부의 텍스트 스타일
  - drawerContentContainerStyle: 항목들을 감싸고 있는 영역의 스타일
  - drawerStyle: 전체 드로어를 감싸고 있는 영역의 스타일
- 이 Props를 사용해 활성화된 항목의 배경색을 주황색으로 바꿔보겠습니다.

> App.js

```jsx
...

function App() {
  return (
    <NavigationContainer>
      <Drawer.Navigator
        initialRouteName="Home"
        drawerPosition="left"
        backBehavior="history"
        screenOptions={{
          drawerActiveBackgroundColor: '#fb8c00',
          drawerActiveTintColor: 'white',
        }}>
        <Drawer.Screen
          name="Home"
          component={HomeScreen}
          options={{title: '홈'}}
        />
        <Drawer.Screen
          name="Setting"
          component={SettingScreen}
          options={{title: '설정'}}
        />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

![image16](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/16.png)

> 드로어의 스타일 변경

- 만약 Drawer 컴포넌트가 나타나는 영역에 아예 다른 UI를 보여주고 싶다면 drawerContent Props에 함수 컴포넌트를 넣어주면 됩니다.

> App.js

> App.js

```jsx
...
import {SafeAreaView} from 'react-native-safe-area-context';

...

function App() {
  return (
    <NavigationContainer>
      <Drawer.Navigator
        initialRouteName="Home"
        drawerPosition="left"
        backBehavior="history"
        drawerContent={({navigation}) => (
          <SafeAreaView>
            <Text>A Custom Drawer</Text>
            <Button
              onPress={() => navigation.closeDrawer()}
              title="Drawer 닫기"
            />
          </SafeAreaView>
        )}>
        <Drawer.Screen
          name="Home"
          component={HomeScreen}
          options={{title: '홈'}}
        />
        <Drawer.Screen
          name="Setting"
          component={SettingScreen}
          options={{title: '설정'}}
        />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

- iOS도 지원하는 앱이라면 drawerContent를 지정할 때 SafeAreaView를 꼭 사용해주세요. 그렇지 않으면 드로어의 상단부 영역이 StatusBar 영역과 겹치게 됩니다.
- drawerContent에 넣는 함수 컴포넌트에서도 navigation을 사용할 수 있습니다. navigation.closeDrawer 함수는 현재 보여지는 드로어를 닫아줍니다.

![image17](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/17.png)

> drawerContent 설정

- 드로어 내비게이터를 사용하면 네이티브 스택 내비게이터와 비슷하게 화면 상단에 헤더가 나타납니다.
- 만약 헤더 좌측에 보여지는 햄버거 버튼을 바꾸고 싶다면 Drawer.Screen의 options에서 headerLeft에 컴포넌트를 설정하면 됩니다.

> App.js - 첫 번째 Drawer.Screen

```jsx
<Drawer.Screen
  name="Home"
  component={HomeScreen}
  options={{ title: "홈", headerLeft: () => <Text>Left</Text> }}
/>
```

- 만약 모든 화면에 공통으로 헤더의 커스터마이징을 적용하려면 Drawer.Navigator의 screenOptions Props에 헤더와 관련해 설정하면 됩니다. 다음과 같이 설정하면 모든 화면에서 헤더가 사라집니다.

> App.js - Drawer.Navigator

```jsx
<Drawer.Navigator
  initialRouteName="Home"
  drawerPosition="left"
  backBehavior="history"
  screenOptions={{
    headerShown: false,
    ...
  }}
  ...
>
```

- 드로어 내비게이터에는 지금까지 설명한 것 외에도 더 많은 기능이 있습니다. 자세한 내용은 공식 문서를 확인
- https://reactnavigation.org/docs/drawer-navigator

## 하단 탭 내비게이터

![image18](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/18.png)

> 하단 탭 내비게이터

- 하단 탭 내비게이터도 마찬가지로 라이브러리를 추가로 설치해야 합니다.
- 하단 탭을 구현하는 과정에서 아이콘도 필요하므로, 아이콘을 편리하게 사용할 수 있도록 react-native-vector-icons 라이브러리도 설치

```
yarn add @react-navigation/bottom-tabs react-native-vector-icons
cd ios
pod install
```

- 우선 아이콘 없이 설정해보겠습니다. App.js 파일에서 기존에 만든 코드를 지우고 다음과 같이 작성해보세요.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createBottomTabNavigator } from "@react-navigation/bottom-tabs";
import { Text } from "react-native";
import { SafeAreaView } from "react-native-safe-area-context";

const Tab = createBottomTabNavigator();

function HomeScreen() {
  return <Text>Home</Text>;
}

function SearchScreen() {
  return <Text>Search</Text>;
}

function NotificationScreen() {
  return <Text>Notification</Text>;
}

function MessageScreen() {
  return <Text>Message</Text>;
}

function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator initialRouteName="Home">
        <Tab.Screen name="Home" component={HomeScreen} />
        <Tab.Screen name="Search" component={SearchScreen} />
        <Tab.Screen name="Notification" component={NotificationScreen} />
        <Tab.Screen name="Message" component={MessageScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

![image19](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/19.png)

> 아이콘이 없는 하단 탭

- 현재 아이콘을 따로 설정하지 않았기 때문에 ? 또는 X가 나타납니다.
- 하단 탭 내비게이터는 앞에서 다룬 네이티브 스택 내비게이터, 드로어 내비게이터와 마찬가지로 상단에 헤더가 나타납니다.
- 헤더는 Tab.Navigator에 screenOptions를 설정하거나 각 Tab.Screen에 options를 설정하여 커스터마이징할 수 있습니다.

- 아이콘을 사용하기 위해 iOS와 안드로이드에 필요한 설정을 해주겠습니다.
- Info.plist를 열어 최하단에 UIAppFonts를 추가

> ios/LearnReactNavigation/Info.plist - 최하단

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

- build.gradle 파일을 열어 최하단에 다음 코드를 추가

> android/app/build.gradle - 최하단

```groovy
project.ext.vectoricons = [
  iconFontNames: [ 'MaterialIcons.ttf' ]
]

apply from "../../node_modules/react-native-vector-icons/font.gradle"
```

- 설정을 마치고 yarn ios와 yarn android 명령어를 실행해 앱을 다시 가동해주세요.
- 이제 각 화면에 아이콘을 설정해주겠습니다. 아이콘은 Screen 컴포넌트에 options Props를 통해 설정할 수 있습니다. 이 과정에서 화면의 타이틀도 변경해주겠습니다. title 값을 지정해주면 아이콘과 함께 나타나는 텍스트도 변경됩니다.

> App.js

```jsx
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createBottomTabNavigator } from "@react-navigation/bottom-tabs";
import { Text } from "react-native";
import Icon from "react-native-vector-icons/MaterialIcons";

...

function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator initialRouteName="Home">
        <Tab.Screen
          name="Home"
          component={HomeScreen}
          options={{
            title: '홈',
            tabBarIcon: ({color, size}) => (
              <Icon name="home" color={color} size={size} />
            ),
          }}
        />

        <Tab.Screen
          name="Search"
          component={SearchScreen}
          options={{
            title: '검색',
            tabBarIcon: ({color, size}) => (
              <Icon name="search" color={color} size={size} />
            ),
          }}
        />

        <Tab.Screen
          name="Notification"
          component={NotificationScreen}
          options={{
            title: '알림',
            tabBarIcon: ({color, size}) => (
              <Icon name="notifications" color={color} size={size} />
            )
          }}
        />

        <Tab.Screen
          name="Message"
          component={MessageScreen}
          options={{
            title: '메시지',
            tabBarIcon: ({color, size}) => (
              <Icon name="message" color={color} size={size} />
            ),

          }}
        />
      </Tab.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

- tabBarIcon에는 함수 컴포넌트를 넣었으며, 이 컴포넌트는 focused, color, size를 Props로 받아옵니다.
- 현재는 focused를 사용하지 않기 때문에 생략했고, color와 size의 경우 Icon 컴포넌트로 그대로 전달해줬습니다.
- 이를 통해 특정 화면을 열었을 때 그 화면의 아이콘 색상이 변경되고, 화면의 가로/세로 모드에 따라 아이콘 크기도 변경됩니다.

![image20](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/20.png)

> 하단 탭 아이콘 적용

### 하단 탭 커스터마이징

- Tab.Navigator의 screenOptions Props를 통해 하단 탭에 대한 설정도 커스터마이징할 수 있습니다. 탭에 관한 설정은 tabBar로 시작합니다.
  - tabBarActiveTintColor: 활성화된 항목의 아이콘과 텍스트 색상
  - tabBarActiveBackgroundColor: 활성화된 항목의 배경색
  - tabBarInActiveTintColor: 비활성화된 항목의 아이콘과 텍스트 색상
  - tabBarInActiveBackgroundColor: 비활성화된 항목의 배경색
  - tabBarShowLabel: 항목에서 텍스트의 가시성 설정(기본값: true)
  - tabBarShowIcon: 항목에서 아이콘의 가시성 설정(기본값: false)
  - tabBarStyle: 하단 탭 스타일
  - tabBarLabelStyle: 텍스트 스타일
  - tabBarItemStyle: 항목 스타일
  - tabBarLabelPosition: 텍스트 위치 'beside-icon'(아이콘 우측) / 'below-icon'(아이콘 하단)
  - tabBarAllowFontScaling: 시스템 폰트 크기에 따라 폰트 크기를 키울지 결정(기본값: true)
  - tabBarSafeAreaInset: SafeAreaView의 forceInset 덮어쓰는 객체(기본값: {bottom: 'always', top: 'never'})
  - tabBarKeyboardHidesTabBar: 키보드가 나타날 때 하단 탭을 가릴지 결정(기본값: false)
- 설정을 통해 하단 탭에서 텍스트는 숨기고, 활성화된 항목의 아이콘 색상을 주황색(#fb8c00)으로 설정해보세요.

> App.js

```jsx
...

function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator
        initialRouteName="Home"
        tabBarOptions={{
          activeTintColor: '#fb8c00',
          showLabel: false
        }}>

    ...

  );
}
```

![image21](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/21.png)

> 하단 탭 커스터마이징

- 하단 탭 내비게이터의 더 많은 기능은 공식 문서에서 확인할 수 있습니다.
- https://reactnavigation.org/docs/bottom-tab-navigator

### 네이티브 스택 내비게이터와 하단 탭 내비게이터를 함께 사용하기

- 상황에 따라 여러 개의 내비게이터를 함께 사용할 수 있습니다. 현재 프로젝트의 화면 구성을 다음과 같이 설정해보겠습니다.

- Stack.Navigator

  - Main (Tab.Navigator)
    - Home
    - Search
    - Notification
    - Message
  - Detail

- screens 디렉터리에 MainScreen 컴포넌트를 다음과 같이 생성하세요.

> screens/MainScreen.js

```jsx
import React from "react";
import { createBottomTabNavigator } from "@react-navigation/bottom-tabs";
import { Text, View, Button } from "react-native";
import Icon from "react-native-vector-icons/MaterialIcons";

const Tab = createBottomTabNavigator();

function HomeScreen({ navigation }) {
  return (
    <View>
      <Text>Home</Text>
      <Button
        title="Detail 1 열기"
        onPress={() =>
          navigation.push("Detail", {
            id: 1,
          })
        }
      />
    </View>
  );
}

function SearchScreen() {
  return (
    <View>
      <Text>Search</Text>
    </View>
  );
}

function NotificationScreen() {
  return (
    <View>
      <Text>Notification</Text>
    </View>
  );
}

function MessageScreen() {
  return (
    <View>
      <Text>Message</Text>
    </View>
  );
}

function MainScreen() {
  return (
    <Tab.Navigator
      initialRouteName="Home"
      screenOptions={{
        tabBarActiveTintColor: "#fb8c00",
        tabBarShowLabel: false,
      }}
    >
      <Tab.Screen
        name="Home"
        component={HomeScreen}
        options={{
          title: "홈",
          tabBarIcon: ({ color, size }) => (
            <Icon name="home" color={color} size={size} />
          ),
        }}
      />
      <Tab.Screen
        name="Search"
        component={SearchScreen}
        options={{
          title: "검색",
          tabBarIcon: ({ color, size }) => (
            <Icon name="search" color={color} size={size} />
          ),
        }}
      />

      <Tab.Screen
        name="Notification"
        component={NotificationScreen}
        options={{
          title: "알림",
          tabBarIcon: ({ color, size }) => (
            <Icon name="notification" color={color} size={size} />
          ),
        }}
      />

      <Tab.Screen
        name="Message"
        component={MessageScreen}
        options={{
          title: "메시지",
          tabBarIcon: ({ color, size }) => (
            <Icon name="message" color={color} size={size} />
          ),
        }}
      />
    </Tab.Navigator>
  );
}

export default MainScreen;
```

- 다음으로 App.js 파일에서 네이티브 스택 내비게이터를 설정한 뒤에 MainScreen과 DetailScreen 컴포넌트를 추가해주겠습니다.

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import {createNativeStackNavigator} from '@react-navigation/native-stack';
import MainScreen from './screens/MainScreen';
import DetailScreen from './screens/DetailScreen';

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen 
          name="Main"
          component={MainScreen}
          options={{headerShown: false}}
        />
        <Stack.Screen name="Detail" component={DetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```
- 여기까지 코드를 입력하면 하단 탭과 네이티브 스택을 같이 사용하는 예시가 완성됩니다.
- HomeScreen에서 Detail 1 열기 버튼을 눌러보세요. DetailScreen이 탭 내비게이터를 가리고 새로 보여집니다.
- 네이티브 스택 내비게이터에서 MainScreen의 화면을 설정할 때, options에 headerShown 값을 false로 해주었는데요. 만약 이 값을 설정하지 않는다면 두 개의 헤더가 나타나는 문제가 발생하므로, 하단 탭 내비게이터를 네이티브 스택 내비게이터 내부에서 사용하게 될 때 이 설정을 해주는 것이 중요합니다.

## 머티리얼 상단 탭 내비게이터

- 머티리얼 상단 탭 내비게이터(Material Top Tab Navigator)는 앞에서 배운 하단 탭 내비게이터와 유사합니다.
- 이 내비게이터는 탭을 상단에 위치시킬 수 있습니다. 그리고 탭을 누르면 구글의 머티리얼 디자인 특유의 물결(ripple) 효과가 나타납니다. 화면을 스와이프하는 형태로 우측/좌측 탭으로 전환할 수도 있습니다.
- 내비게이터를 사용하기 위해 다음 라이브러리들을 설치해주세요.

```
yarn add @react-navigation/material-top-tabs react-native-tab-view react-native-pager-view
```

- react-native-tab-view는 리액트 네이티브에서 탭을 구현할 수 있게 하는 라이브러리입니다. react-navigation에 종속되지 않는 라이브러리이기 때문에 다른 내비게이션 관련 라이브러리와 함께 사용할 수도 있습니다.
- 설치 후에는 기존에 사용한 하단 탭 내비게이터를 대체해보겠습니다.

> screens/MainScreen.js

```jsx
import React from 'react';
import {createMaterialTopTabNavigator} from '@react-navigation/material-top-tabs';
import {Text, Button, View} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

const Tab = createMaterialTopTabNavigator();

...

function MainScreen() {
  return (
    <Tab.Navigator
      initialRouteName="Home"
      screenOptions={{
        tabBarShowLabel: false,
      }}>
      <Tab.Screen 
        name="Home"
        component={HomeScreen}
        options={{
          tabBarIcon: ({color}) => <Icon name="home" color={color} size={24} />,
        }}
      />
      
      <Tab.Screen 
        name="Search"
        component={SearchScreen}
        options={{
          tabBarIcon: ({color}) => (
            <Icon name="search" color={color} size={24} />
          ),
        }}
      />
      
      <Tab.Screen 
        name="Notification"
        component={NotificationScreen}
        options={{
          tabBarIcon: ({color}) => (
            <Icon name="notifications" color={color} size={24} />
          ),
        }}
      />
      
      <Tab.Screen 
        name="Message" 
        component={MessageScreen}
        options={{
          tabBarIcon: ({color}) => (
            <Icon name="message" color={color} size={24} />
          ),
        }}
      />
    </Tab.Navigator>
  );
}

export default MainScreen;
```

- 코드를 저장한 뒤, yarn ios와 yarn android 명령어를 다시 입력하여 시뮬레이터를 재시작하세요.
- 머티리얼 상단 탭 내비게이터의 경우 아이콘 크기를 자체적으로 지정해주지 않으므로 아이콘의 size Props를 직접 지정해야 합니다. 그리고 기존에 다룬 내비게이터와 다르게 이 내비게이터는 헤더를 보여주지 않습니다. App 컴포넌트에서 MainScreen의 화면 설정할 때 options를 제거하여 헤더를 활성화해보세요.

> App.js - Main 화면 설정

```jsx
<Stack.Screen name="Main" component={MainScreen} />
```

![image22](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/05%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EB%82%B4%EB%B9%84%EA%B2%8C%EC%9D%B4%EC%85%98%EC%9C%BC%EB%A1%9C%20%EC%97%AC%EB%9F%AC%20%ED%99%94%EB%A9%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/22.png)
> 머티리얼 상단 탭 내비게이터

- 참고로 머티리얼 물결 효과는 안드로이드 5.0 이상에서만 나타납니다. iOS에서는 이 효과가 없습니다.
- MaterialTopTabNavigator에 설정할 수 있는 옵션들을 알아봅시다. Tab.Navigator 컴포넌트에는 다음과 같은 Props를 지정해줄 수 있습니다.

---

# 내비게이션 Hooks

```

```

```

```

```

```

```

```
