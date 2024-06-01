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

![image5](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/5.png)

- 서로 다른 화면에서 상태를 공유하는 방법을 배웠습니다. DayLog 프로젝트에서는 앞으로 이런 방법으로 화면 간에 앱의 상태를 공유할 예정입니다. 
- 잘 작동하는 것을 확인했다면 이번 절에서 FeedsScreen과 CalendarScreen에 작성한 코드를 모두 초기화해주세요.

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

---
# 새 글 작성하기 

>  WriteScreen의 기능을 구현하겠습니다. 그리고 FeedsScreen에 WriteScreen을 열 수 있는 FloatingWriteButton도 만들어봅니다.

## FloatingWriteButton 만들기

- FloatingWriteButton은 FeedsScreen의 우측 모서리에 나타나는 둥근 버튼입니다.
- 프로젝트 루트 디렉터리에 components 디렉터리를 만들고, 그 안에 다음 파일을 작성

> components/FloatingWriteButton.js

```jsx
import React from 'react';
import {Platform, Pressable, StyleSheet, View} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

function FloatingWriteButton() {
  return (
    <View style={styles.wrapper}>
      <Pressable 
        style={({pressed}) => [
          styles.button,
          Platform.OS === 'ios' && {
            opacity: pressed ? 0.6 : 1,
          }
        ]}
        android_ripple={{color: 'white'}}>
        <Icon name="add" size={24} style={styles.icon} />
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  wrapper: {
    position: 'absolute',
    bottom: 16,
    right: 16,
    width: 56,
    height: 56,
    borderRadius: 28,
    // iOS 전용 그림자 설정
    shadowColor: '#4d4d4d',
    shadowOffset: {width: 0, height: 4},
    shadowOpacity: 0.3,
    shadowRadius: 4,
    // 안드로이드 전용 그림자 설정
    elevation: 5,
    // 안드로이드에서 물결 효과가 영역 밖으로 나가지 않도록 설정
    // iOS에서는 overflow가 hidden일 경우 그림자가 보여지지 않음
    overflow: Platform.select({android: 'hidden'}),
  },
  button: {
    width: 56,
    height: 56,
    borderRadius: 28,
    backgroundColor: '#009688',
    justifyContent: 'center',
    alignItems: 'center',
  },
  icon: {
    color: 'white',
  }
});

export default FloatingWriteButton; 
```

- 리액트 네이티브 v0.63에 새로 도입된 컴포넌트인 Pressable을 사용했습니다(이전에 배운 Touchable* 컴포넌트와 비슷합니다).
- Pressable 컴포넌트는 TouchableWithoutFeedback과 성격이 비슷하지만, 기능이 더 많습니다. android_ripple Props를 설정해 안드로이드에서 물결 효과를 보여줄 수도 있고, 스타일을 설정할 때 pressed 값을 인식해 컴포넌트가 눌리면 동적인 스타일을 적용할 수도 있습니다.
- 이 컴포넌트의 경우 iOS 환경에서는 버튼을 눌렀을 때 투명도를 가지게 설정했고, 안드로이드에서는 물결 효과가 나타나게 만들었습니다.
- styles.wrapper에서는 position: 'absolute' 스타일을 부여했는데요. 이 설정을 통해 컴포넌트의 위치를 좌푯값 left, right, top, bottom으로 지정할 수 있습니다. 앞의 코드에서는 right와 bottom 값을 지정해 우측 모서리에서 16dp만큼의 여백을 두었습니다.
- 그리고 컴포넌트에 그림자 스타일을 적용해줬는데요. iOS와 안드로이드는 그림자 설정을 위한 스타일 이름이 다른 것에 주의하세요. 또한, 안드로이드에서 물결 효과가 원 밖으로 나가지 않도록 overflow를 설정해줬습니다. iOS에서는 overflow: 'hidden' 스타일이 적용되면 그림자도 보여지지 않기 때문에 안드로이드에서만 해당 스타일을 적용하도록 만들었습니다.
- 컴포넌트를 다 작성했으면 FeedsScreen에서 방금 만든 컴포넌트를 불러와 사용해보세요. 그리고 styles.block에 flex: 1을 넣어주세요. 만약 이 값을 넣지 않으면 해당 View가 비어있을 때는 높이가 0으로 간주됩니다. 그러면 방금 만든 컴포넌트에서 position: 'absolute' 설정이 제대로 작동하지 않아 화면에 나타나지 않을 것입니다.

> screens/FeedsScreen.js

```jsx
import React from 'react';
import {StyleSheet, View} from 'react-native';
import FloatingWriteButton from '../components/FloatingWriteButton';

function FeedsScreen() {
  return (
    <View style={styles.block}>
      <FloatingWriteButton />
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
  }
});

export default FeedsScreen;
```

![image6](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/6.png)
> FloatingWriteButton

- 해당 버튼을 누르면 WriteScreen을 띄우도록 컴포넌트를 수정

> components/FloatingWriteButton.js

```jsx
import {useNavigation} from '@react-navigation/native';
import React from 'react';
import {Platform, Pressable, StyleSheet, View} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

function FloatingWriteButton() {
  const navigation = useNavigation();
  const onPress = () => {
    navigation.navigate("Write");
  };
  
  return (
    <View style={styles.wrapper}>
      <Pressable
        style={({pressed}) => [
          styles.button,
          Platform.OS === 'ios' && {
            opacity: pressed ? 0.6 : 1,
          },
        ]}
        android_ripple={{color: 'white'}}
        onPress={onPress}>
        <Icon name="add" size={24} style={styles.icon} />
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  ...
});

export default FloatingWriteButton;
```

## WriteScreen UI 준비하기

- WriteScreen의 UI를 준비해봅시다. 우선 WriteScreen에서 기본적으로 보이는 헤더를 숨겨줍니다.

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
      <Stack.Screen 
        name="Write"
        component={WriteScreen}
        options={{headerShown: false}}
      />
    </Stack.Navigator>
  );
}

export default RootStack;
```

### WriteHeader 만들기

> components/WriteHeader.js

```jsx
import {useNavigation} from '@react-navigation/native';
import React from 'react';
import {Pressable, StyleSheet, View} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

function WriteHeader() {
  const navigation = useNavigation();
  const onGoBack = () => {
    navigation.pop();
  };
  
  return (
    <View style={styles.block}>
      <View style={styles.iconButtonWrapper}>
        <Pressable
          style={styles.iconButton}
          onPress={onGoBack}
          android_ripple={{color: '#ededed'}}>
          <Icon name="arrow-back" size={24} color='#424242' />
        </Pressable>
      </View>
      <View style={styles.buttons}>
        <View style={[styles.iconButtonWrapper, styles.marginRight]}>
          <Pressable
            style={[styles.iconButton]}
            android_ripple={{color: '#ededed'}}>
            <Icon name="delete-forever" size={24} color="#ef5350" />
          </Pressable>
        </View>
        <View style={styles.iconButtonWrapper}>
          <Pressable
            style={styles.iconButton}
            android_ripple={{color: '#ededed'}}>
            <Icon name="check" size={24} color="#009688" />
          </Pressable>
        </View>
      </View>
    </View>
  );
}


const styles = StyleSheet.create({
  block: {
    height: 48,
    paddingHorizontal: 8,
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
  },
  iconButtonWrapper: {
    width: 32,
    height: 32,
    borderRadius: 16,
    overflow: 'hidden',
  },
  iconButton: {
    alignItems: 'center',
    justifyContent: 'center',
    width: 32,
    height: 32,
    borderRadius: 16,
  },
  buttons: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  marginRight: {
    marginRight: 8,
  }
});

export default WriteHeader;
```

- styles.block에서 flex-direction: 'row'로 설정해서 컴포넌트들이 가로 방향으로 나타나게 했습니다. 
- 여기서 justifyContent: 'space-between'이라는 스타일이 적용되었는데, 이렇게 설정하면 해당 컴포넌트 내부에 렌더링된 요소들 사이 여백이 꽉 채워집니다. 만약 컴포넌트 내부에 요소가 여러 개 있으면 다음과 같이 요소들 사이의 여백이 균일하게 주어집니다.

![image7](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/7.png)
> space-between

- 현재 컴포넌트에 요소가 두 개 있으므로 하나는 좌측 끝, 하나는 우측 끝에 붙어서 보입니다.
- 헤더에는 버튼이 세 개 있습니다. 좌측에 뒤로가기 버튼, 우측에 삭제 버튼과 완료 버튼이 있습니다. 삭제 버튼은 작성된 글을 열었을 때만 보여야 하는데 당장은 UI 구현을 위해서 모든 상황에 보이게 만들었습니다.
- 반복되는 코드를 발견했을 때 이를 컴포넌트로 만들어서 컴포넌트를 재사용하는 형태로 리팩토링하는 습관을 가진다면 프로젝트 유지보수에 용이
- 헤더의 버튼 컴포넌트를 쉽게 재사용할 수 있도록 따로 분리해 작성

> components/TransparentCircleButton.js

```jsx
import React from 'react';
import {Platform, Pressable, StyleSheet, View} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';


function TransparentCircleButton({name, color, hasMarginRight, onPress}) {
  return (
     <View
      style={[styles.iconButtonWrapper, hasMarginRight && styles.rightMargin]}>
        <Pressable
          style={({pressed}) => [
            styles.iconButton,
            Platform.OS === 'ios' && pressed && {
              backgroundColor: '#efefef',
            },
          ]}
          onPress={onPress}
          android_ripple={{color: '#ededed'}}>
          <Icon name={name} size={24} color={color} />
        </Pressable>
     </View>
  );
}

const styles = StyleSheet.create({
  iconButtonWrapper: {
    width: 32,
    height: 32, 
    borderRadius: 16,
    overflow: 'hidden',
  },
  iconButton: {
    alignItems: 'center',
    justifyContent: 'center',
    width: 32,
    height: 32,
    borderRadius: 16,
  },
  rightMargin: {
    marginRight: 8,
  }
});

export default TransparentCircleButton;
```

- 이 컴포넌트에서는 4개의 Props를 받아오도록 설정했습니다.
  - name: 아이콘 이름
  - color: 아이콘 색상
  - hasMarginRight: 우측 여백 유무
  - onPress: 버튼을 눌렀을 때 호출할 함수
- 그리고 컴포넌트를 분리하는 과정에서 iOS의 경우 버튼을 클릭할 때 배경색을 설정하도록 처리해줬습니다.
- 컴포넌트 작성이 끝났으면 기존에 만든 버튼들을 방금 만든 컴포넌트로 대체해주세요.

> components/WriteHeader.js

```jsx
import {useNavigation} from '@react-navigation/native';
import React from 'react';
import {StyleSheet, View} from 'react-native';
import TransparentCircleButton from './TransparentCircleButton';

function WriteHeader() {
  const navigation = useNavigation();
  const onGoBack = () => {
    navigation.pop();
  };
  
  return (
    <View style={styles.block}>
      <View style={styles.iconButtonWrapper}>
        <TransparentCircleButton
          onPress={onGoBack}
          name="arrow-back"
          color="#424242"
        />
      </View>
      <View style={styles.buttons}>
        <TransparentCircleButton
          name="delete-forever"
          color="#ef5350"
          hasMarginRight
        />
        <TransparentCircleButton name="check" color="#009688" />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    height: 48,
    paddingHorizontal: 8,
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
  },
  buttons: {
    flexDirection: 'row',
    alignItems: 'center',
  }
});

export default WhiteHeader;
```

- 이제 이 컴포넌트를 WriteScreen에서 사용해 컴포넌트가 잘 나타나는지 확인

> screens/WriteScreen.js

```jsx
import React from 'react';
import {StyleSheet, View} from 'react-native';
import {SafeAreaView} from 'react-native-safe-area-context';
import WriteHeader from '../components/WriteHeader';

function WriteScreen() {
  return (
    <SafeAreaView style={styles.block}>
      <WriteHeader />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
    backgroundColor: 'white',
  },
});

export default WriteScreen;
```

- 현재 내비게이션의 헤더를 없애고 우리가 직접 만든 헤더를 사용하고 있기 때문에 컴포넌트에 SafeAreaView를 사용해 iOS에서 상태 바 영역과 내용이 겹치지 않게 만들어줘야 합니다. 추가로, 배경색을 흰색으로 설정해주세요.

![image8](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/8.png)
> WriteHeader 

- 이번에는 WriteEditor 컴포넌트를 만들어봅시다. 이 컴포넌트에서는 TextInput을 두 개 사용해 제목과 내용을 작성하는 기능을 구현할 것입니다.

> components/WriteEditor.js

```jsx
import React from 'react';
import {View, StyleSheet, TextInput} from 'react-native';

function WriteEditor({title, body, onChangeTitle, onChangeBody}) {
  return (
    <View style={styles.block}>
      <TextInput 
        placeholder="제목을 입력하세요"
        style={styles.titleInput}
        returnKeyType="next"
        onChangeText={onChangeTitle}
        value={title}
      />
      <TextInput 
        placeholder="당신의 오늘을 기록해보세요"
        type={styles.bodyinput}
        multiline
        textAlignVertical="top"
        onChangeText={onChangeBody}
        value={body}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  block: { flex: 1, padding: 16 },
  titleInput: {
    paddingVertical: 0,
    fontSize: 18,
    marginBottom: 16,
    color: '#263238',
    fontWeight: 'bold',
  },
  bodyInput: {
    flex: 1,
    fontSize: 16,
    paddingVertical: 0,
    color: '#263238',
  }
});

export default WriteEditor;
```

- 두 번째 TextView를 보면 multiline Props에 값을 지정하지 않았습니다. 
- 컴포넌트를 사용할 때 이렇게 Props의 이름만 쓰고 따로 값을 지정하지 않으면 값이 true로 지정됩니다. 즉, multiline={true}와 동일한 코드입니다. TextView에 이 값이 true로 설정되면 여러 줄을 작성할 수 있습니다.
- 이 컴포넌트를 WriteScreen에서 보여주세요.

> screens/WriteScreen.js 

```jsx
import React from 'react';
import {StyleSheet, View} from 'react-native';
import {SafeAreaView} from 'react-native-safe-area-content';
import WriteEditor from '../components/WriteEditor';
import WriteHeader from '../components/WriteHeader';

function WriteScreen() {
  return (
    <SafeAreaView style={styles.block}>
      <WriteHeader />
      <WriteEditor />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
    backgroundColor: 'white',
  }
});

export default WriteScreen;
```

![image9](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/06%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/9.png)
> WriteEditor

## useRef로 컴포넌트 레퍼런스 선택하기

- useRef는 함수 컴포넌트에서 컴포넌트의 레퍼런스를 선택할 수 있게 하는 Hook입니다.
- 제목을 입력하고 Enter를 누르면 하단 내용으로 포커스를 이동시키고 싶다고 합시다. 이럴 때는 내용 TextInput의 레퍼런스를 선택해서 포커스해줘야 합니다.
- 먼저 내용 TextInput의 레퍼런스를 선택해봅시다.

> components/WriteEditor.js

```jsx
import React, {useRef} from 'react';
import {View, StyleSheet, TextInput} from 'react-native';

function WriteEditor({title, body, onChangeTitle, onChangeBody}) {
  const bodyRef = useRef();
  
  return (
    <View style={styles.block}>
      <TextInput 
        placeholder="제목을 입력하세요"
        style={styles.titleInput}
        returnKeyType="next"
        onChangeText={onChangeTitle}
        value={title}
      />
      <TextInput 
        placehoolder="당신의 오늘을 기록해보세요"
        style={styles.bodyInput}
        multiline
        textAlignVertical="top"
        onChangeText={onChangeBody}
        value={body}
        ref={bodyRef}
      />
    </View>
  );  
}

... 

```

- 이렇게 ref를 생성해 TextInput의 Props로 지정해주면 원하는 컴포넌트의 레퍼런스를 선택할 수 있습니다. TextInput의 레퍼런스에는 다음과 같은 메서드가 구현되어 있습니다.
  - .focus(): TextInput에 포커스를 잡아줍니다.
  - .blur(): TextInput에 포커스를 해제합니다.
  - .clear(): TextInput의 내용을 모두 비웁니다.
- 제목 TextInput에서 onSubmitEditing Props를 통해 Enter를 눌렀을 때 내용 TextInput으로 포커스해보겠습니다.

> components/WriteEditor.js

```jsx
import React, {useRef} from 'react';
import {View, StyleSheet, TextInput} from 'react-native';

function WriteEditor({title, body, onChangeTitle, onChangeBody}) {
  const bodyRef = useRef();  
    
  return (
    <View style={styles.bloc}>
      <TextInput
        placeholder="제목을 입력하세요"
        style={styles.titleInput}
        returnKeyType="next"
        onChangeText={onChangeTitle}
        value={title}
        onSubmitEditing={() => {
          bodyRef.current.focus();
        }}
      />
    ...
  );
}

...

```

- useRef로 선택한 레퍼런스는 .current 값을 조회해 확인할 수 있습니다.
- 이제 제목을 입력하고 Enter를 눌러보세요. 내용으로 포커스가 이동했나요? (참고로 안드로이드에서 한글을 입력하는 중에 Enter를 누르면 스페이스가 먼저 들어가고, 그다음에 Enter를 한 번 더 눌러야 내용으로 포커스가 이동할 것입니다.)

## KeyboardAvoidingView로 화면 감싸기

> 내용 TextInput에서 Enter를 여러 번 눌러서 화면에서 기본적으로 보여줄 수 있는 줄 수를 초과할 경우, 안드로이드에서는 별 문제없이 스크롤할 수 있지만 iOS에서는 하단 내용이 잘리게 됩니다. 따라서 KeyboardAvoidingView로 WriteScreen 내부의 내용을 감싸줘야 작성한 내용이 엄청 길어져 줄이 많아졌을 때도 문제없이 글을 작성할 수 있습니다.

> screens/WriteScreen.js

```jsx
import React from 'react';
import {KeyboardAvoidingView, Platform, StyleSheet} from 'react-native';
import {SafeAreaView} from 'react-native-safe-area-context';
import WriteEditor from '../components/WriteEditor';
import WriteHeader from '../components/WriteHeader';

function WriteScreen() {
  return (
    <SafeAreaView style={styles.block}>
      <KeyboardAvoidingView
        style={styles.avoidingView}
        behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
        <WriteHeader />
        <WriteEditor />
      </KeyboardAvoidingView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
    backgroundColor: 'white',
  },
  avoidingView: {
    flex: 1,
  }
});

...

```

## WhiteScreen에서 텍스트 상태 관리하기

- 이번에는 WriteScreen에서 제목과 내용 텍스트의 상태를 관리해보겠습니다. 이전에 배운 useState Hook을 사용하면 됩니다.

> screens/WriteScreen.js

```jsx
import React, {useState} from 'react';
import {KeyboardAvoidingView, Platform, StyleSheet} from 'react-native';
import {SafeAreaView} from 'react-native-safe-area-context';
import WriteEditor from '../components/WriteEditor';
import WriteHeader from '../components/WriteHeader';

function WriteScreen() {
  const [title, setTitle] = useState('');
  const [body, setBody] = useState('');
  
  return (
    <SafeAreaView style={styles.block}>
      <KeyboardAvoidingView
        style={styles.avoidingView}
        behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
        <WriteHeader />
        <WriteEditor 
          title={title}
          body={body}
          onChangeTitle={setTitle}
          onChangeBody={setBody}
        />
      </KeyBoardAvoidingView>
    </SafeAreaView>
  );
}

...

```
- useState를 써서 만든 상태 값과 업데이트 함수를 WriteEditor에 Props로 전달해주면 됩니다.

---
# 글 목록 보여주기

---
# Animated로 애니메이션 적용하기 