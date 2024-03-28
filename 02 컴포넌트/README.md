# 컴포넌트 
- 컴포넌트(component)를 직역하면 <code>구성 요소</code>라는 의미입니다. 프론트엔드 개발에서의 컴포넌트는 유저 인터페이스를 구성하는 요소라고 이해하면 됩니다. 
- 리액트로 프론트앤드 개발할 때는 수많은 컴포넌트를 만들면서 진행하는데, 리액트에서의 컴포넌트는 단순히 보이는 부분만 담당하는 것이 아니라, 사용자가 어떠한 행동을 취했을 때 어떠한 작업을 할지도 설정해줄 수 있습니다. 


```jsx
import React from 'react';
import {SafeAreaView, View, Text} from 'react-native';

const App = () => {
    return (
        <SafeAreaView>
            <View>
                <Text>Hello React!</Text>
            </View>
        </SafeAreaView>
    );
};

export default App;
```

- App이 바로 컴포넌트, App 컴포넌트 안에는 다른 다양한 컴포넌트가 들어 있습니다.
- **SafeAreaView** 컴포넌트는 iPhone X 이상 기종에서 디스플레이의 보이지 않는 영역 및 최하단 영역에 내용이 보여지는 것을 방지해줍니다.
- **View** 컴포넌트는 가장 기본적인 컴포넌트로 레이아웃 및 스타일을 담당합니다.
- **Text** 컴포넌트는 텍스트를 보여주는 역할을 합니다.

---
# 나만의 컴포넌트 만들기 

- 프로젝트 디렉토리에 components라는 디렉토리 생성하고 그 안에 Greeting.js 파일을 생성
- 파일을 만든 다음 가장 먼저 해야 할 일은 상단에서 <code>React</code>를 불러오는 것
- View 컴포넌트와 Text 컴포넌트 불러오기

> components/Greeting.js

```jsx
import React from 'react';
import {View. Text} from 'react-native';
```


---
# Props

---
# defaultProps 

---
# JSX 문법 

---
# StyleSheet로 컴포넌트에 스타일 입히기 

---
# Props로 객체 구조 분해 할당 

---
# useState Hook으로 상태 관리하기

---
# 카운터 만들기 