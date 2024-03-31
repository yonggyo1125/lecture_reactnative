# 프로젝트 기반 다지기 

## 프로젝트 기반 다지기 

### 프로젝트 생성하기 

- 다음 명령어로 새로운 프로젝트를 생성해주세요.

```jsx
npx react-native init TodoApp --version 0.70
```
- 프로젝트가 생성되면 방금 만든 프로젝트 디렉토리를 VS Code로 열고 <code>CTRL</code> + <code>`(백틱)</code>을 눌러 터미널을 엽니다.
- 다음 명령어를 입력해 iOS와 안드로이드 시뮬레이터를 실행하세요(윈도우는 안드로이드 시뮬레이터만 실행).

```
yarn ios 
yarn android
```

> Metro Bundler가 기존에 실행중이라면 실행중인 것을 반드시 종료해야 새로 만든 프로젝트의 애플리케이션이 정상 가동됩니다.

> App.js

```jsx
import React from 'react';
import {SafeAreaView, View, Text, StyleSheet} from 'react-native';

function App() {
    return (
        <SafeAreaView>
            <View>
                <Text>TodoApp</Text>
            </View>
        </SafeAreaView>
    );
}

const styles = StyleSheet.create({});

export default App;
```

## 오늘 날짜를 알려주는 DateHead 컴포넌트 만들기 

> components/DateHead.js

```jsx
import React from 'react';
import {View, Text, StyleSheet} from 'react-native';

function DateHead() {
    return (
        <View style={styles.block}>
            <Text style={styles.dateText}>2021년 5월 3일</Text>
        </View>
    );
}

const styles = StyleSheet.create({
    block: {
        padding: 16, 
        backgroundColor: '#26a69a',
    },
    dateText: {
        fontSize: 24,
        color: 'white',
    }
});

export default Datehead;
```

- DateHead 컴포넌트를 App에 적용하기 

> App.js

```jsx
import React from 'react';
import {SafeAreaView, StyleSheet} from 'react-native';
import DateHead from './components/DateHead';

function App() {
    return (
        <SafeAreaView>
            <DateHead />
        </SafeAreaView>
    );
}

const styles = StyleSheet.create({});

export default App;
```

- 이제 오늘 날짜를 나타내도록 구현해봅시다. 자바스크립트에서는 현재 시간을 가져올 때는 Date 객체를 사용합니다. 

```javascript
const today = new Date();
```

> App.js

```jsx
import React from 'react';
import {SafeAreaView, StyleSheet} from 'react-native';
import DateHead from './components/DateHead';

function App() {
    const today = new Date();
    
    return (
        <SafeAreaView>
            <DateHead date={today} />
        </SafeAreaView>
    );
}

const styles = StyleSheet.create({});

export default App;
```

> components/DateHead.js

```jsx
import React fromm 'react';
import {View, Text, StyleSheet} from 'react-native';

function DateHead({date}) {
    const year = date.getFullYear();
    const month = date.getMonth() + 1;
    const day = date.getDate();
    
    const formatted = `${year}년 ${month}월 ${day}일`;
    
    return (
        <View style={styles.block}>
            <Text style={styles.dateText}>{formatted}</Text>
        </View>
    );
}

...
```

## StatusBar 색상 바꾸기
- 지금까지 만든 앱을 시뮬레이터에서 실행하면 iOS에서는 화면의 최상단 상태 영역이 흰색으로 보일 것입니다. 그리고 안드로이드에서는 상태 영역이 회색으로 보일것 입니다. 

![image1](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/1.png)

- 이 상태 영역을 StatusBar라고 부릅니다. 이번에는 StatusBar의 색상을 DateHead와 일치하는 색상으로 설정하겠습니다. 
- iOS에서 구현하는 방법과 안드로이드에서 구현하는 방법이 약간 다릅니다. 
- 우선 안드로이드의 StatusBar 색상을 변경합니다.

> components/DateHead.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, StatusBar} from 'react-native';

function DateHead(date) {
    const year = date.getFullYear();
    const month = date.getMonth() + 1;
    const day = date.getDate();
    
    return (
        <>
            <StatusBar backgroundColor="#26a69a" />
            <View style={styles.block}>
                <Text style={styles.dateText}>
                    {year}년 {month}월 {day}일
                </Text>
            </View>
        </>
    );
}

...

```

- StatusBar에 backgroundColor Props를 설정하면 다음과 같이 안드로이드의 StatusBar 배경색이 지정한 색상으로 변경됩니다. 

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/2.png)

- 반면 iOS의 StatusBar는 이 컴포넌트를 통해 배경색을 지정할 수 없습니다. 그 대신 SafeAreaView의 상단 여백을 없앤 다음, 그 영역을 원하는 색상을 가진 View로 채워야 합니다.

![image3](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/3.png)
> SafeAreaView의 여백

- SafeAreaView 컴포넌트는 이 그림과 같이 상단과 하단에 여백을 설정해 안전한 영역에만 UI 콘텐츠가 보이도록 제한하는데, 아쉽게도 이 컴포넌트는 한쪽 여백만 없애도록 설정할 수 없습니다.


### react-native-safe-area-context 서드 파티 라이브러리 사용하기

- 특정 부분의 여백만 비활성화하고 싶다면 react-native-safe-area-context라는 서드 파티 라이브러리를 사용해야 합니다. 
- 서드 파티 라이브러리(third party library)란 리액트 네이티브에서 공식적으로 만든 게 아니라 커뮤니티에서 오픈 소스로 공개한 라이브러리를 의미합니다.
- 프로젝트 경로에 터미널 창을 열어 다음 명령어를 입력하세요.

```
yarn add react-native-safe-area-context
```

- 서드파티 라이브러리에 네이티브 코드를 사용하기 떄문에 이를 연동해야 합니다. 안드로이드의 경우에는 yarn android할 때 이 작업이 자동으로 이루어지지만, iOS의 경우에는 ios 디렉터리에 들어가서 pod install 명령어를 실행해야 합니다. 
- 이 명령어는 Objective-C 및 스위프트의 라이브러리 관리자 CocoaPods를 통해 yarn으로 설치한 모듈의 네이티브 코드를 프로젝트에 적용해줍니다. 
- 다음 명령어를 입력해주세요.

```
cd ios
pod install
cs ../
yarn ios
yarn android
```

- 이 라이브러리를 적용할 때는 우선 App 컴포넌트에서 SafeAreaProvider 컴포넌트를 사용해 기존 내용을 모두 감싸줘야 합니다. 기존의 SafeAreaView는 지우세요.

> App.js

```jsx
import React from 'react';
import {StyleSheet} from 'react-native';
import DateHead from './components/DateHead';
import {SafeAreaProvider} from 'react-native-safe-area-context';

function App() {
    const today = new Date();
    
    return (
        <SafeAreaProvider>
            <DateHead date={today} />
        </SafeAreaProvider>
    );
}

const styles = StyleSheet.create({});

export default App;
```

- 그 다음에는 SafeAreaView도 불러와서 App을 감싸주세요. SafeAreaView는 언제나 SafeAreaProvider 내부에 위치해야 합니다. 

```jsx
import React from 'react';
import {StyleSheet} from 'react-native';
import DateHead from './components/DateHead';
import {SafeAreaProvider, SafeAreaView} from 'react-native-safe-area-context';

function App() {
    <SafeAreaProvider>
        <SafeAreaView>
            <DateHead date={today} />
        </SafeAreaView>
    </SafeAreaProvider>
}

const styles = StyleSheet.create({});

export default App;

```

- 참고로 지금은 App 컴포넌트에서 SafeAreaProvider와 SafeAreaView를 둘 다 사용하고 있지만, 나중에 여러 화면을 다루는 앱을 만들 때는 SafeAreaProvider를 App 컴포넌트 JSX의 가장 바깥 부분에서 딱 한 번만 사용하고, 다른 화면에서는 SafeAreaView 컴포넌트 하나만 사용하면 됩니다. 
- SafeAreaView에 edges라는 Props를 다음과 같이 설정해보세요.

> App.js

```jsx
import React from 'react';
import {StyleSheet} from 'react-native';
import DateHead from './components/DateHead';
import {SafeAreaProvider, SafeAreaView} from 'react-native-safe-area-context';

function App() {
    <SafeAreaProvider>
        <SafeAreaView edges={['bottom']}>
            <DateHead date={today} />
        </SafeAreaView>
    </SafeAreaProvider>
}

const styles = StyleSheet.create({});

export default App;
```

- edges Props에는 SafeArea를 적용할 모서리를 배열 형태로 전달합니다. 이와 같이 배열에 bottom 값을 넣어 설정하면 SafeArea를 하단 부분에만 적용한다는 의미입니다.
- edges 배열에는 bottom외에 top, left, right 값들을 넣을 수 있습니다. left와 right의 경우에는 디바이스를 가로 모드로 사용할 때 필요한 설정입니다. 
- 이렇게 설정하고 화면을 보면 iOS에서는 다음과 같이 StatusBar와 DateHead가 겹쳐지게 나타날 것입니다.

![image4](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/4.png)
> StatusBar와 겹쳐진 DateHead

- 겹쳐진 영역을 수정하려면 StatusBar 높이와 일치하는 빈 View를 보여주면 됩니다. StatusBar와 높이는 모바일 디바이스별로 다릅니다. iPhoneX는 44이고 하위 iphone 디바이스는 20입니다. 
- 디바이스별로 StatusBar의 크기를 설정하는 코드를 직접 구현하려면 복잡하지만 다행히 이 기능도 react-native-safe-area-context 라이브러리에 구현되어 있습니다. 

### useSafeAreaInsets로 StatusBar 높이 구하기 
- useSafeAreaInsets라는 Hook 함수를 사용하면 StatusBar의 높이를 구할 수 있습니다. 높이를 구해서 그 만큼 공백을 만들어주면 날짜가 보이는 부분이 StatusBar와 겹치지 않습니다.

> components/DateHead.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, StatusBar} from 'react-native';
import {useSafeAreaInsets} from 'react-native-safe-area-context';

function DateHead(date) {
    const year = date.getFullYear();
    const month = date.getMonth() + 1;
    const day = date.getDate();
    
    const {top} = useSafeAreaInsets();
    
    return (
        <>
            <View style={[styles.statusBarPlaceholder, {height: top}]} />
            <StatusBar backgroundColor="#26a69a" />
            <View style={styles.block}>
                <Text style={styles.dateText}>
                    {year}년 {month}월 {day}일
                </Text>
            </View>
        </>
    );
}
```

---

# TextInput으로 사용자 키보드 입력받기

