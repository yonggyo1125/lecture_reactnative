> 다이어리 앱의 나머지 기능을 모두 완성해보겠습니다. 앞으로 구현할 기능은 다음과 같습니다.
- 수정하기
- 삭제하기 
- 검색하기 
- 달력보여주기

# 작성한 글을 WriteScreen으로 열기

- 수정하기와 삭제하기 기능을 구현하기에 앞서 사용자가 FeedList에서 항목을 눌렀을 때 WriteScreen을 띄워서 기존에 작성한 내용을 보여주는 기능을 구현

## FeedListItem 수정하기

- FeedListItem 컴포넌트에서 Pressable에 onPress Props를 설정해 컴포넌트를 눌렀을 때 현재 Props로 받아온 log 객체를 WriteScreen의 파라미터로 넣어서 화면을 열면 됩니다.

> components/FeedListItem.js

```jsx
import React from 'react';
import {Platform, Pressable, StyleSheet, Text} from 'react-native';
import {formatDistanceToNow, format} from 'date-fns';
import {ko} from 'date-fns/locale';
import {useNavigation} from '@react-navigation/native';

...

function FeedListItem({log}) {
    const {title, body, date} = log; // 사용하기 편하게 객체 구조분해 할당
    const navigation = useNavigation();
    const onPress = () => {
        navigation.navigate('Write', {
            log,
        });
    };
    
    return (
        <Pressable
            style={({pressed}) => [
                styles.block,
                Platform.OS === 'ios' && pressed && {backgroundColor: '#efefef'},
            ]}
            android_ripple={{color: '#ededed'}}
            onPress={onPress}>
        
        ...
        
    );
}


```

## WriteScreen에서 log 파라미터 인식하기

- WriteScreen에서 log 파라미터를 인식해 파라미터가 주어졌을 때 제목과 내용의 기본값을 지정해주겠습니다.

> screens/WriteScreen.js

```jsx
...

function WriteScreen({route}) {
    const log = route.params?.log;
    
    const [title, setTitle] = useState(log?.title ?? '');
    const [body, setBody] = useState(log?.body ?? '');
    
    ...
}

...
```

- 여기서 ?. 문법을 사용했는데, 이를 옵셔널 체이닝(optional chaining) 문법이라고 부릅니다. 이 문법을 사용하면 null이거나 undefined일 수 있는 객체의 프로퍼티를 에러 없이 접근할 수 있습니다. 만약 이 문법을 사용하지 않으면 다음과 같이 구현해야 하죠.

```javascript
const log = route.params ? route.params.log : undefined;

const [title, setTitle] = useState(log ? log.title : '');
const [body, setBody] = useState(log ? log.body : '');
```

- 상태의 기본값을 설정하는 부분에서는 OR 연산자를 사용했습니다. log?.body ?? ''의 의미는 log?.body가 유효한 값이라면 해당 값을 사용하고, 그렇지 않으면 공백 문자열을 사용하라는 의미입니다.
- 코드를 작성한 뒤 FeedList에서 항목을 선택해 열어보세요. 항목의 내용이 WriteScreen의 텍스트 상태 초깃값으로 설정되어 있는지 확인

# 수정 기능 구현하기

- 작성한 항목을 WriteScreen으로 여는 기능을 구현했으니, 이제 수정 기능을 구현해봅시다. WriteScreen의 onSave 함수에서 log 라우트 파라미터가 유효하다면 수정하는 함수를 호출하고, 그렇지 않다면 생성하는 함수를 호출

## LogContext에 onModify 함수 구현하기
- LogContext에서 항목을 수정할 수 있는 onModify라는 함수 구현

> contexts/LogContext.js

```jsx
import React from 'react';
import {createContext, useState} from 'react';
import {v4 as uuidv4} from 'uuid';

const LogContext = createContext();

export function LogContextProvider({children}) {
    ...
    
    const onModify = (modified) => {
        // logs 배열을 순회해 id가 일치하면 log를 교체하고 그렇지 않으면 유지
        const nextLogs = logs.map((log) => log.id === modified.id ? modified : log);
        setLogs(nextLogs);
    };
    
    return (
        <LogContext.Provider value={{logs, onCreate, onModify}}>
            {children}
        </LogContext.Provider>
    );
}

export default LogContext;
```

- logs 배열의 불변성을 지키면서 배열을 업데이트하기 위해 배열의 내장 함수 map으로 우리가 바꾸고 싶은 항목만 교체된 새로운 배열을 생성해 상태를 업데이트

## WriteScreen에서 onModify 함수 사용하기

> screens/WriteScreen.js

```jsx
...

function WriteScreen({route}) {
    const log = route.params?.log;
    
    const [title, setTitle] = useState(log?.title ?? '');
    const [body, setBody] = useState(log?.body ?? '');
    const navigation = useNavigation();
    
    const {onCreate, onModify} = useContext(LogContext);
    const onSave = () => {
        if (log) {
            onModify({
                id: log.id,
                date: log.date,
                title,
                body,
            });
        } else {
            onCreate({
                title,
                body,
                // 날짜를 문자열로 변환
                date: new Date().toISOString(),
            });
        }
        navigation.pop();
    };
    
    ...
}

...

```

# 삭제 기능 구현하기

- LogContext에서 삭제 기능을 위한 함수를 구현한 다음 WriteScreen에서 사용

## LogContext에 onRemove 함수 구현하기

> contexts/LogContext.js

```jsx
...

export function LogContextProvider({children}) {
    ...
    
    const onModify = (modified) => {
        // logs 배열을 순회해 id가 일치하면 log를 교체하고 그렇지 않으면 유지
        const nextLogs = logs.map((log) => log.id === modified.id ? modified : log);
        setLogs(nextLogs);
    };
    const onRemove = (id) => {
        const nextLogs = logs.filter((log) => log.id !== id);
        setLogs(nextLogs);
    };
    
    return (
        <LogContext.Provider value={{logs, onCreate, onModify, onRemove}}>
            {children}
        </LogContext.Provider>
    );
}

export default LogContext;
```

- 배열의 내장 함수 filter로 특정 id를 제외한 항목들로만 구성된 새로운 배열을 만들어서 상태를 업데이트해줬습니다. 이렇게 한 이유는 불변성을 유지하면서 배열을 업데이트하기 위해서입니다.

## WriteScreen에서 onRemove 함수 사용하기

- 우측 상단의 삭제 버튼을 눌렀을 때 onRemove 함수를 호출하도록 구현하겠습니다. 
- 삭제 버튼을 눌렀을 때 바로 삭제하지 않고, Alert.alert를 사용해 사용자에게 정말 삭제할 것인지 한 번 더 물어보는 과정을 거치겠습니다.

> screens/WriteScreen.js

```jsx
import {useNavigation} from '@react-navigation/native';
import React, {useContext, useState} from 'react';
import {Alert, KeyboardAvoidingView, Platform, StyleSheet} from 'react-native';
import {SafeAreaView} from 'react-native-safe-area-context';
import WriteEditor from '../components/WriteEditor';
import WriteHeader from '../components/WriteHeader';
import LogContext from '../comtexts/LogContext';

function WriteScreen({route}) {
    ...
    const {onCreate, onModify, onRemove} = useContext(LogContext);
    ...
    
    const onAskRemove = () => {
        Alert.alert(
            '삭제',
            '정말로 삭제하시겠어요?',
            [
                {text: '취소', style: 'cancel'},
                {
                    text: '삭제',
                    onPress: () => {
                        onRemove(log?.id);
                        navigation.pop();
                    },
                    style: 'destructive',
                },
            ],
            {
                cancelable: true,
            },
        );
    };
    
    return (
        <SafeAreaView style={styles.block}>
            <KeyboardAvoidingView
                style={styles.avoidingView}
                behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
                <WriteHeader 
                    onSave={onSave}
                    onAskRemove={onAskRemove}
                    isEditing={!!log}
                />
                <WriteEditor 
                    title={title}
                    body={body}
                    onChangeTitle={setTitle}
                    onChangeBody={setBody}
                />
            </KeyboardAvoidingView>
        </SafeAreaView>
    );
};

...

```

- onAskRemove라는 함수를 만들어서 WriteHeader에 Props로 전달해줬습니다.
- 이전에 Alert.alert를 사용할 때는 취소 버튼의 onPress와 4번째 인자인 옵션 객체에 Alert.alert를 닫을 때 호출하는 함수인 onDismiss에 () => {}와 같은 형태로 아무 작업도 하지 않는 함수를 넣어줬는데, 만약 아무 작업도 하지 않는다면 아예 생략해도 무방합니다.
- 추가로 isEditing이라는 값도 전달해줬습니다. WriteHeader에서는 이 값을 확인해 삭제 버튼을 보여줄지 말지 결정하면 됩니다.
- 여기서 !!log라는 코드를 사용했지요? 이는 log가 유효한 값이면 true, 그렇지 않으면 false 값을 전달하겠다는 의미입니다. NOT 연산자 !는 기본적으로 true를 false로, 또는 false를 true로 바꿉니다. 하지만 Boolean이 아닌 타입에 ! 연산자를 쓴다면 어떻게 될까요?
    - !null -> true 
    - !{} -> false
    - !'' -> true
    - ![] -> false
- 이처럼 NOT 연산자를 두 번(!!) 사용할 경우 log 값이 유효한 객체라면 값이 true가 되고, 값이 null이나 undefined라면 false가 됩니다.

## WriteHeader 수정하기

- WriteHeader에서 isEditing Props가 true일 때만 삭제 버튼을 보여주고, 이 버튼을 눌렀을 때 onAskRemove를 호출하도록 컴포넌트를 수정

> components/WriteHeader.js

```jsx
...

function WriteHeader({onSave, onAskRemove, isEditing}) {
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
                {isEditing && (
                    <TransparentCircleButton
                        name="delete-forever"
                        color="#ef5350"
                        hasMarginRight
                        onPress={onAskRemove}
                    />
                )}
                <TransparentCircleButton 
                    name="check"
                    color="#009688"
                    onPress={onSave}
                />
            </View>
        </View>
    );
}

...

```

![image1](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/1.png)
> 항목 삭제 Alert


# 검색 기능 구현하기

- 검색을 하려면 화면의 헤더 부분에 텍스트를 입력할 수 있어야 하므로, 헤더를 커스터마이징해야 합니다.

## SearchHeader 컴포넌트 만들기

- MainTab 컴포넌트에서 현재 화면에 따라 다른 헤더 제목을 설정하기 위해 getHeaderTitle이라는 함수를 만들었습니다.
- 네이티브 스택 내비게이터의 headerTitle 옵션에는 문자열이 아닌 JSX를 넣어줄 수도 있습니다. 우선 SearchHeader라는 컴포넌트를 만듭니다.

> components/SearchHeader.js

```jsx
import React from 'react';
import {StyleSheet, Text} from 'react-native';

function SearchHeader() {
  return <Text style={styles.block}>Hello</Text>;
}

const styles = StyleSheet.create({
  block: {
    color: 'white',
    backgroundColor: 'blue',
  }
});

export default SearchHeader;
```

- 임시로 배경이 파란색인 Text를 보여주도록 했습니다. 이제 이 컴포넌트를 검색 화면일 때 headerTitle로 지정해보겠습니다. MainTab을 열어서 검색 화면의 설정을 같이 수정하세요.

> screens/MainTab.js

```jsx
...

import SearchHeader from '../components/SearchHeader'

... 

    <Tab.Screen 
      name="Search"
      component={SearchScreen}
      options=[{
        title: '검색',
        tabBarIcon: ({color, size}) => (
          <Icon name="search" size={size} color={color} />
        ),
        headerTitle: () => <SearchHeader />,
      }]
    />
  </Tab.Navigator>
  );
}

export default MainTab;
```

- 검색 화면을 띄웠을 때 헤더가 다음과 같이 나타날 것입니다

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/2.png)
> 커스텀 헤더

- iOS는 컴포넌트가 중앙에 정렬되고, 안드로이드는 좌측에 정렬됩니다. 타이틀이 헤더의 일부 영역만 사용하지 않고 전체 영역을 사용하도록 만들고 싶습니다. 그렇게 하려면 화면 크기를 가져온 다음에 해당 값을 참고해 dp 단위의 크기를 직접 설정해줘야 합니다.

## 화면 크기 조회하기
- 화면 크기를 dp 단위로 가져오는 방법은 두 가지입니다.
- 첫 번째 방법은 Dimensions.get을 사용하는 것입니다.

```jsx
import {Dimensions} from 'react-native';

const { width, height } = Dimensions.get('window');
```

- Dimensions.get에는 두 가지 문자열을 넣을 수 있습니다. 
  - 'window'를 사용하면 현재 앱에서 사용할 수 있는 영역의 크기를 가져옵니다. 
  - 'screen'을 사용하면 전체 화면의 크기를 가져옵니다.
- iOS에서는 두 크기의 차이가 없지만, 안드로이드에서는 차이가 있습니다. 안드로이드에서 'window'로 크기를 조회했을 때는 상단의 상태 바와 하단의 소프트 메뉴 바 영역을 제외한 크기를 반환합니다.
- 이 방법은 컴포넌트 외부에서도 작동하므로 StyleSheet에서도 사용할 수 있습니다. 화면의 방향을 바꾸거나, 폴더블 디바이스를 사용하면 이 크기가 바뀔 수도 있습니다. 이러한 경우에는 Dimensions.addEventListener와 Dimensions.removeEventListener를 사용해 크기가 바뀌는 이벤트에 대비해야 합니다.

```jsx
import React, {useState, useEffect} from 'react';
import {View, Dimension} from 'react-native';

function MyComponent() {
  const [dimension, setDimension] = useState(Dimensions.get('window'));
  useEffect(() => {
    const eventListener = ({window, screen}) => {
      setDimension(window);
    }
    Dimensions.addEventListener('change', eventListener);
    return () => {
      Dimensions.removeEventListener('change', eventListener);
    }
  }, []);
  
  ... 
  
}
```

- 두 번째 방법은 useWindowDimensions Hook을 사용하는 것입니다. 
- Dimensions.get과 달리 화면 크기가 바뀌는 상황에 우리가 직접 대비할 필요가 없습니다. Hook 형태로 사용하기 때문에 값이 바뀌면 컴포넌트에서 자동으로 반영됩니다.

```jsx
import React from 'react';
import {useWindowDimensions} from 'react-native';

function MyComponent() {
  const {width, height} = useWindowDimensions();
}
```

- 이 방법은 함수 컴포넌트 내부에서만 사용 가능하며, 전체 화면의 크기를 가져오는 기능은 없습니다. 만약 전체 화면의 크기를 가져와야 한다면 Dimensions.get('screen')을 사용해야 합니다.
- 두 번째 방법이 작성해야 할 코드가 더 적어서 간단하므로, 여기서는 이 방법을 사용하겠습니다. SearchHeader 컴포넌트에 useWindowDimensions로 너비를 직접 계산해 설정해봅시다.

> components/SearchHeader.js

```jsx
import React from 'react';
import {StyleSheet, useWindowDimensions, View} from 'react-native';

function SearchHeader() {
  const {width} = useWindowDimensions();
  return <View style={[styles.block, {width: width - 32, height: 24}]} />;
}

const styles = StyleSheet.create({
  block: {
    backgroundColor: 'blue',
  },
});

export default SearchHeader;
```

- Text 대신 View를 사용하고 높이는 24, 너비는 전체 너비에 32를 뺀 값을 설정해줬습니다. 32를 뺀 이유는 양 옆에 여백이 16씩 있기 때문입니다.

![image3](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/3.png)
> SearchHeader 너비 직접 계산

## SearchHeader 컴포넌트 UI 구성하기 
- 이 컴포넌트에서는 검색어를 입력할 TextInput과 검색어를 초기화하는 버튼을 보여줘야 합니다.

> components/SearchHeader.js

```jsx
import React from 'react';
import {
  Pressable, 
  StyleSheet,
  TextInput,
  useWindowDimensions,
  View,
} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';

function SearchHeader() {
  const {width} = useWindowDimensions();
  return (
    <View style={[styles.block, {width: width - 32}]}>
      <TextInput style={styles.input} placeholder="검색어를 입력하세요." autoFocus />
      <Pressable
        style={({pressed}) => [styles.button, pressed && {opacity: 0.5}]}>
        <Icon name="cancel" size={20} color="#9e9e9e" />
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  input: {
    flex: 1,
  },
  button: {
    marginLeft: 8,
  },
});

export default SearchHeader;
```

![image4](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/4.png)
> SearchHeader 컴포넌트 UI 구성하기

- TextInput에 autoFocus라는 Props를 사용해줬는데, 이러면 이 컴포넌트가 화면에 나타날 때 자동으로 포커스가 잡힙니다. 그럼 검색 화면을 열자마자 바로 키보드가 떠서 검색어를 입력할 수 있습니다.
- 컴포넌트의 UI를 다 구성했으니 이제 검색어에 대한 상태를 관리해줘야 합니다. SearchHeader에서 입력한 검색어를 SearchScreen에서 사용할 수 있어야 하는데, 현재 SearchHeader 컴포넌트는 MainTab 쪽에서 사용하고 있기 때문에, 상태를 공유하려면 이번에도 Context를 사용해야 합니다.

## SearchContext 만들기 

- 검색어 상태를 관리하기 위해 SearchContext를 만듭니다.

> contexts/SearchContext.js

```jsx
import React, {createContext, useState} from 'react';
import SearchContext = createContext();

export function SearchContextProvider({children}) {
  const [keyword, onChangeText] = useState('');
  return (
    <SearchContext.Provider value={{keyword, onChangeText}}>
      {children}
    </SearchContext.Provider>
  );
}

export default SearchContext;
```

- 이번 Context는 별도로 만들어야 할 함수가 없습니다. useState로 만든 상태 값인 keyword와 업데이터 함수인 onChangeText를 SearchContext.Provider의 value로 설정하면 됩니다. Context를 다 만든 다음 App 컴포넌트를 열어 SearchContextProvider 컴포넌트를 불러와 사용해주세요.