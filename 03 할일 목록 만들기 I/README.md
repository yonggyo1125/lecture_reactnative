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

const styles = StyleSheet.create({
    statusBarPlaceholder: {
        backgroundColor: '#26a69a',
    },
    block: {
        padding: 16, 
        backgroundColor: '#26a69a',
    },
    dateText: {
        fontSize: 24,
        color: 'white',
    }
});

export default DateHead;
```

- useSafeAreaInsets() Hook 함수를 사용하면 각 모서리에 필요한 공백의 크기를 알아볼 수 있습니다. 이 함수의 반환값에는 top 외에도 bottom, left, right가 있습니다. iOS 화면을 확인해 보면 다음과 같이 상태 영역의 배경색이 설정되어 있습니다.

![image5](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/5.png)
> iOS StatusBar 배경색 설정


### StatusBar 내용 색상 변경하기
- StatusBar의 정보 색상을 변경하는 방법은 StatusBar에 barStyle Props를 light-content로 설정하면 됩니다. 

```jsx
<StatusBar backgroundColor="#26a69a" barStyle="light-content" />
```

- barStyle 값은 dark-content(어두운 내용), light-content(밝은 내용), default(시스템 기본 설정)로 설정할 수 있습니다. / iOS, 안드로이도 공통 설정

![image6](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/6.png)
> StatusBar 내용 색상 변경


### 레이아웃 준비하기 
- 화면 최하단에 새 항목을 추가할 수 있는 AddTodo라는 컴포넌트를 배치합니다.
- 우선은 이 컴포넌트의 높이만 잡아주고, 이 컴포넌트가 차지하는 영역을 바로 볼 수 있도록 배경색을 설정합니다.

> components/AddTodo.js

```jsx
import React from 'react';
import {View, StyleSheet} from 'react-native';

function AddTodo() {
    return <View style={styles.block} />;
}

const styles = StyleSheet.create({
    block: {
        height: 64,
        backgroundColor: 'red',
    },
});

export default AddTodo;
```

- 이 컴포넌트를 App에서 불러와 DateHead 컴포넌트 하단에 사용하세요. 

> App.js

```jsx
import React from 'react';
import {StyleSheet} from 'react-native';
import DateHead from './components/DateHead';
import {SafeAreaProvider, SafeAreaView} from 'react-native-safe-area-context';
import AddTodo from './components/AddTodo';

function App() {
    const today = new Date();
    
    return (
        <SafeAreaProvider>
            <SafeAreaView edges={['bottom']}>
                <DateHead date={today} />
                <AddTodo />
            </SafeAreaView>
        </SafeAreaProvider>
    );
}

const styles = StyleSheet.create({});

export default App;
```

- DateHead와 AddTodo 사이에 공간을 만들어 줍니다.
- 둘 사이에 할일 항목 리스트를 보여주고, 만약 할일 항목이 하나도 없다면 이미지와 문구를 보여줍니다. 
- 항목이 하나도 없을 때 사용할 컴포넌트의 이름은 Empty로 하겠습니다.

> components/Empty.js

```jsx
import React from 'react';
import {View, Text, StyleSheet} from 'react-native';

function Empty() {
    return (
        <View style={styles.block}>
            <Text style={styles.description}>야효! 할일이 없습니다.</Text>
        </View>
    );
}

const styles = StyleSheet.create({
    block: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
    },
    description: {
        fontSize: 24,
        color: '#9e9e9e',
    }
});

export default Empty;
```

- 이 컴포넌트에서는 block 스타일에 flex: 1을 설정해 자신이 차지할 수 있는 영역을 모두 차지하도록 했습니다. 그리고 alignItems와 justifyContent 값을 모두 center로 설정했습니다. 그러면 내용이 중앙에 위치합니다. 
- description 스타일에는 폰트 크기와 색상을 설정했습니다. 
- 컴포넌트 코드를 다 작성했으면 App.js 파일을 열어 Empty 컴포넌트를 DateHead와 AddTodo 사이에 사용하세요.
- 아직은 Empty 컴포넌트가 보이지 않는데, 추가로 SafeAreaView 컴포넌트에 StyleSheet로 flex: 1 스타일을 적용해야 합니다.

> components/App.js

```jsx
import React from 'react';
import {StyleSheet} from 'react-native';
import DateHead from './components/DateHead';
import {SafeAreaProvider, SafeAreaView} from 'react-native-safe-area-context';
import AddTodo from './components/AddTodo';
import Empty from './components/Empty';

function App() {
    <SafeAreaViewProvider>
        <SafeAreaView edges={['bottom']} style={styles.block}>
            <DateHead date={today} />
            <Empty />
            <AddTodo />
        </SafeAreaView>
    </SafeAreaViewProvider>
}

const styles = StyleSheet.create({
    block: {
        flex: 1,
    },
});

export default App;
```

### 이미지 사용하기

- [이미지 다운로드](http://bit.ly/chapter-3-images) 
- 압축을 풀면 assets 디렉토리가 나오는데, 이를 프로젝트 디렉토리로 이동시킵니다.
- 이미지를 화면에 보여줄 때는 <code>Image</code>라는 컴포넌트를 사용합니다. 
- assets/images 디렉토리를 보면 다음과 같은 파일이 있습니다.
    - circle.png
    - circle@2x.png 
    - circle@3x.png
    - young_and_happy.png 
- 기본적으로 이미지가 young_and_happy.png와 같은 형태로 딱 하나만 있어도 불러와 사용하는 데 전혀 지장은 없습니다. 이와 같이 이미지가 여러개 있는 이유는 화면의 밀도에 따라 다른 이미지를 보여주기 위함입니다. 
- 맨 위에 @2x 또는 @3x가 붙어있지 않은 이미지의 크기는 200px X 200px 입니다. 그리고 @2x가 붙어있는 이미지의 크기는 400px X 400px(2배), @3x가 붙어있는 이미지의 크기는 600px X 600px(3배)입니다.
- 기술이 발전하면서 디바이스의 크기는 비슷하지만 화면의 해상도가 갈수록 높아지고 있습니다. 고해상도의 모바일 디바이스에서는 해상도가 높은 이미지를 보여줘야 더욱 선명하게 보입니다. 
- 예를 들어, iPhone 11 Pro의 화면 해상도는 2436px ㅌ 1125px 이고, 화면 밀도는 458ppi 입니다.

![image7](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/7.png)
> 화면 밀도 ppi

- ppi(pixel per inch)란 1inch에 몇 px이 들어갈 수 있는지를 의미하는 밀도 단위입니다. 이는 dpi(dots per inch)와 비슷한 개념입니다(ppi는 디스플레이에서 사용하는 단위이고, dpi는 인쇄물에서 사용하는 단위입니다).
- 리액트 네이티브에서 스타일링할 때 크기는 모두 dp로 설정합니다. dp(density-independent pixel)란 1인치당 픽셀 밀도에 따라 크기가 일관된 UI를 보여줄 수 있는 단위입니다.
- dp와 px을 변환하는 계산 방법

```
dp = px * 160 / ppi
px = dp * ppi / 160
```

![image8](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/8.png)
> 디바이스 해상도에 따라 다른 이미지 사용

- 파일명 뒷부분이 .ios.png로 끝나는 파일은 iOS 운영 체제에서, .android.png로 끝나는 파일은 안드로이드 운영 체제에서 보입니다. 
- Image 컴포넌트를 다음과 같이 사용해 봅시다. 

> components/Empty.js

```jsx
import React from 'react';
import {View, Text, Image, StyleSheet} from 'react-native';

function Empty() {
  return (
    <View style={styles.block}>
      <Image source={require('../assets/images/circle.png')} />
      <Text style={styles.description}>야호! 할일이 없습니다.</Text>
    </View>
  );
}

...

```

- <code>Image</code> 컴포넌트에서 사용할 경로를 지정할 때는 source라는 Props로 지정합니다. 그리고 경로를 입력할 때는 require()라는 함수로 입력해야 합니다. 
- 이 코드에서 사용한 경로에서 ../는 상위 디렉터리를 의미합니다.
- 화면에 동그라미가 나타나는데 iOS에서는 1x, 안드로이드에서는 3x 동그라미가 나타났습니다.

![image9](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/9.png)
> Image 컴포넌트 사용하기

### resizeMode 이해하기

- Image 컴포넌트는 따로 설정하지 않으면 실제 이미지 크기를 기반으로 dp 단위의 크기로 변환되어 나타납니다. 
- 예를 들어, 프로젝트에서 사용하는 circle 이미지는 별도로 설정하지 않으면 200dp X 200dp의 사이즈로 보입니다.
- 스타일로 Image 컴포넌트의 크기를 300dp X 200dp로 설정해보세요.

> components/Empty.js

```jsx
import React from 'react';
import {View, Text, Image, StyleSheet} from 'react-native';

function Empty() {
    return (
        <View style={styles.block}>
          <Image 
            source={require('../assets/images/circle.png')}
            style={styles.image} 
          />
          <Text style={styles.description}>야효! 할일이 없습니다.</Text>
        </View>
    );
}

const styles = StyleSheet.create({
    block: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
    },
    image: {
      width: 300,
      height: 200,
    },
    description: {
        fontSize: 24,
        color: '#9e9e9e',
    }
});

export default Empty;
```

![image10](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/10.png)
> 위 아래가 잘린 이미지

- 이미지의 위 아래가 잘린 채로 보입니다. 스타일로 이미지 크기를 조정할 때 실제 이미지 크기와 값이 다르면 이미지는 리사이징됩니다. 
- 리사이징할 때 다양한 옵션을 설정할 수 있는데, 이는 resizeMode를 통해서 가능합니다. 

```jsx
<Image 
  source={require('../assets/images/circle.png')}
  style={styles.image}
  resizeMode="cover"
/>
```

- resizeMode는 총 5가지 값으로 설정할 수 있습니다.
  - **cover**: resizeMode를 따로 설정하지 않으면 이 값을 기본값으로 지정합니다. 이 옵션은 이미지의 가로 세로 비율을 유지한 상태로 이미지를 리사이징합니다. 이미지와 뷰의 가로 세로 비율이 일치하지 않을 경우 이미지의 일부분이 잘립니다.
  - **contain**: 이미지의 가로 세로 비율을 유지한 상태로 이미지를 리사이징하며 이미지의 모든 영역이 뷰 안에 보이게 합니다.
  - **stretch**: 뷰의 크기대로 이미지를 리사이징합니다. 이 과정에서 이미지의 가로 세로 비율이 원본과 달라질 수 있습니다.
  - **repeat**: 뷰의 크기가 이미지의 크기보다 크면 바둑판식으로 이미지를 반복합니다. 
  - **center**: 이미지를 뷰 중앙에 둡니다. contain과 마찬가지로 이미지의 모든 영역이 뷰 안에 보이게 합니다. 단, 뷰가 이미지보다 크면 이미지의 크기가 커지지 않고 원본 사이즈를 유지합니다.
- 이해하기 쉽도록 직접 resizeMode 값을 바꿔보면서 결과를 확인해 봅시다. 
- image 스타일에 다음과 같이 배경색을 지정하면 실제 뷰의 크기를 시각적으로 파악할 수 있으니 스타일을 다음과 같이 설정

```jsx
image: {
  width: 300,
  height: 200,
  backgroundColor: 'gray',
},
```

- resizeMode Props를 cover, contain, stretch, repeat, center 순서로 바꿔보면서 다음과 같은 결과가 나타나는지 확인하세요.

![image11](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/11.png)
> resizeMode

- contain과 center는 차이가 없는 것 처럼 보입니다. 이 두 속성은 뷰의 너비와 높이가 둘 다 원본 이미지 크기보다 클 때 차이가 발생합니다.
- image의 width와 height 값을 250으로 설정해봅시다.

```jsx
image: {
  width: 250,
  height: 250,
  backgroundColor: 'gray',
}
```

![image12](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/12.png)
> contain과 center 

- contain은 이미지가 뷰의 크기에 맞춰 커진 반면, center는 원본 이미지 크기 그대로 나타났습니다. 참고로 center는 화면 밀도에 따라 디바이스별로 다른 결과가 나타날 수 있습니다(여백의 크기가 달라질 수 있습니다). 

### 외부 이미지 사용하기
- Image 컴포넌트로 프로젝트 내에 있는 이미지 외에 인터넷상 이미지도 보여줄 수 있습니다.
- 외부 이미지를 설정할 때는 다음과 같이 객체에 uri 값을 설정해 source Props로 설정하면 됩니다.

```jsx
<Image 
  source={{uri: 'https://via.placeholder.com/105'}}
  style={styles.image}
  resizeMode="contain"
/>
```

- 이 코드를 볼 때 중괄호가 두 번 겹쳐져 있어서 헷갈일 수도 있습니다. 단순히 객체 타입의 값을 Props에 넣었을 뿐이며 다음의 코드와 동일합니다.

```jsx
const source = {uri: 'https://via.placeholder.com/150'};

return (
  <View style={styles.block}>
    <Image 
      source={source}
      style={styles.image}
      resizeMode="contain"
    />
    <Text style={styles.description}>야호! 할일이 없습니다.</Text>
  </View>
);
```

![image13](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/13.png)

### young_and_happy.png 보여주기 

> components/Empty.js

```jsx 
import React from 'react';
import {View, Text, Image, StyleSheet} from 'react-native';

function Empty() {
  return (
    <View style={styles.block}>
      <Image 
        source={require('../assets/images/young_and_happy.png')}
        style={styles.image}
      />
      <Text style={styles.description}>야호! 할일이 없습니다.</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  image: {
    width: 240,
    height: 179,
    marginBottom: 16,
  },
  description: {
    fontSize: 24,
    color: '#9e9e9e',
  }
});

export default Empty;
```

![image14](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/03%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20I/images/14.png)

- 안드로이드 시뮬레이터를 자세히 보면 이미지의 배경색은 흰색인 반면 그 뒤의 화면 배경색은 연한 회색인 것을 확인할 수 있습니다. 안드로이드의 배경색은 기본적으로 흰색이 아닌 연한 회색입니다. 안드로이드 앱을 디자인할 때는 이 점에 주의해야 합니다.
- 이미지와 화면의 배경색 차이가 발생하지 않도록 App 컴포넌트의 block 스타일을 다음과 같이 변경하세요.

> App.js - styles

```jsx
const styles = StyleSheet.create({
  block: {
    flex: 1,
    backgroundColor: 'white',
  },
});
```

---

# TextInput으로 사용자 키보드 입력받기

- 키보드 입력을 받아낼 때는 <code>TextInput</code>이라는 컴포넌트를 사용합니다. 

> components/AddTodo.js
 
```jsx
import React from 'react';
import {View, StyleSheet, TextInput} from 'react-native';

function AddTodo() {
  return (
    <View style={styles.block}>
      <TextInput placeholder="할일을 입력하세요." style={styles.input} />
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    height: 64,
    paddingHorizontal: 16,
    borderColor: '#bdbdbd',
    borderTopWidth: 1,
    borderBottomWidth: 1,
    justifyContent: 'center'
  },
  input: {
    fontSize: 16,
    paddingVertical: 8,
  }
});

export default AddTodo;
```

- TextInput 컴포넌트의 placeholder는 컴포넌트에 아무것도 입력하지 않았을 때 보여줄 기본 텍스트입니다. 사용자 입력에 대한 일종의 도움말
- paddingHorizontal이라는 속성을 block에 적용하였는데, 좌우측 padding 값을 설정하겠다는 의미, 즉, 다음 코드와 동일

```jsx
paddingLeft: 16,
paddingRight: 16
```

- input 부분의 스타일에는 paddingVertical이라는 속성을 설정했는데, 위 아래 padding을 주어진 값으로 설정하겠다는 의미, 즉, 다음 코드와 동일

```jsx
paddingTop: 16,
paddingBottom: 16
```

- input의 padding은 화면에 보이지 않는 영역인데, 설정한 이유는 바로 터치 영역을 늘리기 위해서입니다. 
- 만약 이를 설정하지 않았다면 텍스트가 있는 곳을 정확히 터치해야 입력을 시작할 수 있습니다. 하지만 이렇게 padding을 설정하면 터치 위치가 텍스트에서 조금 빗나가도 입력을 시작할 수 있습니다.