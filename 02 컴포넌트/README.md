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

- 새로운 컴포넌트를 선언하는 방법은 두 가지가 있습니다. 첫 번째 방법은 함수로 컴포넌트를 선언하는 것, 두 번째 방법은 클래스로 컴포넌트를 선언하는 것입니다. 가장 많이 사용하는 방법은 함수로 선언하는 것
- 함수형태로 만드는 컴포넌트는 함수 컴포넌트라고 부릅니다.

> components/Greeting.js

```jsx
import React from 'react';
import {View, Text} from 'react-native';

function Greeting() {
    return (
        <View>
            <Text>안녕하세요, 함수 컴포넌트!</Text>
        </View>
    );   
}

export default Greeting;
```

- 함수로 선언한 컴포넌트에서는 이와 같이 XML 형태로 이루어진 내용을 반환해줘야 합니다. 이 문법을 JSX라고 부릅니다.
- 최하단의 export default Greeting은 App 컴포넌트에서도 작성한 것처럼 다른 파일에서 이 컴포넌트를 불러올 수 있게 해주는 코드입니다.

> App.js

```jsx 
import React from 'react';
import {SafeAreaView} from 'react-native';
import Greeting from './components/Greeting';

const App = () => {
    return (
        <SafeAreaView>
            <Greeting />
        </SafeAreaView>
    );
};

export default App;
```

> Metro 실행
> 
> yarn ios 또는 yarn android 명령어를 입력했을 때 실행되는 Metro가 꺼져 있다면 yarn ios 또는 yarn android를 한 번 더 입력하세요. IOS 시뮬레이터 또는 안드로이드 에뮬레이터가 켜져 있어도 Metro가 실행 중이 아니라면 앱에 변경이 반영되지 않습니다.

- App 컴포넌트를 보면 컴포넌트를 선언할 때 화살표 함수 문법을 사용했습니다.

```jsx 
const App = () => {
    (...)
}
```

- Greeting 컴포넌트에서는 function 키워드를 사용해 컴포넌트를 선언했습니다.

```jsx 
function Greeting() {
    (...)
}
```

- 두 방식은 코드 스타일이 다르나 기능적으로는 차이가 없습니다. 화살표 함수 문법을 썼을 때의 장점은, 복잡한 로직 없이 바로 반환하는 코드라면 다음과 같이 중괄호 코드 블록과 return 키워드를 생략할 수 있다는 것

```jsx
const App = () => {
    <SafeAreaView>
        <Greeting />
    </SafeAreaView>
};
```

- 함수 컴포넌트를 만들 때 어떤 방식을 사용하든 상관없습니다. 취향에 따라 마음에 드는 것을 선택하되 일관성 있게만 사용하면 됩니다.

---
# Props
- Props는 properties를 줄인 말로 컴포넌트의 속성을 의미합니다.
- Props를 사용하면 컴포넌트를 사용할 때 임의의 값을 넣어줄 수 있습니다. 

> components/Greeting.js

```jsx
import React from 'react';
import {View, Text} from 'react-native';

function Greeing(props) {
    return (
        <View>
            <Text>안녕하세요 {props.name}</Text>
        </View>
    );
}

export default Greeting;
```

- 함수에서 props라는 파라미터를 설정하고 기존에 함수 컴포넌트가 있던 부분을 {props.name}으로 교체했습니다. JSX에서는 자바스크립트 표현식을 보여줘야 할 때는 이렇게 중괄호로 감싸어 작성하면 됩니다.

- 여기에서 자바스크립트 표현식은 연산 결과, 함수 호출, 특정 변수 또는 상수를 말합니다. 

```jsx
function getDifference(a, b) {
    return a - b;
}

function Sample() {
    const value = 'hello';
    const user = {
        name: 'John Doe'
    };
    
    return (
        <View>
            <Text>{1 + 1}</Text>
            <Text>{value}</Text>
            <Text>{user.name}</Text>
            <Text>{getDifference(5, 4)}</Text>
        </View>
    );
}
```

- Greeting을 사용할 때 name 값을 적용해 봅시다. 

> components/App.js

```jsx
import React from 'react';
import {SafeAreaView} from 'react-native';
import Greeting from './components/Greeting';

const App = () => {
    return (
        <SafeAreaView>
            <Greeting name="Props" />
        </SafeAreaView>
    );
};
```

---
# defaultProps 
- 컴포넌트에 Props를 지정하지 않았을 때 사용할 기본값을 설정해주고 싶다면 defaultProps를 사용하면 됩니다.

> components/Greeting.js

```jsx
import React from 'react';
import {View, Text} from 'react-native';

function Greeting(props) {
    return (
        <View>
            <Text>안녕하세요 {props.name}!</Text>
        </View>
    );
}

Greeting.defaultProps = {
    name: '리액트 네이티브',
};

export default Greeting;
```

- name의 기본값을 '리액트 네이티브'로 지정했습니다. 코드를 저장하고 실행해 보면 '안녕하세요 리액트 네이티브!'라고 나타납니다.

---
# JSX 문법 

## 태그를 열면 반드시 닫아주기 

- 특정 내용을 홑화살괄호로 감싸준 것을 태그라고 부릅니다.  - \<Text\>
- \<Text\>안녕하세요\</Text\>라는 JSX 코드가 있다면 \<Text\>는 태그의 시작으로 여는 태그이고 \</Text\>는 태그의 끝으로 닫는 태그입니다.
- 태그를 열었으면 꼭 닫아줘야 합니다. 닫지 않으면 오류가 발생합니다.

## 스스로 닫는 태그 사용하기 

- 여는 태그와 닫는 태그 사이에 별도의 내용을 넣어야 할 필요가 없을 때는 스스로 닫는(Self-closing) 태그를 사용합니다.
- 다음과 같이 여는 태그의 뒷 부분에 / 문자를 넣으면 별도로 닫는 태그를 입력하지 않아도 됩니다.

```jsx
<Greeting />
```

## 반환할 땐 꼭 하나의 태그로 감싸기

- 컴포넌트에서 JSX를 반환할 때는 꼭 하나의 태그로 감싸져 있어야 합니다.
- 다음과 같은 코드는 오류가 발생합니다.

> components/App.js

```jsx
import React from 'react';
import {View, Text} from 'react-native';

function Greeting(props) {
    return (
        <View>
            <Text>안녕하세요 {props.name}!</Text>
        </View>
        <Text>Extra Text!</Text>
    );
}

Greeting.defaultProps = {
    name: '리액트 네이티브',
};

export default Greeting;
```

- 'Adjacent JSX elements must be wrapped in an enclosing tag'라는 오류가 나타났는데, 이 상황을 해결하는 방법은 두 가지가 있습니다. 
- 첫 번째 방법은 새로운 View 컴포넌트를 작성해 그 사이에 기존 내용을 넣는 것입니다.

> components/Greeting.js

```jsx
import React from 'react';
import {View, Text} from 'react-native';

function Greeting(props) {
    return (
        <View>
            <View>
                <Text>안녕하세요 {props.name}!</Text>
            </View>
            <Text>Extra Text!</Text>
        </View>
    );
}

Greeting.defaultProps = {
    name: '리액트 네이티브',
};

export default Greeting;
```


- 하지만 이렇게 해결하면 불필요한 View 컴포넌트를 하나 사용하게 되므로 나중에 레이아웃과 관련해 작업할 때 귀찮은 일이 발생할 수도 있습니다.
- 이보다 더 좋은 방법은 JSX Fragment를 사용하는 것입니다. 사용 방법은 빈 태그를 사용하면 됩니다.

> components/Greeting.js

```jsx
import React from 'react';
import {View, Text} from 'react-native';

function Greeting(props) {
    return (
        <>
            <View>
                <Text>안녕하세요 {props.name}!</Text>
            </View>
            <Text>Extra Text!</Text>
        </>
    );
}

Greeting.defaultProps = {
    name: '리액트 네이티브',
};

export default Greeting;
```

##  JSX 안에서 자바스크립트 표현식을 보여줄 땐 중괄호로 감싸기

- 여는 태그와 닫는 태그 사이에 자바스크립트 표현식을 사용할 때 중괄호를 사용합니다. 
- 추가로 컴포넌트의 Props를 설정할 떄도 자바스크립트 표현식을 사용해야 한다면 중괄호를 사용해야 합니다.

> components/App.js

```jsx
import React from 'react';
import {SafeAreaView} from 'react-native';
import Greeting from './components/Greeting';

const App = () => {
    const name = 'JSX';
    return (
        <SafeAreaView>
            <Greeing name={name} />
        </SafeAreaView>
    );
};
```

- 컴포넌트의 Props를 설정할 때도 자바스크립트 표현식을 사용하는 중괄호로 값을 감싸주면 됩니다. 

## 주석 작성하기 

> components/App.js

```jsx
import React from 'react';
import {SafeAreaView} from 'react-native';
import Greeting from './components/Greeting';

const App = () => {
    const name = 'JSX';
    return (
        <SafeAreaView>
            {/* 주석을 작성해봅시다. */}
            <Greeing 
                name={name} // 이름을 설정하기 
            />
        </SafeAreaView>
    );
};
```

- JSX에서 주석을 작성하는 방법
    - {/* 와 */} 사이에 주석을 넣는 것
    - // 문자를 사용해 주석을 작성하는 것입니다. JSX의 여는 태그 또는 스스로 닫는 태그에서만 사용할 수 있으며, 이 주석을 작성한 다음에는 꼭 새 줄을 입력해줘야 합니다.

---
# StyleSheet로 컴포넌트에 스타일 입히기 

- 리액트 네이티브에서는 별도의 CSS 파일에 스타일을 작성하지 않고, 자바스크립트 파일 안에 StyleSheet라는 것을 사용합니다. 

```jsx
const styles = StyleSheet.create({
  container: {
    backgroundColor: 'black', 
    color: 'white',
    borderWidth: 4,
    borderStyle: 'solid',
    borderColor: 'blue'
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold'
  }
});
```

- CSS와의 주요 차이점은 다음과 같습니다.
  - 셀렉터라는 개념이 존재하지 않습니다.
  - 모든 스타일 속성은 camelCase로 작성해야 합니다.
  - display 속성은 기본적으로 flex이며, 다른 값은 none 밖에 없습니다.
  - flexDirection 속성의 기본값은 웹에서는 row이지만, 리낵트 네이티브에서는 column입니다.
  - 리액트 네이티브에서 스타일링할 떄 숫자 단위는 dp뿐입니다.
  - background 대신 backgroundColor를 사용해야 합니다.
  - border 대신 borderWidth, borderStyle, borderColor 등을 따로따로 설정해야 합니다.

> components/Box.js

```jsx
import React from 'react';
import {View, StyleSheet} from 'react-native';

function Box() {
  return <View style={styles.box} />;
}

const styles = StyleSheet.create({
  box: {
    width: 64,
    height: 64,
    backgroundColor: 'black',
  },
});

export default Box;
```

- StyleSheet는 react-native 모듈에서 불러와서 사용할 수 있습니다. 
- 새로운 스타일을 선언할 때는 StyleSheet.create 함수를 사용하며, 그 안에 스타일을 작성합니다. 그리고 특정 컴포넌트에 스타일을 적용할 때는 style Props를 설정하면 됩니다.

> App.js 

```jsx
import React from 'react';
import {SafeAreaView} from 'react-native';
import Box from './components/Box';

const App = () => {
  return (
    <SafeAreaView>
      <Box />
    </SafeAreaView>
  );
};

export default App;
```

### Props로 컴포넌트 스타일을 커스터마이징하기

#### 부드러운 모서리 구현하기 

> components/Box.js

```jsx
import React from 'react';
import {View, StyleSheet} from 'react-native';

function Box(props) {
  return <View style={[styles.box, styles.rounded]} />;
}

const styles = StyleSheet.create({
  box: {
    width: 64,
    height: 64,
    backgroundColor: 'black',
  },
  rounded: {
    borderRadius: 16,
  }
});

export default Box;
```

- 컴포넌트 스타일을 지정할 때 여러 스타일을 적용하고 싶다면 다음과 같이 배열 형태로 설정하면 됩니다.

```jsx
<View style={[styles.box, styles.rounded]} />
```

> components/Box.js

```jsx
import React from 'react';
import {View, StyleSheet} from 'react-native';

function Box(props) {
  return <View style={[styles.box, props.rounded ? styles.rounded : null]} />;
}

...
```

- 이 코드에서는 props.rounded 값을 참조해 삼항연산자를 통해 이 값이 true면 styles.rounded를 적용하고 그렇지 않다면 null을 설정해 아무 스타일도 적용되지 않도록 처리했습니다.
- 코드를 조금 더 짧게 작성하고 싶으면 다음과 같이 수정할 수 있습니다. 

```jsx
<View style={[styles.box, props.rounded && styles.rounded]} />
```

- 이와 같이 작성하면 props.rounded 값이 true일 때는 styles.rounded 스타일을 적용하고 false일 때는 아무런 스타일도 적용하지 않습니다.

> App.js

```jsx
import React from 'react';
import {SafeAreaView} from 'react-native';
import Box from './components/Box';

const App = () => {
  return (
    <SafeAreaView>
      <Box rounded={true} />
    </SafeAreaView>
  );
};
```

- Boolean 타입의 Props를 설정할 때는 코드를 다음과 같이 간소화할 수도 있습니다.

```jsx
<Box rounded />
```

- 이렇게 Props의 이름민 적어주면 이 값을 true로 설정합니다.

### 다양한 크기 구현하기

> components/Box.js

```jsx
import React from 'react';
import {View, StyleSheet} from 'react-native';

function Box(props) {
  return (
    <View 
      style={[styles.box, props.rounded && styles.rounded, sizes[props.size]} 
     />
  );
}

Box.defaultProps = {
  size: 'medium',
};

const styles = StyleSheet.create({
  box: {
    backgroundColor: 'black',
  },
  rounded: {
    borderRadius: 16,
  },
  small: {
    width: 32,
    height: 32,
  },
  medium: {
    width: 64, 
    height: 64,
  },
  large: {
    width: 128,
    height: 128,
  }
});

const sizes = {
  small: styles.small,
  medium: styles.medium,
  large: styles.large,
};

export default Box;
```

- small, medium, large 스타일을 준비한 다음, 이 스타일들을 sizes라는 객체에 넣어줬습니다. 그리고 props.size 값을 받아와서 sizes[size]를 조회해 원하는 스타일을 선택했습니다.
- 추가로 컴포넌트에 size Props가 설정되지 않았을 때는 기본값으로 medium을 사용하도록 defaultProps를 설정했습니다.

> App.js

```jsx
import React from 'react';
import {SafeAreaView} from 'react-native';
import Box from './components/Box';

const App = () => {
  return (
    <SafeAreaView>
      <Box rounded={true} size="large" />
    </SafeAreaView>
  );
};

export default App;
```

### 사각형 색상 변경하기

- color 값은 다양하므로 크기를 위한 Props를 구현할 때처럼 사전에 스타일을 미리 선언하는 방식은 힘듭니다. 그 대신 Props로 받은 값을 스타일로 직접 넣어주면 쉽게 구현할 수 있습니다. 

> components/Box.js

```jsx
import React from 'react';
import {View, StyleSheet} from 'react-native';

function Box(props) {
  return (
    <View 
      style={[
        styles.box,
        props.rounded && styles.rounded,
        sizes[props.size],
        {
          backgroundColor: props.color,
        }
      ]}
    />
  );
}

Box.defaultProps = {
  size: 'medium',
  color: 'black',
};

...

```
- props.color 값을 사용해 스타일 객체를 직접 만들어 컴포넌트에 스타일을 적용해줬습니다.
- defaultProps로 color Props를 설정하지 않았다면 기본적으로 검은색을 사용하도록 했습니다.

- 컴포넌트의 색상을 파란색으로 만들기

> App.js

```jsx
import React from 'react';
import {SafeAreaView} from 'react-native';
import Box from './components/Box';

const App = () => {
  return (
    <SafeAreaView>
      <Box rounded={true} size="large" color="blue" />
    </SafeAreaView>
  );
};

export default App;
```

---
# Props로 객체 구조 분해 할당 

- 구조 분해 할당은 함수의 파라미터에서도 활용할 수 있습니다.

> components/Box.js

```jsx
import React from 'react';
import {View, StyleSheet} from 'react-native';

function Box({rounded, size, color}) {
  return (
    <View 
      style={[
        styles.box,
        rounded && styles.rounded,
        sizes[size], 
        {
          backgroundColor: color,
        },
      ]}
    />
  );
}

...

```

---
# useState Hook으로 상태 관리하기

- 사용자와의 상호 작용으로 유저 인터페이스에 변화를 줄수 있습니다. 이 작업을 진행하려면 상태를 관리해야 합니다. 
- 리액트에서 상태를 관리하는 가장 기본적인 방법은 <code>useState</code>라는 함수를 사용하는 것입니다. 
- 리액트에서는 use로 시작하는 다양한 함수가 내장되어 있는데 이 함수들을 Hook이라고 부릅니다. Hook을 사용하여 상태관리, 최적화, 컴포넌트 작동 흐름 관리 등 다양한 기능을 구현할 수 있습니다. 
- 그 중에서 useState는 상태 값을 관리하는 함수입니다.

## useState 사용해보기 

```jsx
import React, {useSate} from 'react';
```

> App.js

```jsx
import React, {useState} from 'react';
import {SafeViewArea} from 'react-native';
import Box from './components/Box';

const App = () => {
  const [visible, setVisible] = useState(true);
  
  return (
    <SafeAreaView>
      <Box rounded={true} size="large" color="blue" />
    </SafeAreaView>
  );
};

export default App;
```

- useState 함수는 다음과 같이 사용합니다.

```jsx
const [visible, setVisible] = useState(true);
```

- 여기에서 visible은 상태 값을 가리킵니다. setVisible은 상태 값을 변경할 수 있는 함수, setVisible(true)를 호출하면 visible 값이 true로 바뀝니다.
- useState함수에 넣어준 파라미터는 상태 값의 초기값입니다.

## Hook의 규칙 

- Hook은 컴포넌트의 최상위 레벨에서만 사용해야 합니다. 즉, Hook은 조건문이나 반복문 또는 중첩 함수에서 호출되면 안됩니다. 만약 함수의 흐름 중간에 리턴을 하는 경우(early return)에는 Hook은 함수가 리턴되기 전에 사용되어야 합니다. 
- 여러 Hook을 사용하여 직접 Hook을 만들 수 있습니다. 이를 커스텀(Custom) Hook이라고 부릅니다. react 패키지 외에서 불러오는 Hook은 모두 커스텀 Hook입니다.
- Hook은 커스텀 Hook 또는 함수 컴포넌트에서만 사용할 수 있습니다. 클래스에서는 사용이 불가능하며, 리액트와 관련없는 일반적인 자바스크립트 함수에서 사용하면 오류가 발생합니다.

## Button 컴포넌트 사용하기 

> App.js

```jsx
import React, {useState} from 'react';
import {SafeAreaView, Button} from 'react-native';
import Box from './components/Box';

const App = () => {
  const [visible, setVisible] = useState(true);
  const onPress = () => {
    setVisible(!visible);
  };
  
  return (
    <SafeAreaView>
      <Button title="토글" onPress={onPress} />
      <Box rounded={true} size="large" color="blue" />
    </SafeAreaView>
  );
};

export default App;
```

## 조건부 렌더링 구하기

- 조건부 렌더링이란 특정 조건에 따라 다른 결과물을 보여주는 것을 의미합니다. visible 값이 true일 때만 Box 컴포넌트를 보여줘야 하는데, 이를 구현하는 방법은 두 가지가 있습니다.
- 첫 번째 방법은 삼항연산자를 사용한는 것

> App.js

```jsx
import React, {useState} fron 'react';
import {SafeAreaView, Button} from 'react-native';
import Box from './components/Box';

const App = () => {
  const [visible, setVisible] = useState(true);
  
  const onPress = () => {
    setVisible(!visible);
  };
  
  return (
    <SafeAreaView>
      <Button title="토글" onPress={onPress} />
      {visible ? <Box rounded={true} size="large" color="blue" /> : null}
    </SafeAreaView>
  );
};

export default App;
```

- 두 번째 방법은 && 연산자를 사용하는 것

```jsx
{visible && <Box rounded={true} size="large" color="blue" />
```

---
# 카운터 만들기 

## UI 준비하기 
- 카운터는 화면의 전체 영역을 사용합니다. 따라서 가장 먼저 해야 하는 작업은 App 컴포넌트의 SafeAreaView가 전체 영역을 사용하도록 설정하는 것
- 이 작업은 flex 스타일 속성을 1로 설정하면 됩니다.

> App.js 

```jsx
import React, { useState } from 'react';
import {SafeAreaView, StyleSheet} from 'react-native';

const App = () => {
  return (
    <SafeAreaView style={styles.full}>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  full: {
    flex: 1,
  },
});

export default App;
```

> flex 스타일 속성을 1로 설정하는 것은 자신이 위치한 곳의 영역을 모두 차지하겠다는 의미
 
- components 디렉토리에 Counter.js 파일을 생성해 다음과 같이 코드를 작성해주세요.

> components/Counter.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, Button} from 'react-native';

function Counter() {
  return (
    <View style={styles.wrapper}>
      <View style={styles.numberArea}>
        <Text style={styles.number}>0</Text>
      </View>
      <Button title="+1" />
      <Button title="-1" />
    </View>
  );
}

const styles = StyleSheet.create({
  wrapper: {
    flex: 1,
  },
  numberArea: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  number: {
    fontSize: 72, 
    fontWeight: 'bold',
  },
});

export default Counter;
```

> 여기에서 alignItems는 가로 정렬을 의미하고 justifyContent는 세로 정렬을 의미합니다. 
> 만약 이 컴포넌트의 flexDirection을 row로 바꾸면 alignItems가 세로 정렬을 의미하고, justifyContent가 가로 정렬을 의미하게 됩니다. 

> App.js

```jsx
import React, {useState} from 'react';
import {SafeAreaView, StyleSheet} from 'react-native';
import Counter from './components/Counter';

const App = () => {
  return (
    <SafeAreaView style={styles.full}>
      <Counter />
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  full: {
    flex: 1,
  }
});

export default App;
```


## Counter 컴포넌트에 Props 설정하기

- 이 컴포넌트에 총 세가지 Props를 설정할 것입니다.
  - **count**: 화면 중앙에 보여줄 값입니다.
  - **onIncrease**: +1 버튼을 눌렀을 때 호출할 함수입니다.
  - **onDecrease**: -1 버튼을 눌렀을 때 호출할 함수입니다.

> components/Counter.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, Button} from 'react-native';

function Counter({count, onIncrease, onDecrease}) {
  return (
    <View style={styles.wrapper}>
      <View style={styles.numberArea}>
        <Text style={styles.number}>{count}</Text>
      <View>
      <Button title="+1" onPress={onIncrease} />
      <Button title="-1" onPress={onDecrease} />
    </View>
  );
}

...

```

## App에서 상태 관리하기

- App 컴포넌트에서 Counter 컴포넌트에 전달해야 할 상태를 관리해주겠습니다.

```jsx
import React, { useState } from 'react';
import {SafeAreaView, StyleSheet} from 'react-native';
import Counter from './components/Counter';

const App = () => {
  const [count, setCount] = useState(0);
  
  const onIncrease = () => setCount(count + 1);
  const onDecrease = () => setCount(count - 1);
  
  return (
    <SafeAreaView style={styles.full}>
      <Counter count={count} onIncrease={onIncrease} onDecrease={onDecrease} />
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  full: {
    flex: 1,
  }
});

export default App;

```

- useState를 사용할 때는 기본값으로 숫자 타입인 0을 넣었습니다.

```jsx
const [count, setCount] = useState(0);
```

- 이 값을 변경하는 onIncrease와 onDecrease 함수를 만들었습니다.

```jsx
const onIncrease = () => setCount(count + 1);
const onDecrease = () => setCount(count - 1);
```

- 이를 Counter 컴포넌트에 Props로 전달해줍니다.

```jsx
<Counter count={count} onIncrease={onIncrease} onDecrease={onDecrease} />
```

> 컴포넌트에 Props를 설정할 때 레퍼런스의 이름이 Props의 이름과 무조건 같아야 할 필요는 없습니다.