> 1. 내비게시션 설정
> 2. Context API를 사용한 전역 상태 관리
> 3. date-fns를 사용한 날짜 포맷팅 
> 4. Animated를 사용한 애니메이션 효과

# 프로젝트 준비하기 
- 새로운 프로젝트 생성 

```
npx react-native init DayLog --version 0.70
```

- 필요한 라이브러리 설치 

```
cd DayLog
yarn add @react-navigation/native @react-navigation/native-stack @react-navigation/bottom-tabs react-native-screens react-native-safe-area-context react-native-vector-icons
```

- 설치가 끝났으면 iOS를 위해 Pod도 설치

```
npx pod-install
```

- 이 명령어는 프로젝트 루트 디렉터리에서 Pod를 바로 설치할 수 있게 하는 명령어입니다. 매번 Pod를 설치할 때마다 ios 디렉터리에 들어갔다가 다시 나오는 게 번거롭게 느껴질 때 사용하면 됩니다.

## react-native-vector-icons 적용하기

- react-native-vector-icons의 MaterialIcons를 사용할 수 있도록 Info.plist를 수정

> ios/DayLog/info.plist

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

- build.gradle 파일을 열어서 최하단에 다음 코드를 추가해주세요.

> android/app/build.gradle - 최하단

```groovy
project.ext.vectoricons = [
    iconFontNames: [ 'MaterialIcons.ttf' ]
]

apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
```

## react-navigation 적용하기

- App 컴포넌트의 내용을 모두 비우고, NavigationContainer 컴포넌트를 불러와 사용

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';

function App() {
    return <NavigationContainer></NavigationContainer>;
}

export default App;
```

![image1](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/1.png)

> 화면 설계 

- DayLog 프로젝트에는 두 종류의 내비게이션을 사용
  - RootStack은 네이티브 스택 내비게이션
  - MainTab은 하단 탭 내비게이션
- FeedsScreen: 작성한 글을 목록 형태로 보여주는 화면
- CalendarScreen: 달력 형태로 글을 조회하는 화면
- SearchScreen: 글을 검색할 수 있는 화면
- WriteScreen은 글을 작성하거나 수정하는 화면인데, MainTab에 넣지 않고 RootStack에 넣어서 이 화면이 나타날 때는 하단 탭이 나타나지 않도록 설정
- 프로젝트 디렉터리에 screens 디렉터리를 만들고 다음과 같이 화면들을 준비

> screens/FeedsScreen.js

```jsx
import React from 'react';
import {StyleSheet, View} from 'react-native';

function FeedsScreen() {
    return <View style={styles.block} />;
}

const styles = StyleSheet.create({
    block: {},
});

export default FeedsScreen;
```

> screens/CalendarScreen.js

```jsx
import React from 'react';
import {StyleSheet, View} from 'react-native';

function CalendarScreen() {
    return <View style={styles.block} />;
}

const styles = StyleSheet.create({
    block: {},
});

export default CalendarScreen;
```

> screens/SearchScreen.js

```jsx
import React from 'react';
import {StyleSheet, View} from 'react-native';

function SearchScreen() {
    return <View style={styles.block} />;
}

const styles = StyleSheet.create({
    block: {},
});

export default SearchScreen;
```

> screens/WriteScreen.js

```jsx
import React from 'react';
import {StyleSheet, View} from 'react-native';

function WriteScreen() {
    return <View style={styles.block} />;
}

const styles = StyleSheet.create({
    block: {},
});

export default WriteScreen;
```

- 하단 탭 내비게이션을 사용하는 MainTab 컴포넌트 제작

> screens/MainTab.js

```jsx
import React from 'react';
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';
import FeedsScreen from './FeedsScreen';
import CalendarScreen from './CalendarScreen';
import SearchScreen from './SearchScreen';

const Tab = createBottomTabNavigator();

function MainTab() {
    return (
        <Tab.Navigator>
            <Tab.Screen name="Feeds" component={FeedScreen} />
            <Tab.Screen name="Calendar" component={CalendarScreen} />
            <Tab.Screen name="Search" component={SearchScreen} />
        </Tab.Navigator>
    );
}

export default MainTab;
```

- 네이티브 스택 내비게이션을 사용하는 RootStack 컴포넌트

> screens/RootStack.js

```jsx
import React from 'react';
import {createNativeStackNavigator} from '@react-navigation/native-stack';
import MainTab from './MainTab';
import WriteScreen from './WriteScreen';

const Stack = createNativeStackNavigator();

function RootStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen 
        name="MainTab"
        component={MainTab}
        options={{headerShown: false}}
      />
      <Stack.Screen name="Write" component={WriteScreen} />
    </Stack.Navigator>
  );
}

export default RootStack; 
```

- 네이티브 스택 내비게이터에 MainTab 화면을 설정할 때 headerShown 값을 false로 설정하세요. 이렇게 설정하지 않으면 헤더 두 개가 중첩되어 나타납니다.
- 내비게이션을 사용하는 컴포넌트의 이름을 지을 때 따로 정해진 규칙은 없으므로, 자유롭게 지으면 됩니다. 예를 들어 MainTab 컴포넌트의 경우 이를 하나의 화면으로 간주해 MainTabScreen 또는 MainScreen으로 이름을 정해도 됩니다. RootStack도 마찬가지로 RootStackScreen이나 RootScreen이라는 이름을 사용해도 상관없습니다.
- 내비게이션 전용 컴포넌트와 일반 화면을 구분하기 위해 이름에 규칙을 사용할 것, 내비게이션 전용 컴포넌트에는 MainTab과 RootStack처럼 이름 뒤에 Tab이나 Stack을 넣겠습니다.
- RootStack 컴포넌트를 따로 지정하지 않고 바로 App 컴포넌트 내부에서 네이티브 스택 내비게이터를 사용하는 것도 가능합니다만, 이 프로젝트에서는 App 컴포넌트의 간결함을 위해 이렇게 RootStack 컴포넌트를 따로 분리합니다.
- App 컴포넌트에서 RootStack 컴포넌트를 사용

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

- iOS와 안드로이드 시뮬레이터를 가동해 지금까지 만든 결과물 확인

```
yarn ios
yarn android
```

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/2.png)

- 탭 아이콘 설정, 화면 타이틀 설정, 탭 스타일 변경

> screens/MainTab.js

```jsx
import React from 'react';
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';
import FeedScreen from './FeedScreen';
import CalendarScreen from './CalendarScreen';
import SearchScreen from './SearchScreen';
import Icon from 'react-native-vector-icons/MaterialIcons';

const Tab = createBottomTabNavigator();

function MainTab() {
  return (
    <Tab.Navigator
      tabBarOptions={{
        showLabel: false,
        activeTintColor: '#009688',
      }}>
      <Tab.Screen 
        name="Feeds" 
        component={FeedsScreen}
        options={{
          tabBarIcon: ({color, size}) => (
            <Icon name="view-stream" size={size} color={color} />
          ),
        }}
      />
      <Tab.Screen 
        name="Calendar"
        component={CalendarScreen}
        options={{
          tabBarIcon: ({color, size}) => (
            <Icon name="event" size={size} color={color} />
          ),
        }}
      />
      <Tab.Screen 
        name="Search"
        component={SearchScreen}
        options={{
          tabBarIcon: ({color, size}) => (
            <Icon name="search" size={size} color={color} />
          ),
        }}
      />
    </Tab.Navigator>
  );
}

export default MainTab;
```

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/2.png)
> 네비게이션 아이콘 및 헤더 이름 설정

---
# Context API 사용하기 

---
# 새 글 작성하기 

---
# 글 목록 보여주기

---
# Animated로 애니메이션 적용하기 