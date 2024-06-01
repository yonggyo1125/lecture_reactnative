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

- WriteScreen에서 새 글을 작성하면 FeedsScreen과 CalendarScreen에 변화가 반영되어야 합니다. SearchScreen에서는 작성한 글을 검색할 수도 있어야 합니다. 만약 내비게이션을 사용하지 않으면 기능 구현에 필요한 상태와 함수들을 App 컴포넌트에서 선언해 Props를 통해 필요한 컴포넌트에 전달해줬을 텐데요. 내비게이션을 사용하는 중에 이렇게 처리하기는 꽤 복잡합니다.
- 이럴 때 매우 유용하게 사용할 수 있는 것이 바로 Context API입니다. 이 기능은 리액트에 내장된 기능으로 Props를 사용하지 않아도 특정 값이 필요한 컴포넌트끼리 쉽게 값을 공유할 수 있게 해줍니다. 주로 프로젝트에서 전역 상태를 관리할 때 많이 사용합니다.
- 구현 전 연습, 프로젝트에 contexts라는 디렉터리를 만들고 그 안에 LogContext.js 파일을 생성하세요.

> contexts/LogContext.js

```jsx
import {createContext} from 'react';

const LogContext = createContext('안녕하세요');

export default LogContext;
```

- 새로운 Context를 만들 때는 createContext 함수를 사용합니다. 이렇게 Context를 만들면 LogContext.Provider라는 컴포넌트와 LogContext.Consumer라는 컴포넌트가 만들어집니다.
- Provider는 Context 안에 있는 값을 사용할 컴포넌트들을 감싸주는 용도로 사용합니다. 현재 프로젝트로 예를 들면 RootStack 컴포넌트나 App 컴포넌트 내부에서 내용을 감싸주면 됩니다.
- App 컴포넌트의 내용을 LogContext.Provider로 감싸줍니다.

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import RootStack from './screens/RootStack';
import LogContext from './contexts/LogContext';

function App() {
  return (
    <NavigationContainer>
      <LogContext.Provider value="안녕하세요">
        <RootStack />
      </LogContext.Provider>
    </NavigationContainer>
  );
}

export default App;
```

- Provider에는 value라는 Props를 설정할 수 있는데요. 이 값이 바로 Context를 통해 여러 컴포넌트에서 공유할 수 있는 값입니다.
- 이렇게 Provider 컴포넌트를 사용하면 이 컴포넌트 내부에 선언된 모든 컴포넌트에서 Context 안의 값을 사용할 수 있습니다.

> screens/FeedsScreen.js

```jsx
import React from 'react';
import {StyleSheet, View, Text} from 'react-native';
import LogContext from './contexts/LogContext';

function feedsScreen() {
  return (
    <View style={styles.block}>
      <LogContext.Consumer>
        {(value) => <Text>{value}</Text>}
      </LogContext.Consumer>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {},
});

export default FeedsScreen;
```

-  Context 안의 값을 사용할 때는 Consumer 컴포넌트를 사용하는데, 아직 이 방식이 익숙하지 않을 것입니다. 컴포넌트 태그 사이에 함수를 넣어줬는데, 이는 Render Props라는 패턴입니다.
- 이 패턴을 이해하려면 우선 리액트 컴포넌트의 children이라는 Props를 이해해야 합니다.

## children Props

- children Props는 우리가 Text 컴포넌트에서 사용하는 것처럼 컴포넌트 태그 사이에 넣어준 값

> screens/FeedsScreen.js

```jsx
import React from 'react';
import {StyleSheet, View, Text} from 'react-native';
import LogContext from '../contexts/LogContext';

function FeedsScreen() {
  return (
    <View style={styles.block}>
      <Box>
        <Text>1</Text>
      </Box>
      <Box>
        <Text>2</Text>
      </Box>
      <Box>
        <Text>3</Text>
      </Box>
    </View>
  );
}

function Box({children}) {
  return <View style={styles.box}>{children}</View>;
}

const styles = StyleSheet.create({
  box: {
    box: {
      borderWidth: 2,
      padding: 16,
      borderBottomColor: 'black',
      marginBottom: 16,
    },
  }
});

export default FeedsScreen;
```

- 이렇게 하면 Box 컴포넌트 태그 사이에 넣은 JSX를 children이라는 Props로 받아와서 사용할 수 있습니다.

![image4](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/4.png)

- Render Props는 이 children의 타입을 함수 형태로 받아오는 패턴입니다.
- Render Props를 사용하면 반대로 우리가 사용할 컴포넌트에서 특정 값을 밖으로 빼내와 사용할 수가 있습니다. 
- 예를 들어 Box 컴포넌트에서 ‘Hello World’라는 값을 FeedsScreen 컴포넌트에 전달해 사용하도록 만들고 싶다면 다음과 같이 구현할 수 있습니다.


> screens/FeedsScreen.js

```jsx
import React from 'react';
import {StyleSheet, View, Text} from 'react-native';

function FeedsScreen() {
  return (
    <View style={styles.block}>
      <Box>{(value) => <Text>{value}</Text>}</Box>
    </View>
  );
}

function Box({children}) {
  return <View style={styles.box}>{children('Hello World')}</View>
}

const styles = StyleSheet.create({
  box: {
    borderWidth: 2,
    padding: 16,
    borderBottomColor: 'black',
    marginBottom: 16,
  },
});

export default FeedsScreen;
```
- 이렇게 코드를 입력하고 나면 화면에 ‘Hello World’가 나타납니다.

## useContext Hook 함수

- Render Props는 리액트에 Hooks가 없던 시절 유용했는데, 요즘은 사용할 일이 그렇게 많지 않습니다. 그
- Context의 Consumer라는 것도 꼭 사용할 필요가 없습니다. 왜냐하면 리액트에 useContext라는 Hook이 있으므로 Context 값을 훨씬 간결하게 사용할 수 있습니다.

> screens/FeedsScreen.js

```jsx
import React, {useContext} from 'react';
import {StyleSheet, View, Text} from 'react-native';
import LogContext from '../contexts/LogContext';

function FeedsScreen() {
  const value = useContext(LogContext);
  return (
    <View style={styles.block}>
      <Text>{value}</Text>
    </View>
  );
}

const styles = StyleSheet.create({});

export default FeedsScreen;
```

- Consumer와 Render Props를 사용할 때는 Context의 값을 읽는 부분이 JSX에 있기 때문에 컴포넌트 로직 작성이 까다로울 수 있습니다. 
- 반면 useContext Hook을 사용하는 경우에는 컴포넌트에서 JSX를 반환하기 전에 값을 조회할 수 있기 때문에 컴포넌트 로직 작성이 더욱 편합니다.

## Context에서 유동적인 값 다루기
- App 컴포넌트에서 useState를 사용해 관리하는 상태를 Provider로 넣어주면 값이 바뀔 때 useContext를 사용하는 컴포넌트 쪽에서도 리렌더링이 잘 발생할 것입니다.
- Provider를 사용하는 컴포넌트에서 Context의 상태를 관리하는 것보다는 Provider 전용 컴포넌트를 따로 만드는 것이 유지보수성이 더 높습니다.
- 특히 Context에서 다루는 로직이 복잡할 때는 전용 컴포넌트를 만드는 것이 좋습니다.

> contexts/LogContext.js

```jsx
import React from 'react';
import {createContext, useState} from 'react';

const LogContext = createContext();

export function LogContextProvider({children}) {
  const [text, setText] = useState('');
  return (
    <LogContext.Provider value={{text, setText}}>
      {children}
    </LogContext.Provider>
  );
}

export default LogContext;
```

- LogContextProvider라는 컴포넌트를 따로 만들어줬습니다.
- 내부에서는 useState를 사용해 간단한 문자열 상태 값을 관리하고, Provider의 value에는 text와 setText를 넣어줬습니다.
- App에서 기존에 사용한 LogContext.Provider를 지우고 LogContext
  Provider를 사용하세요.

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import RootStack from './screens/RootStack';
import {LogContextProvider} from './contexts/LogContext';

function App() {
  return (
    <NavigationContainer>
      <LogContextProvider>
        <RootStack />
      </LogContextProvider>
    </NavigationContainer>
  );
}

export default App;
```

- 이렇게 저장하면 FeedsScreen에서 오류가 발생합니다. 기존 Context에서는 문자열 값만 주었지만 이번에는 객체 형태의 값을 받아왔기 때문
- FeedsScreen에서 값을 수정하고 수정된 값을 보여주겠습니다.

> screens/FeedsScreen.js

```jsx
import React, {useContext} from 'react';
import {StyleSheet, View, TextInput} from 'react-native';
import LogContext from '../contexts/LogContext';

function FeedsScreen() {
  const {text, setText} = useContext(LogContext);
  return (
    <View style={styles.bold}>
      <TextInput 
        value={text}
        onChangeText={setText}
        placeholder="텍스트를 입력하세요."
        style={styles.input}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  input: {
    padding: 16,
    backgroundColor: 'white',
  }
});

export default FeedsScreen;
```

- 그다음에는 CalendarScreen에서 LogContext가 지닌 값을 화면에 띄워보세요

> screens/CalendarScreen.js

```jsx
import React, {useContext} from 'react';
import {StyleSheet, Text, View} from 'react-native';
import LogContext from '../contexts/LogContext';

function CalendarScreen() {
  const {text} = useContext(LogContext);
  return (
    <View style={styles.block}>
      <Text style={styles.text}>text: {text}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {},
  text: {
    padding: 16,
    fontSize: 24
  }
});

export default CalendarScreen;
```

- 코드를 다 작성했으면 피드 화면의 TextInput에 Hello World를 입력한 뒤, 달력 화면을 열어서 작성한 문구가 그대로 나타났는지 확인

---
# 새 글 작성하기 

---
# 글 목록 보여주기

---
# Animated로 애니메이션 적용하기 