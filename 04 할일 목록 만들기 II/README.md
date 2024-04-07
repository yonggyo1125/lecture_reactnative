# 불변성을 지키면서 객체와 배열 업데이트 하기

## 불변성을 지켜야 하는 이유 
- 리액트에서 불변성을 지켜야 하는 이유는 렌더링 성능 최적화 방식 때문입니다. 리액트에서는 부모 컴포넌트가 리렌더링(상태가 업데이트되어 다시 렌더링되는 것)되면 기본적으로 자식 컴포넌트 또한 리렌더링 됩니다. 예를 들어, 우리가 만든 프로젝트의 App 컴포넌트가 리렌더링 되면 DateHead, Empty, AddTodo가 모두 리렌더링됩니다. 즉, 작성한 함수들이 한번 더 호출되는 것입니다. 문제는 변경사항이 없을 때도 리렌더링된다는 것입니다. 
- 리액트는 최적화가 정말 많이 되어 있기 때문에 렌더링이 불필요한 컴포넌트가 리렌더링된다고 해서 일반적으로 성능에 부하가 많이 발생하지는 않습니다. 컴포넌트가 리렌더링된 후에는 그 결과물이 기존 결과물과 차이가 있으면 화면에 보이는 UI를 건드리고, 그렇지 않으면 기존 UI를 그대로 유지합니다.
- 그런데 컴포넌트에서 다루는 데이터가 많아지거나 연산량이 늘어난 경우에는 컴포넌트에 정말 변화가 발생했을 때만 리렌더링하도록 최적화할 수 있습니다. 컴포넌트에 변화가 필요한지 필요하지 않은지는 바로 Props의 변화를 통해서 알 수 있습니다. 컴포넌트의 렌더링 성능을 최적화하기 위해서는 이전에 컴포넌트가 들고 있던 Props와 새로 받아올 Props를 비교하는 과정이 필요합니다. 이 과정에서 불변성을 유지하는 것이 필요합니다. 

```javascript
const data = { id: 1, text: '안녕하세요' };

const sameData = data;
sameData.text = '안녕하세요';

console.log(sameData === data); // true
```

- sameData에 기존의 data를 대입하고, text 값을 수정했습니다. 비록 새로운 이름을 사용해 선언하긴 했지만 sameData와 data는 똑같은 객체를 가리키고 있습니다. 따라서 sameData.text 값을 바꾸면 당연히 data.text 값 또한 바뀌게 됩니다. sameData와 data는 완전히 일치하는 객체이기 때문에 이를 비교하면 결과는 true가 나타납니다. 
- 하지만 불변성을 유지하면서 상태를 업데이트 한다면 달라집니다.

```javascript
const data = { id: 1, text: '안녕하세요' };
const nextData = { ...data, text: '안녕히계세요' };

console.log(nextData === data); // false
```

- 이번에 선언한 nextData는 새로운 객체입니다. 이 객체에는 기존의 data 값을 spread 연산자를 사용해 펼쳐주고, text 값을 새로운 값으로 덮어씌워줬습니다. 두 객체는 서로 완전히 다른 객체이기 때문에 둘을 비교하면 결과는 false가 나타납니다.

## 배열의 불변성을 지키는 방법 

- 리액트에서 배열 타입의 상태를 다룰 때에도 불변성을 지켜야 합니다. 

```javascript
const numbers = [0, 1, 2, 3];
// 추가 
numbers.push(4);

// 수정
numbers[0] = 10;

// 삭제 
numbers.splice(0, 1);
```

- 이 코드는 모두 배열을 직접 수정합니다. 리액트에서 상태를 관리할 때는 이와 같이 코드를 작성하면 안됩니다. 그 대신 배열의 내장 함수들을 활용해 새로운 배열을 생성하는 방식으로 배열 상태를 업데이트해야 합니다. 

### 새로운 항목 추가하기 
- 불변성을 유지하면서 배열에 새로운 항목을 추가하는 방법은 두 가지가 있습니다. 첫 번쨰 방법은 spread 연산자를 사용하는 것입니다. 

```javascript
const numbers = [0, 1, 2, 3];
const nextNumbers = [...numbers, 4];
```

- 두 번째 방법은 배열의 내장 함수 concat을 사용하는 것입니다. 

```javascript
const numbers = [0, 1, 2, 3];
const nextNumbers = numbers.concat(4); 
```

- concat을 사용해 1개 이상의 값을 넣을 수도 있습니다. 

```javascript
const numbers = [0, 1, 2, 3];
const nextNumbers = numbers.concat([4, 5, 6]);
console.log(nextNumbers);
```

### 항목 제거하기
- 배열에서 특정 항목을 제거해 새로운 배열을 만드는 방법 또한 다양합니다. 가장 추천하는 방법은 배열 내장 함수 filter를 사용하는 것입니다. 
- filter 함수는 배열에서 특정 조건을 만족하는 원소들로 이루어진 새로운 배열을 만들어줍니다. 

```javascript
const numbers = [-3, -2, -1, 0, 1, 2, 3];
const filtered = numbers.filter(number => number > 0);
console.log(filtered); // [1, 2, 3];
```

- 이 코드에서는 filter를 통해 값이 0보다 큰 원소들을 모아서 새로운 배열을 만들었습니다. 특정 값만 없애고 싶다면 다음과 같이 코드를 입력하면 됩니다. 

```javascript
const numbers = [-2, -2, -1, 0, 1, 2, 3];
const nextNumbers = numbers.filter(number => number !== 0);
console.log(nextNumbers);
```

- 배열에서 숫자 0과 일치하지 않는 값들을 필터링해서 0을 제거했습니다. 그런데 이런 방법에는 문제점이 하나 있습니다. 만약 0이 여러 개라면 모든 0이 사라지게 됩니다. 따라서 이와 같은 상황에서는 실제 값을 직접 비교해 삭제하는 것보다 index를 삭제하는 것이 더 좋습비니다. 
- 예를 들어, 이 코드에서 numbers 안에 숫자 0의 index는 3입니다. index가 3인 항목을 지우고 싶다면 다음과 같이 코드를 입력하면 됩니다. 

```javascript
const numbers = [-3, -2, -1, 0, 1, 2, 3];
const nextNumbers = numbers.filter((number, i) => i !== 3);
console.log(nextNumbers);
```

- filter 함수에 넣는 인자 함수에서 두 번째 파라미터로 항목의 index를 받아올 수 있습니다. 만약 이 값이 불필요하다면 생략해도 되지만 필요할 때는 이렇게 두 번째 파라미터로 조회하면 됩니다. 또한, 화살표 함수를 작성할 경우 파라미터가 하나뿐일 때는 괄호를 생략해도 되지만 두 개 이상일 때는 괄호가 반드시 있어야 합니다. 
- 이번에는 여러 객체로 이루어진 배열을 다룰 때 어떻게 해야 하는지 알아봅시다.

```javascript
const items = [{ id: 1 }, { id: 2}, { id: 3 }, { id: 4 }];
```

- 이 배열에서 id 값이 3인 객체를 지우고 싶다고 가정해봅시다. 기존에 작업한 것과 별 다를 것이 없습니다.

```javascript
const items = [{ id: 1 }, { id: 2}, { id: 3 }, { id: 4 }];
const nextItems = items.filter(item => item.id !== 3);
console.log(nextItems);
```

- 특정 항목을 제거할 때는 다음 방법으로 제거해도 괜찮습니다. 

```javascript
const items = [{ id: 1 }, { id: 2}, { id: 3 }, { id: 4 }];
const index = items.findIndex(item => item.id === 3); // 2
const nextItems = [...items].splice(index, 1);
console.log(nextItems);
```

- findIndex 내장 함수는 배열에서 특정 조건을 만족하는 원소의 index 값을 조회합니다. 배열의 내장 함수 splice는 사실 불변성을 지키는 함수가 아닙니다. 하지만 이 함수를 사용하기 전에 \[...items\] 코드를 사용해 기존 items 배열 안에 있던 내용을 넣은 새로운 배열을 만들었기 때문에 items 배열은 변함없이 기존 상태를 그대로 유지하게 됩니다. 따라서 작업 자체는 기존 items의 불변성을 유지합니다.
- filter 사용을 권장합니다. filter를 사용하는 편이 간결하기 때문입니다.

### 항목 수정하기 

- 불변성을 지키면서 배열의 특정 항목을 수정하는 방법도 다양합니다. 그 중에서 가장 추천하는 방식은 배열의 내장 함수 map을 사용하는 것입니다.
- map 함수는 기본적으로 배열 안의 모든 값에 영향을 줍니다. 그런데 조건부로 처리하면 원하는 값에만 변화를 줄 수 있습니다.
- 예를 들어, 다음 배열에서 숫자 0을 10으로 바꾸고 싶다고 가정해봅시다.

```javascript
const numbers = [-3, -2, -1, 0, 1, 2, 3];
```

- 그러면 다음과 같이 코드를 작성해 0을 10으로 바꾼 새로운 배열을 생성할 수 있습니다

```javascript
const numbers = [-3, -2, -1, 0, 1, 2, 3];
const nextNumbers = numbers.map(number => number === 0 ? 10 : number);
console.log(nextNumbers); // [-3, -2, -1, 10, 1, 2, 3]
```

- 여러 객체로 이루어진 배열을 다룰 때도 마찬가지입니다. 한 가지 주의할 점은, 객체 내부의 값을 업데이트할 때도 불변성을 유지하면서 업데이트해야 한다는 점입니다.

```javascript
const items = [
      { id: 1, text: '안녕하세요' },
      { id: 2, text: '환영합니다' },
      { id: 3, text: '반갑습니다' }
];
```

- 이 배열에서 id 값이 2인 항목의 text 값을 ‘안녕히계세요’로 업데이트하는 코드를 작성해보겠습니다.

```javascript
const items = [
      { id: 1, text: '안녕하세요' },
      { id: 2, text: '환영합니다' },
      { id: 3, text: '반갑습니다' }
];
const nextItems = items.map(item => item.id === 2 ? { ...item, text: '안녕히계세요' } : item);
console.log(nextItems);
```
- 배열 내부의 객체를 업데이트할 때는 이 코드와 같이 spread 연산자를 사용해 불변성을 유지해야 합니다.
- 항목을 제거할 때 작성한 것처럼 \[...items\]와 findIndex를 사용해 특정 항목을 업데이트할 수도 있습니다.


```javascript
const items = [
      { id: 1, text: '안녕하세요' },
      { id: 2, text: '환영합니다' },
      { id: 3, text: '반갑습니다' }
];
const index = items.findIndex(item => item.id === 2); // 1
const nextItems = [...items];
nextItems[index] = {
    ...nextItems[index],
    text: '안녕히계세요'
};
console.log(nextItems);
```

- 출력되는 결과는 이전과 동일합니다. 이 코드에서 nextItems\[index\] = 형태의 대입 코드는 불변성을 유지하는 코드가 아니지만, nextItems라는 배열을 새로 만들어 해당 배열을 수정한 것이므로 기존의 items 배열은 건드리지 않았습니다. 따라서 items 배열의 불변성이 유지되기 때문에 이 코드 또한 문제가 없습니다.
- map을 사용해 업데이트하는 것이 더 간결하기 때문에 map을 사용해 배열의 원소를 업데이트하는 것을 권장합니다.

---
# todos 상태 만들기 및 FlatList로 항목 화면에 나타내기

## todos 상태 만들기
- todos 상태는 App 컴포넌트에서 만들겠습니다. 기본으로 보여줄 할일 항목 세 가지도 배열 안에 추가합니다.

> App.js

```jsx
import React, {useState} from 'react';
...

function App() {
    const today = new Date();
    const [todos, setTodos] = useState([
      {id: 1, text: '작업환경 설정', done: true},
      {id: 2, text: '리액트 네이티브 기초 공부', done: false},
      {id: 3, text: '투두리스트 만들어보기', done: false},
    ]);
    
    ...
}
```

- 사용할 할일 항목을 가리키는 객체는 다음 값을 지닙니다.
    - id: 각 항목의 고유 ID 값
    - text: 화면에서 보여줄 text 값
    - done: 할일의 완료 여부를 나타내는 값    

## TodoList 컴포넌트 만들기

- TodoList 컴포넌트를 만들고 해당 컴포넌트 내부에 FlatList를 사용해 여러 항목을 보여주겠습니다. components 디렉터리에 TodoList.js 파일을 생성해 다음과 같이 코드를 작성

> components/TodoList.js

```jsx
import React from 'react';
import {FlatList, View, Text, StyleSheet} from 'react-native';

function TodoList({todos}) {
    return (
        <FlatList 
            style={styles.list}
            data={todos}
            renderItem={({item}) => (
                <View>
                   <Text>{item.text}</Text>
                </View>
            )}
            keyExtractor={item => item.id.toString()} 
        />
    );
}

const styles = StyleSheet.create({
    list: {
        flex: 1,
    },
});

export default TodoList;
```

- TodoList 컴포넌트는 todos Props를 받아와서 해당 값을 FlatList의 data Props로 설정해줍니다. 이렇게 data Props를 설정하면 renderItem이라는 함수를 통해 배열 안의 각 원소들 데이터를 가리키는 뷰를 보여줄 수 있습니다.
- keyExtractor Props는 각 항목의 고유 값을 추출해주는 함수입니다. 현재 프로젝트에서는 각 항목 데이터의 id 값이 항목의 고유 값입니다. 리스트를 렌더링할 때는 고유 값이 있어야 합니다. 고유 값이 존재하지 않는 경우에는 기본적으로 항목의 순서값인 index를 사용합니다. 이 경우 배열 내부에 변동사항이 생기면 UI를 비효율적으로 업데이트합니다. 따라서 성능이 좋지 않으니 주의해주세요. 추가로 고유 값은 문자열 타입이어야 하므로 고유 값이 숫자라면 .toString()을 호출해 문자열로 변환해야 합니다.
- TodoList 컴포넌트를 다 작성했다면 해당 컴포넌트를 App에서 사용해봅시다. todos 배열의 length가 0일 때는 Empty 컴포넌트를, 그렇지 않을 때는 TodoList 컴포넌트를 보여주도록 코드를 수정해보세요.

> App.js

```jsx
...

import TodoList from './components/TodoList';

function App() {
    const today = new Date();
    
    const [todos, setTodos] = useState([
        {id: 1, text: '작업환경 설정', done: true},
        {id: 2, text: '리액트 네이티브 기초 공부', done: false},
        {id: 3, text: '투두리스트 만들어보기', done: false},
    ]);
    
    return (
        <SafeAreaProvider>
            <SafeAreaView edges=[{'bottom'}] style={styles.block}>
                <KeyboardAvoidingView
                    behavior={Platform.select({ios: 'padding', android: undefined})}
                    style={styles.avoid}>
                    <DateHead date={today} />
                    {todos.length === 0 ? <Empty /> : <TodoList todos={todos} />}
                    <AddTodo />
               </KeyboardAvoidingView>
            </SafeAreaView>
        </SafeAreaProvider>
    );
}

...

```

![image1](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/1.png)
> TodoList 보여주기

## TodoItem 컴포넌트 만들기

- TodoItem이라는 컴포넌트를 만들어 스타일링도 하고, 나중에 기능을 붙일 수 있도록 해보겠습니다. 
- components 디렉터리에 TodoItem.js 파일을 생성하세요. 그리고 다음 코드를 작성해보세요.

> components/TodoItem.js

```jsx
import React from 'react';
import {View, Text, StyleSheet} from 'react-native';

function TodoItem({id, text, done}) {
  return (
    <View style={styles.item}>
      <View style={styles.circle} />
      <Text style={styles.text}>{text}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  item: {
    flexDirection: 'row',
    padding: 16,
    alignItems: 'center',
  },
  circle: {
    width: 24,
    height: 24,
    borderRadius: 12,
    borderColor: '#26a69a',
    borderWidth: 1,
    marginRight: 16,
  },
  text: {
    flex: 1,
    fontSize: 16,
    color: '#212121',
  },
});


export default TodoItem;
```

- 이 컴포넌트에서는 id, text, done 값을 Props로 받아옵니다. 지금 당장은 id를 사용하지 않지만 추후 업데이트/삭제를 구현할 때 해당 값이 필요합니다. 또한, done 값에 따라 완료됐을 때는 다른 뷰를 보여줘야 합니다.
- 방금 만든 컴포넌트를 TodoList에서 불러와 renderItem 부분에서 사용해보세요

> components/TodoList.js

```jsx 
import React from 'react';
import {FlatList, View, Text, StyleSheet} from 'react-native';
import TodoItem from './TodoItem';

function TodoList({todos}) {
  return (
    <FlatList 
      style={styles.list}
      data={todos}
      renderItem={(item) => (
        <TodoItem id={item.id} text={item.text} done={item.done} />
      )}
      keyExtractor={item => item.id.toString()}
    />
  );
}

const styles = StyleSheet.create({
  list: {
    flex: 1,
  },
});

export default TodoList;
```

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/2.png)
> TodoItem 컴포넌트 스타일링

## 항목 사이에 구분선 보여주기

- TodoItem 컴포넌트와 다른 TodoItem 컴포넌트 사이에 구분선을 보여주는 방법을 알아보겠습니다.
- 웹에서는 CSS의 & + & 셀렉터를 통해 특정 엘리먼트들 사이에만 테두리를 보여주게 설정할 수 있는데요. 리액트 네이티브에서는 셀렉터 기능이 존재하지 않습니다.
- 그 대신 FlatList에 ItemSeparatorComponent Props를 지정해 컴포넌트 사이에 구분선을 설정할 수 있습니다.

```jsx
import React from 'react';
import {FlatList, View, StyleSheet} from 'react-native';
import TodoItem from './TodoItem';

function TodoList({todos}) {
  return (
    <FlatList 
      ItemSeparatorComponent={() => <View style={styles.sparator} />}
      style={styles.list}
      data={todos}
      renderItem={(item) => (
        <TodoItem id={item.id} text={item.text} done={item.done} />
      )}
      keyExtractor={item => item.id.toString()}
    />
  );
}

const styles = StyleSheet.create({
  list: {
    flex: 1,
  },
  sparator: {
    backgroundColor: '#e0e0e0',
    height: 1
  }
});

export default TodoList;
```

![image3](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/3.png)
> 항목 사이에 구분선 보여주기

## 완료한 항목에 다른 스타일 적용하기

- App 컴포넌트에서 선언한 todos 배열의 첫 번째 항목을 보면 done 값을 true로 설정해 해당 할 일이 완료됐다는 정보를 지니도록 했습니다. 완료 항목과 미완료 항목을 구분할 수 있도록 done 값이 true일 때 다른 스타일을 적용해봅시다.
- 완료 항목은 좌측 원의 배경색을 채우고 체크 아이콘을 보여줄 것입니다. 아이콘은 assets/check_image/check_white.png 이미지 파일을 사용하겠습니다.

> components/TodoItem.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, Image} from 'react-native';

function TodoItem({id, text, done}) {
  return (
    <View style={styles.item}>
      <View style={[styles.circle, done && styles.filled]}>
        {done && (
          <Image 
            source={require('../assets/icons/check_white/check_white.png')}
          />
        )}
      </View>
      <Text style={[styles.text, done && styles.lineThrough]}>{text}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  item: {
    flexDirection: 'row', 
    padding: 16,
    borderBottomColor: '#e0e0e0',
    alignItems: 'center',
  },
  circle: {
    width: 24,
    height: 24,
    borderRadius: 12,
    borderColor: '#26a69a',
    borderWidth: 1,
    marginRight: 16,
  },
  filled: {
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#26a69a',
  },
  text: {
    flex: 1,
    fontSize: 16,
    color: '#212121',
  },
  lineThrough: {
    color: '#9e9e9e',
    textDecoration: 'line-through',
  }
});

export default TodoItem;
```

![image4](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/4.png)
> 완료 항목 구분하기 

---
# 새 항목 등록하기

- 이제 App 컴포넌트의 todos 상태에 따라 화면에 할일 항목이 나타납니다. AddTodo 컴포넌트 기능을 마저 구현해 할일을 등록해봅시다.
- 우선 App 컴포넌트에 onInsert라는 함수를 다음과 같이 구현한 다음, 해당 함수를 AddTodo의 Props로 설정해주세요.

> App.js

```jsx
...

function App() {
    const today = new Date();
    const [todos, setTodos] = useState([
      {id: 1, text: '작업환경 설정', done: true},
      {id: 2, text: '리액트 네이티브 기초 공부', done: false},
      {id: 3, text: '투두리스트 만들어보기', done: false},
    ]);
    
    const onInsert = text => {
      // 새로 등록할 할목의 id를 구합니다.
      // 등록된 항목 중에서 가장 큰 id를 구하고, 그 값에 1을 더합니다. 
      // 만약 리스트가 비어 있다면 1을 id로 사용합니다. 
      const nextId = todos.length > 0 ? Math.max(...todos.map(todo => todo.id)) + 1 : 1;
      const todo = {
        id: nextId,
        text,
        done: false
      };
      
      setTodos(todos.concat(todo));
    };
    
    return (
      <SafeAreaProvider>
        <SafeAreaView edges={['bottom']} style={styles.block}>
          <KeyboardAvoidingView 
            behavior={Platform.select({ios: 'padding'})}
            style={styles.avoid}>
             <DateHead date={today} />
             {todos.length === 0 ? <Empty /> : <TodoList todos={todos} />}
             <AddTodo onInsert={onInsert} />
          </KeyboardAvoidingView>
        </SafeAreaView>
      </SafeAreaProvider>
    );
}
```

- onInsert 함수를 다 작성했으면 해당 함수를 AddTodo 컴포넌트에서 사용해보세요.

> components/AddTodo.js

```jsx
...

function AddTodo({onInsert}) {
  const [text, setText] = useState('');
  
  const onPress = () => {
    onInsert(text);
    setText('');
    Keyboard.dismiss();
  };
  
  ...
}

...
```

- onInsert를 Props로 받아와서 onPress 함수에서 현재 text 상태를 인자로 넣어 호출하면 됩니다.

![image5](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/5.png)
> 새 항목 추가 

> 안드로이드 시뮬레이터에서는 한글 키보드가 기본적으로 등록되어 있지 않습니다. 따라서 영어로 입력하거나 안드로이드 설정에 들어가서 System > Languages & Input > Languages에서 한국어를 추가한 뒤 입력하세요.


---
# 할일 완료 상태 토글하기 

- 이번에는 할일 완료 상태를 토글하는 기능을 구현해보겠습니다. 토글(Toggle)이란 하나의 값을 다른 값으로 전환하는 것을 의미하는데요. 우리 프로젝트의 경우 done 값을 false → true, true → false로 변경하게 됩니다.
- 우선 App 컴포넌트에 onToggle이라는 함수를 다음과 같이 작성하고, 해당 함수를 TodoList의 Props로 설정해주세요. 이 함수는 항목의 고유 값인 id를 파라미터로 받아옵니다.

> App.js

```jsx
...
function App() {
  const today = new Date();
  const [todos, setTodos] = useState([
    {id: 1, text: '작업환경 설정', done: true},
    {id: 2, text: '리액트 네이티브 기초 공부', done: false},
    {id: 3, text: '투두리스트 만들어보기', done: false},
  ]);
  
  
  const onInsert = text => {
    // 새로 등록할 항목의 id를 구합니다.
    // 등록된 항목 중에서 가장 큰 id를 구하고, 그 값에 1을 더합니다.
    // 만약 리스트가 비어있다면 1을 id로 사용합니다.
    const nextId =
      todos.length > 0 ? Math.max(...todos.map(todo => todo.id)) + 1 : 1;
      const todo = {
        id: nextId,
        text,
        done: false,
      };

      setTodos(todos.concat(todo));
  };
  
  const onToggle = id => {
     const nextTodos = todos.map(todo => 
      todo.id === id ? {...todo, done: !todo.done} : todo,
  };
  
  setTodos(nextTodos);
  
  return (
    <SafeAreaProvider>
      <SafeAreaView edges={['bottom']} style={styles.block}>
        <KeyboardAvoidingView 
          behavior={Platform.select({ios: 'padding'})}
          style={styles.avoid}>
          <DateHead date={today} />
          {todos.length === 0 ? (
            <Empty />
          ) : (
            <TodoList todos={todos} onToggle={onToggle} />
          )}
          <AddTodo onInsert={onInsert} />
        </KeyboardAvoidingView>
      </SafeAreaView>
    <SafeAreaProvider>
  );
}

...

```

- 작성한 onToggle 함수의 특정 항목 업데이트 원리는 odos 배열의 모든 항목에 대해 todo.id 값이 함수의 파라미터로 받아온 id 값과 일치하면 done 값을 반전시키고, 그렇지 않으면 기존 객체를 그대로 유지하겠다는 의미입니다.
- 이제 TodoList 컴포넌트를 열어 방금 받아온 onToggle 함수를 TodoItem 컴포넌트에 그대로 Props로 전달해주세요.

> components/TodoList.js

```jsx
import React from 'react';
import {FlatList, View, StyleSheet} from 'react-native';
import TodoItem from './TodoItem';

function TodoList({todos, onToggle}) {
  return (
    <FlatList 
      ItemSeparatorComponent={() => <View style={styles.separator} />}
      style={styles.list}
      data={todos}
      renderItem={({item}) => (
        <TodoItem 
          id={item.id}
          text={item.text}
          done={item.done}
          onToggle={onToggle}
        />
      )}
      keyExtractor={item => item.id.toString()}
    />
  );
}

...

```

- 다음으로 TodoItem 컴포넌트에서 onToggle을 사용합니다. 
- 좌측에 체크 아이콘이 나타나는 원을 터치할 수 있는 영역으로 만들 텐데, 그렇게 하기 위해서 필요한 컴포넌트를 TouchableOpacity 컴포넌트로 감싸주겠습니다.

> components/TodoItem.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, Image, TouchableOpacity} from 'react-native';

function TodoItem({id, text, done, onToggle}) {
  return (
    <View style={styles.item}>
      <TouchableOpacity onPress={() => onToggle(id)}>
        <View style={[styles.circle, done && styles.filled]}>
          {done && (
            <Image 
              source={require('../assets/icon/check_white.png')}
            />
          )}
        </View>
      </TouchableOpacity>
      <Text style={[styles.text, done && styles.lineThrough]}>{text}</Text>
    </View>
  );
}

...

```

- TouchableOpacity의 onPress 부분을 보면 () => onToggle(id)와 같은 형태로 Props 설정 부분에서 바로 함수를 선언해 넣어줬습니다. 
- 함수를 만들어 특정 상수 또는 변수에 담지 않고 바로 사용하는 함수를 익명 함수라고 부릅니다. 만약 함수가 간결하고, 사용하는 곳이 한 곳밖에 없다면 앞의 코드처럼 익명 함수를 작성하면 됩니다.
- 여기서 주의할 점은 절대로 onPress={onToggle(id)}와 같이 코드를 작성하면 안 된다는 점입니다. 이렇게 코드를 작성하면 컴포넌트가 렌더링될 때마다 onToggle이 호출되는 상황이 발생합니다. onToggle이 호출되면 컴포넌트는 또 리렌더링되고, 결국 앱에서 오류가 발생하게 됩니다.

![image6](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/6.png)
> 항목 토글하기 

---
# 항목 삭제하기 

- 프로젝트의 마지막 기능인 항목을 삭제하는 기능을 구현해봅시다. 다음과 같이 완료한 항목 우측에 삭제 아이콘을 표시하고, 이 아이콘을 누르면 항목을 삭제하게 만들어보겠습니다.

![image7](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/7.png)
> 항목 삭제 UI 미리보기

## 벡터 아이콘 사용하기

- 이번에 보여줄 삭제 아이콘은 Image 컴포넌트를 사용하지 않습니다. react-native-vector-icons라는 라이브러리로 벡터 아이콘을 사용해보겠습니다.
- 벡터 아이콘은 폰트 또는 SVG를 사용해 크기가 조정돼도 아이콘이 흐려지거나 깨지지 않습니다. 모든 해상도에서 또렷하게 나타나며 색상도 쉽게 변경할 수 있습니다.
- react-native-vector-icons 라이브러리는 오픈 소스로 공개된 벡터 아이콘을 리액트 네이티브 프로젝트에서 간편하게 컴포넌트처럼 사용할 수 있게 해줍니다.
- 이 라이브러리를 설치해봅시다. 다음 명령어를 입력하세요.

```
yarn add react-native-vector-icons
```

- 그리고 iOS와 안드로이드 프로젝트 둘 다 추가해줘야 하는 사항이 있습니다.

### iOS에 react-native-vector-icons 적용하기

- ios 디렉터리로 이동해 pod install 명령어를 입력하세요.

```
cs ios
pos install
```

- ios/TodoApp/Info.plist 파일을 열고 스크롤을 아래로 쭉 내려서 맨 마지막의
  </dict>를 찾은 뒤, 다음과 같이 UIAppFonts 속성을 추가

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

- plist 파일은 iOS 앱의 프로퍼티 리스트(property list) 파일로 앱의 이름, 아이콘, 버전 등 앱에서 필요한 설정값을 지니고 있습니다. 여기에 추가할 폰트로 어떤 것이 있는지는 node_modules/react-native-vector-icons/Fonts 디렉터리를 열어보거나 다음 링크를 보면 됩니다.
- https://github.com/oblador/react-native-vector-icons/tree/master/Fonts
- 다양한 오픈 소스 아이콘이 들어있는 폰트 파일이 있는데요. 파일을 모두 추가할 필요는 없고, 사용하고 싶은 아이콘 종류만 넣으면 됩니다. 우리는 구글에서 디자인한 MaterialIcons만 사용할 것이므로 나머지는 모두 생략하겠습니다.
- 다 설치했으면 yarn ios 명령어를 다시 한번 입력해주세요.

### 안드로이드에 react-native-vector-icons 적용하기

- 안드로이드에서는 android/app/build.gradle 파일을 열고 맨 아래로 스크롤을 내린 후 다음 텍스트를 추가하세요.

> android/app/build.gradle

```groovy
...

apply from: file("../../node_modules/react-native-vector-icons/fonts.gradle")
```
- build.gradle 파일은 안드로이드의 범용 빌드 도구인 Gradle에서 사용하는 파일로 프로젝트의 의존성, 플러그인 및 빌드에 필요한 설정에 대한 정보를 지니고 있습니다.
- 설치가 끝나면 yarn android 명령어를 다시 한번 입력해주세요.

### 삭제 아이콘 보여주기

- 삭제 아이콘을 보여줍시다. 다음 페이지에 들어가면 react-native-vector-icons에 있는 모든 아이콘을 검색할 수 있습니다.
-  https://oblador.github.io/react-native-vector-icons
- 이 페이지에서 delete를 검색하면 MaterialIcons 섹션에 delete 아이콘을 확인할 수 있습니다.

![image8](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/8.png)
> react-native-vector-icons에서 delete 검색 

- react-native-vector-icons의 아이콘은 다음과 같이 사용할 수 있습니다.

```jsx
import icon from 'react-native-vector-icons/MaterialIcons';
const deleteIcon = <Icon name="delete" size={32} color="red" />;
```

- import하는 부분의 'react-native-vector-icons/MaterialIcons'에서 MaterialIcons 대신 다른 폰트를 사용할 수도 있습니다. 
- Icon 컴포넌트의 name 부분에 검색해서 확인한 아이콘의 이름을 넣으면 됩니다. size 및 color Props를 사용해 아이콘의 크기 및 색상을 변경할 수도 있습니다.

> components/TodoItem.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, Image, TouchableOpacity} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

function TodoItem({id, text, done, onToggle}) {
  return (
    <View style={styles.item}>
      <TouchableOpacity onPress={() => onToggle(id)}>
         <View style={[styles.circle, done && styles.filled]}>
          {done && (
            <Image 
              source={require('../assets/icons/check_white/check_white.png')}
            />
          )}
         </View>
      </TouchableOpacity>
      <Text style={[styles.text, done && styles.lineThrough]}>{text}</Text>
      {done ? (
        <Icon name="delete" size={32} color="red" />
      ) : (
        <View style={styles.removePlaceholder} />
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  
  ...
  
  removePlaceholder: {
    width: 32, 
    height: 32,
  }
});

export default TodoItem;
```

- 이 코드는 삼항연산자를 사용해 done 값이 true일 때는 아이콘을 보여주고, 그렇지 않을 때는 removePlaceholder라는 스타일을 가진 View를 보여주도록 했습니다. 여기서 removePlaceholder 스타일을 사용한 이유는 아이콘이 보이지 않을 때도 삭제 아이콘이 보일 영역을 미리 차지해 두기 위함입니다.
- 만약 이 작업을 하지 않으면 항목의 내용이 아주 긴 경우, 토글할 때마다 텍스트가 보이는 영역이 달라질 것입니다.

![image9](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/9.png)
> 삭제 아이콘이 나타난 완료 항목 

### 항목 삭제 함수 만들기

- 이제 App 컴포넌트에서 항목을 삭제하는 함수 onRemove를 선언해 이를 TodoList 컴포넌트의 Props로 설정해줍시다. 이 함수는 onToggle처럼 항목의 id 값을 파라미터로 받아옵니다.

> App.js

```jsx
...

function App() {
  ...
  
  const onRemove = id => {
    const nextTodos = todos.filter(todo => todo.id !== id);
    setTodos(nextTodos);
  };
  
  return (
    <SafeAreaProvider>
      <SafeAreaView edges={['bottom']} style={styles.block}>
        <KeyboardAvoidingView
          behavior={Platform.select({ios: 'padding'})}
          style={styles.avoid}>
          <DateHead date={today} />
          {todos.length === 0 ? (
            <Empty />
          ) : (
            <TodoList todos={todos} onToggle={onToggle} onRemove={onRemove} />
          )}
          <AddTodo onInsert={onInsert} />
        </KeyboardAvoidingView>
      </SafeAreaView>
    </SafeAreaProvider>
  );
}

...

```

- 다음으로 이전에 onToggle을 다룬 것처럼 onRemove 함수도 TodoItem에 전달해줍시다. TodoList 컴포넌트를 다음과 같이 수정하세요.

> components/TodoList.js

```jsx
import React from 'react';
import {FlatList, View, StyleSheet} from 'react-native';
import TodoItem from './TodoItem';

function TodoList({todos, onToggle, onRemove}) {
  return (
    <FlatList
      ItemSeparatorComponent={() => <View style={styles.separator} />}
      style={styles.list}
      data={todos}
      renderItem={({item}) => (
        <TodoItem
          id={item.id}
          text={item.text}
          done={item.done}
          onToggle={onToggle}
          onRemove={onRemove}
        />
      )}
      keyExtractor={item => item.id.toString()}
    />
  );
}

...

```

- 삭제 아이콘을 TouchableOpacity 컴포넌트로 감싸고 onPress Props를 설정해주세요.

> components/TodoItem.js

```jsx
import React from 'react';
import {View, Text, StyleSheet, Image, TouchableOpacity} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

function TodoItem({id, text, done, onToggle, onRemove}) {
  return (
    <View style={styles.item}>
      <TouchableOpacity onPress={() => onToggle(id)}>
        <View style={[styles.circle, done && style.filled]}>
          {done && (
            <Image 
              source={require('../assets/icons/check_white/check_white.png')}
            />
          )}
        </View>
      </TouchableOpacity>
      <Text style={[styles.text, done && styles.lineThrough]}>{text}</Text>
      {done ? (
        <TouchableOpacity onPress={() => onRemove(id)}>
          <Icon name="delete" size={32} color="red" />
        </TouchableOpacity>
      ) : (
        <View style={styles.removePlaceholder} />
      )}
    </View>
  );  
}

...

```

### 항목을 삭제하기 전에 한번 물어보기

- 이번에는 항목을 삭제하기 전에 사용자에게 정말 삭제할 건지 물어보는 알림창을 띄워보겠습니다. 리액트 네이티브에 내장되어 있는 Alert라는 API를 통해 구현할 수 있습니다.

> components/TodoItem.js

```jsx
import React from 'react';
import {
  View,
  Text,
  StyleSheet,
  Image,
  TouchableOpacity,
  Alert,
} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

function TodoItem({id, text, done, onToggle, onRemove}) {
  const remove = () => {
    Alert.alert(
      '삭제',
      '정말로 삭제하시겠어요?',
      [
        {text: '취소', onPress: () => {}, style: 'cancel'},
        {
          text: '삭제',
          onPress: () => {
            onRemove(id);
          },
          style: 'destructive',
        },
      ],
      {
        cancelable: true,
        onDismiss: () => {},
      },
    );
  };
  
  return (
    <View style={styles.item}>
      <TouchableOpacity onPress={() => onToggle(id)}>
        <View style={[styles.circle, done && style.filled]}>
          {done && (
            <Image 
              source={require('../assets/icons/check_white/check_white.png')}
            />
          )}
        </View>
      </TouchableOpacity>
      <Text style={[styles.text, done && styles.lineThrough]}>{text}</Text>
      {done ? (
        <TouchableOpacity onPress={remove}>
          <Icon name="delete" size={32} color="red" />
        </TouchableOpacity>
      ) : (
        <View style={styles.removePlaceholder} />
      )}
    </View>
  );  
}

...

```

- Alert.alert 함수의 파라미터는 제목, 내용, 버튼 배열, 옵션 객체 순서입니다. 버튼 배열에 넣는 버튼 객체에는 text 값을 통해 버튼의 이름을 지정할 수 있고, onPress를 통해 버튼이 눌렸을 때 호출할 함수를 설정할 수 있습니다.
- style은 cancel, default, destructive 값을 설정할 수 있는데 iOS에서만 작동합니
  - **cancel**: 취소를 의미하며 폰트가 두껍게 나타납니다.
  - **default**: 기본을 의미하며 기본 버튼(파란색 텍스트)이 나타납니다.
  - **destructive**: ‘파괴적’인 것을 의미하며 지금과 같이 삭제하는 상황에 적합한 스타일입니다.
- 참고로 안드로이드는 버튼에 스타일이 적용되지 않습니다. 만약 버튼 스타일을 변경하고 싶다면 Alert처럼 보이는 컴포넌트를 직접 제작해야 합니다.
- 4번째 파라미터로 넣는 옵션 객체에는 cancelable 값을 통해 안드로이드에서 Alert 박스 바깥 영역을 터치하거나 Back 버튼을 눌렀을 때 Alert가 닫히도록 설정할 수 있습니다. onDismiss는 Alert가 닫힐 때 호출되는 함수입니다.

![image10](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/10.png)
> iOS의 Alert

![image11](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/04%20%ED%95%A0%EC%9D%BC%20%EB%AA%A9%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/11.png)
> 안드로이드의 Alert

---
# AsyncStorage로 앱이 꺼져도 데이터 유지하기 

- AsyncStorage는 리액트 네이티브에서 사용할 수 있는 key-value 형식의 저장소입니다.
- iOS에서는 네이티브 코드로 구현되어 있으며, 안드로이드에서는 네이티브 코드와 SQLite를 기반으로 구현되어 있습니다.
- AsyncStorage는 브라우저에서 사용하는 localStorage와도 꽤 비슷합니다. 값을 저장할 때는 문자열 타입으로 저장해야 하며, getItem, setItem, removeItem, clear 등 localStorage에서 사용하는 메서드와 같은 이름을 가진 메서드들도 존재합니다. 
- localStorage와의 큰 차이점이라면 AsyncStorage는 비동기적으로 작동한다는 것입니다. 값을 조회하거나 설정할 때 Promise를 반환합니다.


## AsyncStorage 설치하기

- 예전에는 AsyncStorage가 리액트 네이티브 프로젝트에 자체 내장되어 있어서 별도로 설치할 필요가 없었지만, 해당 기능을 커뮤니티에서 유지보수하면서 라이브러리로 분리됐습니다. 따라서 사용하려면 따로 설치해줘야 합니다.
- 다음 명령어를 사용해 이 라이브러리를 설치하세요.

```
yarn add @react-native-community/async-storage
```

- iOS에서는 다시 pod install해줘야 합니다.

```
cd ios
pos install
```

- 설치 후에는 yarn ios, yarn android 명령어를 실행해주세요.

## AsyncStorage의 기본 사용법

- AsyncStorage를 프로젝트에 적용해주겠습니다. 우선 AsyncStorage의 기본 사용법을 알아봅시다.

### 불러오기

- AsyncStorage를 사용하고 싶은 컴포넌트에 다음 코드를 사용해 불러옵니다.

```jsx
import AsyncStorage from '@react-native-community/async-storage';
```

### 저장하기

- 특정 값을 저장할 때는 setItem 메서드를 사용합니다.

```javascript
const save = async () => {
  try {
    await AsyncStorage.setItem('key', 'value');
  } catch (e) {
    // 오류 예외 처리
  }
}
```

- 값을 저장할 때는 문자열 타입이어야 합니다. 만약 객체 및 배열 타입을 저장하려면 다음과 같이 JSON.stringify 함수를 사용해야 합니다.

```javascript
await AsyncStorage.setItem('todos', JSON.stringify(todos));
```

### 불러오기

- 특정 값을 불러올 때는 getItem 메서드를 사용합니다.

```javascript
const load = async () => {
  try {
    const value = await AsyncStorage.getItem('key');
    // value를 사용하는 코드 
  } catch (e) {
    // 오류 예외 처리
  }
};
```

- 만약 객체 및 배열을 불러오려면 JSON.parse 함수를 사용해 문자열을 JSON으로 변환해야 합니다.

```javascript
const rawTodos = await AsyncStorage.getItem('todos');
const todos = JSON.parse(rawTodos);
```

### 초기화하기

- AsyncStorage에 있는 모든 값을 초기화하고 싶다면 다음과 같이 clear 메서드를 사용합니다.

```jsx
const clearAll = async () => {
  try {
    await AsyncStorage.clear();
  } catch (e) {
    // 오류 예외 처리
  }
};
```

- 더 많은 API에 관한 정보는 다음 링크를 참고해주세요.
- https://bit.ly/async-storage-docs

## AsyncStorage 적용하기

- 이제 할일 목록을 AsyncStorage에 저장해 앱을 재시작해도 데이터가 유지되도록 만들어줍시다. 가장 기본적인 방법은 onInsert, onToggle, onRemove 함수에서 setTodos를 호출한 뒤 setItem 메서드도 호출해 데이터를 저장하는 방법입니다.

### useEffect 사용하기

- 이를 수행하기 위해 각 함수를 수정하는 것보다 더 좋은 방법이 있습니다. 바로 useEffect라는 Hook 함수를 사용하는 것입니다. 이 Hook 함수를 사용하면 컴포넌트에서 특정 상태가 바뀔 때마다 원하는 코드를 실행할 수 있습니다. 또한, 컴포넌트가 마운트(가장 처음 화면에 나타남)되거나 언마운트(화면에서 컴포넌트가 사라짐)될 때 원하는 코드를 실행할 수도 있습니다.
- 우선 특정 상태가 바뀔 때마다 특정 함수를 호출하는 예시를 알아보겠습니다. App 컴포넌트에서 다음과 같이 useEffect를 불러오세요.

> App.js - 상단

```jsx
import React, { useState, useEffect} from 'react';
```

- 다음으로 App 컴포넌트의 useState 하단에 다음과 같이 useEffect를 사용해보세요.

> App.js - App 컴포넌트

```jsx
function App() {
  const today = new Date();

  const [todos, setTodos] = useState([
    {id: 1, text: '작업환경 설정', done: true},
    {id: 2, text: '리액트 네이티브 기초 공부', done: false},
    {id: 3, text: '투두리스트 만들어보기', done: false},
  ]);

  useEffect(() => {
    console.log(todos);
  }, [todos]);

  ...
```

- useEffect의 첫 번째 인자에는 주시하고 싶은 값이 바뀌었을 때 호출하고 싶은 함수를 넣습니다. 두 번째 인자에는 주시하고 싶은 값을 배열 안에 넣습니다.
- useEffect에 등록한 함수는 두 번째 인자의 배열 안에 넣은 값이 바뀔 때도 호출되지만, 컴포넌트가 가장 처음 렌더링됐을 때도 호출됩니다.

### todos 저장하기

- useEffect로 등록한 함수 내부에서 AsyncStorage의 setItem 메서드를 사용합시다.

> App.js

```jsx
import React, {useState, useEffect} from 'react';
import {StyleSheet, KeyboardAvoidingView, Platform} from 'react-native';
import {SafeAreaProvider, SafeAreaView} from 'react-native-safe-area-context';
import AsyncStorage from '@react-native-community/async-storage';

import DateHead from './components/DateHead';
import AddTodo from './components/AddTodo';
import Empty from './components/Empty';
import TodoList from './components/TodoList';

function App() {
  const today = new Date();

  const [todos, setTodos] = useState([
    {id: 1, text: '작업환경 설정', done: true},
    {id: 2, text: '리액트 네이티브 기초 공부', done: false},
    {id: 3, text: '투두리스트 만들어보기', done: false},
  ]);

  // 저장
  useEffect(() => {
    async function save() {
      try {
        await AsyncStorage.setItem('todos', JSON.stringify(todos));
      } catch (e) {
        console.log('Failed to save todos');
      }
    }
  }, [todos]);

  ...
  
```

- 코드를 보면 useEffect에 등록한 함수를 async 함수로 만들지 않고 내부에 async 함수를 따로 선언한 다음, 이를 호출해줬습니다.
- useEffect에 등록한 함수는 async 키워드를 붙이면 안 됩니다. useEffect에는 정리(cleanup)하는 기능이 있는데, 함수에서 Promise를 반환하면 이 기능과 충돌이 나기 때문입니다.
- 정리 기능은 컴포넌트가 언마운트되거나 컴포넌트가 업데이트되기 전에 특정 코드를 실행할 수 있는 기능으로, useEffect에서 함수 타입의 값을 반환하면 이 기능을 사용할 수 있습니다.


```jsx
useEffect(() => {
  // 업데이트된 다음에 출력
  console.log(todos);
  return () => {
    // 업데이트되기 전에 출력
    // 여기서 todos는 업데이트되기 전의 값을 가리킵니다.
    console.log(todos);
  }
}, [todos])
```

- 추가로 useEffect를 사용해 컴포넌트 마운트 또는 언마운트 시 특정 작업을 하고 싶다면 useEffect의 두 번째 파라미터에 비어있는 배열을 사용하면 됩니다.

```jsx
useEffect(() => {
  console.log('컴포넌트가 마운트될 때 출력됨');
  return () => {
    console.log('컴포넌트가 언마운트될 때 출력됨');
  }
}, [])
```

### todos 불러오기

- 앱을 가동할 때 AsyncStorage에 저장한 todos를 불러와서 상태를 업데이트해주겠습니다. App이 마운트될 때 useEffect와 AsyncStorage의 getItem 메서드를 사용하면 됩니다.

> App.js - App 컴포넌트

```jsx
function App() {
  const today = new Date();

  const [todos, setTodos] = useState([
    {id: 1, text: '작업환경 설정', done: true},
    {id: 2, text: '리액트 네이티브 기초 공부', done: false},
    {id: 3, text: '투두리스트 만들어보기', done: false},
  ]);

  // 불러오기
  useEffect(() => {
    async function load() {
      try {
        const rawTodos = await AsyncStorage.getItem('todos');
        const savedTodos = JSON.parse(rawTodos);
        setTOdos(savedTodos);
      } catch (e) {
        console.log('Failed to load todos');
      }
    }
    load();
  }, []);
  
  // 저장 
  useEffect(() => {
    async function save() {
      try {
        await AsyncStorage.setItem('todos', JSON.stringify(todos));
      } catch (e) {
        console.log('Failed to save todos');
      }
    }
    
    save();
  }, [todos]);  
...

```

- useEffect의 두 번째 배열이 비었으면, 컴포넌트가 마운트될 때 딱 한 번만 함수가 호출됩니다.
- 데이터를 불러와서 상태를 업데이트하는 코드를 작성할 때는 꼭 기존의 todos를 저장하는 useEffect보다 상단 위치에 코드를 작성해주세요. 저장하는 useEffect가 먼저 있으면 불러오기 기능이 제대로 작동하지 않습니다. useEffect는 등록된 순서대로 작동하는데, 저장하는 useEffect가 먼저 호출되면 todos의 초깃값을 저장해버린 다음에 불러오기가 진행되므로 초깃값만 불러오기 때문입니다.
- 코드를 저장하고 할일 목록에 변화를 준 다음, 리로드해 데이터가 잘 보존됐는지 확인해보세요.

### AsyncStorage를 사용하는 코드 추상화시키기

- 리액트 네이티브 프로젝트에서 AsyncStorage를 사용할 때는 방금 사용한 것처럼 직접 사용하지 않고 코드를 한번 추상화시켜서 사용할 것이 권장합니다. 그 이유는 추후 key 값이 바뀔 수도 있고, AsyncStorage가 아닌 다른 방식을 통해 데이터를 저장할 수도 있는데 이러한 상황에서 유지보수하기가 더욱 쉬워지기 때문입니다.
- 프로젝트 최상위 디렉터리에 storages 디렉터리를 만들고, 그 안에 todosStorages.js 파일을 생성하세요.

> storages/todosStorage.js

```jsx
import AsyncStorage from '@react-native-community/async-storage';

const key = 'todos';

const todosStorage = {
  async get() {
    try {
      const rawTodos = await AsyncStorage.getItem(key);
      
      if (!rawTodos) {
        // 저장된 데이터가 없으면 사용하지 않음
        throw new Error('No saved todos');
      }
      
      const savedTodos = JSON.parse(rawTodos);
      return savedTodos;
    } catch (e) {
      throw new Error('No saved todos');
    }
  },
  async set(data) {
    try {
      await AsyncStorage.setItem(key, JSON.stringify(data));
    } catch (e) {
      throw new Error('Failed to save todos');
    }
  }
};

export default todosStorage;
```

- 이 코드에서는 todosStorage라는 객체를 만들어 해당 객체 내부에 get과 set 메서드를 만들어줬습니다. 추가로 데이터를 불러오거나 저장할 때 사용하는 key 값은 따로 상단에서 상수로 선언해 추후 손쉽게 바꿀 수 있도록 준비해줬습니다.
- 코드를 작성한 뒤, todosStorage를 App 컴포넌트에서 불러와 사용하세요.

> App.js

```jsx
...

import todoStorage from './storages/todoStorage';

function App() {
  const today = new Date();

  const [todos, setTodos] = useState([
    {id: 1, text: '작업환경 설정', done: true},
    {id: 2, text: '리액트 네이티브 기초 공부', done: false},
    {id: 3, text: '투두리스트 만들어보기', done: false},
  ]);

  useEffect(() => {
    todosStorage
      .get()
      .then(setTodos)
      .catch(console.error);
  }, []);

  useEffect(() => {
    todosStorage.set(todos).catch(console.error);
}, [todos]);
```

- App 컴포넌트에서는 더 이상 AsyncStorage를 사용하지 않으므로 import 코드를 지워주세요. 그리고 todosStorage를 불러온 다음 useEffect에서 get과 set 메서드를 사용할 때 이번에는 async 함수를 따로 선언하지 않고 Promise를 그대로 사용했습니다. 데이터를 불러오는 부분에서 .then(setTodos)라고 코드를 작성했는데, 데이터를 불러오고 나서 그 결과물을 setTodos의 함수 인자로 넣어 호출하겠다는 의미입니다.

### 안드로이드에서 AsyncStorage 최대 용량 설정하기

- 안드로이드에서 AsyncStorage의 최대 크기는 기본적으로 6MB로 설정되어 있습니다. AsyncStorage에 너무 많은 데이터를 넣는 것을 방지하기 위해서라고 합니다. 이 용량을 초과하면 오류가 발생합니다. 
- 최대 용량을 늘리려면 android/gradle.properties 파일에 다음 코드를 추가하면 됩니다.

> android/gradle.properties

```properties
...
AsyncStorage_db_size_in_MB=10
```

- 이 코드는 최대 용량을 10MB로 설정합니다. iOS는 최대 용량이 따로 지정되어 있지 않습니다.


### AsyncStorage의 제한
- AsyncStorage는 설정이 매우 간편하고 사용법도 쉽습니다.
- 하지만 단점은 AsyncStorage에서 다루는 데이터의 규모가 커지면 성능이 떨어집니다. 문자열 타입으로만 저장할 수 있기 때문에 데이터가 많아질수록 속도가 느려집니다. 물론 캐싱 시스템, 스로틀링(throttling), 페이지네이션 구현 등으로 성능을 최적화할 수도 있긴 합니다. 하지만 최적화가 코드 몇 줄 추가하는 것처럼 간단하지는 않을 것입니다. 그리고 검색 또는 정렬 기능이 지원되지 않습니다.
- 따라서 AsyncStorage는 비교적 소규모 데이터를 다룰 때 사용하는 것이 좋습니다. 데이터의 규모가 커졌을 때 사용할 수 있는 대안으로는 realm와 react-native-sqlite-storage가 있습니다(안드로이드의 AsyncStorage는 이미 SQLite를 사용하긴 하지만, react-native-sqlite-storage를 사용하면 인덱싱 기능을 지원받을 수 있고, 더욱 다양한 방식으로 데이터를 저장하고 조회할 수 있습니다).