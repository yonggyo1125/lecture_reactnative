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

---

# TextInput으로 사용자 키보드 입력받기

