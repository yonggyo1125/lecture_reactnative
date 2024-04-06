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

---
# 항목 삭제하기 

---
# AsyncStorage로 앱이 꺼져도 데이터 유지하기 
