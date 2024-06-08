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

- 이번 Context는 별도로 만들어야 할 함수가 없습니다. useState로 만든 상태 값인 keyword와 업데이터 함수인 onChangeText를 SearchContext.Provider의 value로 설정하면 됩니다. 
- Context를 다 만든 다음 App 컴포넌트를 열어 SearchContextProvider 컴포넌트를 불러와 사용해주세요.
- SearchContext와 기존에 만든 LogContext는 의존 관계가 아니기 때문에 Provider 컴포넌트의 사용 순서는 중요하지 않습니다.

> App.js

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import RootStack from './screens/RootStack';
import {LogContextProvider} from './contexts/LogContext';
import {SearchContextProvider} from './contexts/SearchContext';

function App() {
  return (
    <NavigationContainer>
      <SearchContextProvider>
        <LogContextProvider>
          <RootStack />
        </LogContextProvider>
      </SearchContextProvider>
    </NavigationContainer>
  );
}

export default App;
```

- SearchContext를 적용한 후 SearchHeader와 SearchScreen에서 useContext로 해당 Context를 사용합니다.
- SearchHeader에는 TextInput에 value와 onChangeText Props를 설정하세요. 그리고 우측의 Pressable을 눌렀을 때 텍스트를 초기화하도록 구현하세요.

> components/SearchHeader.js

```jsx
import React, {useContext} from 'react';
import {
  Pressable,
  StyleSheet,
  TextInput,
  useWindowDimensions,
  View,
} from 'react-native';
import Icon from 'react-native-vector-icons/MaterialIcons';
import SearchContext from '../contexts/SearchContext';

function SearchHeader() {
  const {width} = useWindowDimensions();
  const {keyword, onChangeText} = useContext(SearchContext);
  
  return (
    <View style={[styles.block, {width: width - 32}]}>
      <TextInput 
        style={styles.input}
        placeholder="검색어를 입력하세여"
        value={keyword}
        onChangeText={onChangeText}
        autoFocus
      />
      <Pressable
        style={({pressed}) => [styles.button, pressed && {opacity: 0.5}]}
        onPress={() => onChangeText('')}>
        <Icon name="cancel" size={20} color="#9e9e9e" />
      </Pressable>
    </View>
  );
}

...

```

- SearchScreen에는 임시로 Text를 사용해 검색어를 화면에 보여주도록 설정합시다. SearchContext가 잘 작동하고 있는지 검증하기 위함

> screens/SearchScreen.js

```jsx
import React, {useContext} from 'react';
import {StyleSheet, Text, View} from 'react-native';
import SearchContext from '../contexts/SearchContext';

function SearchScreen({navigation}) {
  const {keyword} = useContext(SearchContext);
  return (
    <View style={styles.block}>
      <Text>{keyword}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {},
});

export default SearchScreen;
```

- 이제 SearchKeyword에 검색어를 입력해보세요. 입력한 검색어가 SearchScreen에도 그대로 잘 나타나는지 확인

![image5](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/5.png)
> SearchContext 사용하기 

## 검색어 필터링 후 FeedList 재사용하기

- 배열의 내장 함수 filter 함수를 통해 구현하면 됩니다. 검색어를 사용해 배열의 데이터를 필터링한 다음, 그 결과물을 FeedList 컴포넌트를 재사용해 화면에 띄워봅시다.
> screens/SearchScreen.js

```jsx
import React, {useContext} from 'react';
import {StyleSheet, View} from 'react-native';
import FeedList from '../components/FeedLists';
import LogContext from '../contexts/LogContext';
import SearchContext from '../contexts/SearchContext';

function SearchScreen({navigation}) {
  const {keyword} = useContext(SearchContext};
  const {logs} = useContext(LogContext);
  
  const filtered = keyword === '' ? [] : logs.filter((log) => [log.title, log.body].some((text) => text.includes(keyword)));
  
  return (
    <View style={styles.block}>
      <FeedList logs={filtered} />
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
  },
});

export default SearchScreen;
```

- 이 코드에서 text.includes라는 문자열 내장 함수를 사용했습니다. 이는 텍스트에 특정 문자열이 존재하는지 확인하는 함수입니다. 만약 존재한다면 true를 반환하고 그렇지 않으면 false를 반환합니다.
- 그리고 \[log.title, log.body\] 배열에서 사용한 some이라는 배열 내장 함수는 배열 원소 중 특정 조건이 true인 원소가 하나라도 있으면 true를 반환하고, 모든 원소가 특정 조건을 만족하지 않을 때 false를 반환합니다.
- 검색어가 있을 때는 필터링을 거치고, 그렇지 않을 때는 빈 배열을 보여주도록 설정했습니다.

![image6](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/6.png)
> 검색 기능 구현 

## EmptySearchResult 만들기
- 이제 검색 기능은 거의 다 구현했습니다. 검색 결과가 없을 때 안내 문구를 보여주는 컴포넌트를 만들고 이 기능을 마무리합시다.
- 검색 결과가 없는 상황은 두 가지입니다. 첫 번째는 사용자가 검색어를 입력하지 않았을 때이고, 두 번째는 검색어와 일치하는 결과물이 없을 때입니다. 이 두 상황에 맞는 문구를 준비해 상황에 따라 알맞은 문구를 보여주는 컴포넌트 EmptySearchResult를 만들어봅시다.

> components/EmptySearchResult.js

```jsx
import React from 'react';
import {View, Text, StyleSheet} from 'react-native';

const messages = {
  NOT_FOUND: '검색 결과가 없습니다.',
  EMPTY_KEYWORD: '검색어를 입력하세요.',
};

function EmptySearchResult({type}) {
  return (
    <View style={styles.block}>
      <Text style={styles.text}>{messages[type]}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  block: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    color: '#9e9e9e',
    fontSize: 16,
  },
});

export default EmptySearchResult;
```

- 이 컴포넌트는 type이라는 Props를 받아옵니다. 이 값이 NOT_FOUND일 때는 검색 결과가 없다는 문구를 띄우고, EMPTY_KEYWORD일 때는 검색어를 입력하라는 문구를 띄웁니다.
- 이제 이 컴포넌트를 SearchScreen에서 사용해줍니다.

> screens/SearchScreen.js

```jsx
import React, {useContext} from 'react';
import {StyleSheet, View} from 'react-native';
import EmptySearchResult from '../components/EmptySearchResult';
import FeedList from '../components/FeedList';
import LogContext from '../contexts/LogContext';
import SearchContext from '../contexts/SearchContext';

function SearchScreen({navigation}) {
  const {keyword} = useContext(SearchContext);
  const {logs} = useContext(LogContext);
  
  const filtered = keyword === '' ? [] : logs.filter((log) => [log.title, log.body].some((text) => text.includes(keyword)));
  
  if (keyword === '') {
    return <EmptySearchResult type="EMPTY_KEYWORD" />;
  }
  
  if (filtered.length === 0) {
    return <EmptySearchResult type="NOT_FOUND" />;
  }
  
  return (
    <View style={styles.block}>
      <FeedList logs={filtered} />
    </View>
  );
} 

const styles = StyleSheet.create({
  block: {
    flex: 1,
  },
});

export default ScreenSearch;
```

![image7](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/7.png)
> EmptySearchResult 만들기


## 달력 기능 구현하기

- 달력 화면을 만드는 과정에서 react-native-calendars라는 라이브러리를 사용합니다. 이 라이브러리를 설치해주세요.

```
yarn add react-native-calendars
```

- 라이브러리를 설치한 다음에는 CalendarView라는 컴포넌트를 만듭니다. 이 컴포넌트에서는 방금 설치한 라이브러리를 사용해 달력을 보여주고, 로그가 작성된 날짜를 달력에 표시하도록 구현하겠습니다.

> components/CalendarView.js

```jsx
import React from 'react';
import {Calendar} from 'react-native-calendars';
import {StyleSheet} from 'react-native';

function CalendarView() {
  return <Calendar style={styles.calendar} />;
}

const styles = StyleSheet.create({
  calendar: {
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
});

export default CalendarView;
```

- CalendarView 컴포넌트를 작성한 다음 CalendarScreen에서 사용해주세요.

> screens/CalendarScreen.js

```jsx
import React from 'react';
import CalendarView from '../components/CalendarView';

function CalendarScreen() {
  return <CalendarView />;
}

export default CalendarScreen;
```
![image8](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/8.png)
> react-native-calendars

## 달력에 표시하기

- 앞으로 사용자가 달력에서 특정 날짜를 선택해 해당 날짜에 작성된 로그를 조회할 수 있는 기능을 구현해볼 것입니다. 이 기능을 구현하기 위해 사용자가 현재 선택한 날짜, 로그를 작성한 날짜에 특별한 표시를 해주겠습니다. 추가로 달력에 사용되는 색상들도 변경해보겠습니다.

> components/CalendarView.js

```jsx
import React from 'react';
import {Calendar} from 'react-native-calendars';
import {StyleSheet} from 'react-native';

function CalendarView() {
  // 현재 연/월 사용하기
  const markedDates = {
    '2021-05-17': {
      selected: true,
    },
    '2021-05-18': {
      makred: true,
    },
    '2021-05-19': {
      marked: true,
    },
  };
  return (
    <Calendar
      style={styles.calendar}
      markedDates={markedDates}
      theme={{
        selectedDayBackgroundColor: '#009688',
        arrowColor: '#009688',
        dotColor: '#009688',
        todayTextColor: '#009688',
      }}
    />
  );
}

const styles = StyleSheet.create({
  calendar: {
     borderBottomWidth: 1,
     borderBottomColor: '#e0e0e0',
  },
});

export default CalendarView;
```

- 달력에 표시하기 위해 markedDates라는 객체를 사용합니다. 이 객체에 들어가는 키 값은 날짜의 ‘yyyy-MM-dd’ 형태입니다. 표시를 원하는 각 날짜에 객체를 만들고, 거기에 marked 값을 true로 설정하면 날짜에 점이 나타납니다. selected 값을 true로 설정하면 이 날짜를 선택했다는 의미로 날짜의 배경색을 변경합니다. 표시하는 색상은 theme이라는 Props를 통해 변경할 수 있습니다.
- 참고로 달력에서 사용하는 색상은 앱을 리로드해야 반영됩니다.

![image9](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/9.png)
> 달력에 표시하기

## 데이터를 달력과 연동하기

- 데이터의 날짜를 표시하도록 구현해보겠습니다. 우선 CalendarScreen에서 LogContext의 logs 배열을 받아오세요.

> screens/CalendarScreen.js

```jsx
import React, {useContext} from 'react';
import CalendarView from '../components/CalendarView';
import LogContext from '../contexts/LogContext';

function CalendarScreen() {
  const {logs} = useContext(LogContext);
  return <CalendarView />;
}

export default CalendarScreen;
```

- 다음으로 이 logs 배열을 Calendar 컴포넌트의 markedDates Props 형태로 변환해줍니다. 변환된 객체는 CalendarView에 전달해주세요.

> screens/CalendarScreen.js

```jsx
import {format} from 'date-fns';
import React, {useContext} from 'react';
import CalendarView from '../components/CalendarView';
import LogContext from '../contexts/LogContext';

function CalendarScreen() {
  const {logs} = useContext(LogContext);
  const markedDates = logs.reduce((acc, current) => {
    const formattedDate = format(new Date(current.date), 'yyyy-MM-dd');
    acc[formattedDate] = {marked: true};
    return acc;
  }, {});
  
  return <CalendarView markedDates={markedDates} />;
}

export default CalendarScreen;
```

- 다음으로 selectedDate라는 상태를 만들어주세요. 이 값은 현재 선택된 날짜를 의미하며 기본값은 오늘 날짜입니다.

> screens/CalendarScreen.js

```jsx
import {format} from 'date-fns';
import React, { useContext, useState } from 'react';
import CalendarView from '../components/CalendarView';
import LogContext from '../contexts/LogContext';

function CalendarScreen() {
  const {logs} = useContext(LogContext);
  const [selectedDate, setSelectedDate] = useState(
    format(new Date(), 'yyyy-MM-dd'),
  );
  
  const markedDates = logs.reduce((acc, current) => {
    const formattedDate = format(new Date(current.date), 'yyyy-MM-dd');
    acc[formattedDate] = {marked: true};
    return acc;
  }, {});
  
  return (
    <CalendarView 
      markedDates={markedDates}
      selectedDate={selectedDate}
      onSelectDate={setSelectedDate}
    />
  );
}

export default CalendarScreen;
```

- selectedDate와 setSelectedDate 또한 CalendarView에 전달해주세요. setSelectedDate는 onSelectDate라는 이름으로 설정하겠습니다.
- 그다음에는 CalendarView에서 markedSelectedDate라는 새로운 객체를 만들어 현재 날짜에 selected: true를 설정하세요.

> components/CalendarView.js

```jsx
import React from 'react';
import {Calendar} from 'react-native-calendars';
import {StyleSheet} from 'react-native';

function CalendarView({markedDates, selectedDate, onSelectDate}) {
  const markedSelectedDate = {
    ...markedDates,
    [selectedDate]: {
      selected: true,
      marked: markedDates[selectedDate]?.marked,
    },
  },
  
  return (
    <Calendar
      style={styles.calendar}
      markedDates={markedSelectedDate}
      theme={{
        selectedDayBackgroundColor: '#009688',
        arrowColor: '#009688',
        dotColor: '#009688',
        todayTextColor: '#009688',
      }}
    />
  );
}

...

```

- 이제 달력에서 날짜를 선택했을 때 onSelectDate를 호출해줘야 합니다. 이는 Calendar에 onDayPress라는 Props를 설정해 구현할 수 있습니다.

> components/CalendarView.js = return

```jsx
function CalendarView({markedDates, selectedDate, onSelectDate}) {
  ...
  
  return (
    <Calendar 
      style={styles.calendar}
      markedDates={markedSelectedDate}
      onDayPress={(day) => {
        onSelectDate(day.dateString);
      }}
      theme={{
        selectedDayBackgroundColor: '#009688',
        arrowColor: '#009688',
        dotColor: '#009688',
        todayTextColor: '#009688',
      }}
    />
  );
}
```

- 참고로 여기서 day 객체는 다음과 같이 구성되어 있습니다.

```json
{
  "dateString": "2021-08-23", 
  "day": 23, 
  "month": 8, 
  "timestamp": 1629676800000, 
  "year": 2021
}
```

## 달력 하단에 로그 목록 보여주기

- 이번에는 특정 날짜를 선택했을 때 그날 작성된 로그들을 달력 하단에 목록으로 띄워볼 텐데, 이때 FeedList를 재사용할 수 있습니다. FeedList를 CalendarView 하단에 보여주는 것은 아니고, FeedList에서 사용한 FlatList에 ListHeaderComponent라는 Props를 사용할 것입니다. 이 Props를 통해 FlatList의 내용 상단부에 특정 컴포넌트를 보여줄 수 있습니다.
- FeedList 컴포넌트를 다음과 같이 수정하세요.

> components/FeedList.js

```jsx
import React from 'react';
import {FlatList, StyleSheet, View} from 'react-native';
import FeedListItem from './FeedListItem';

function FeedList({logs, onScrolledToBottom, ListHeaderComponent}) {
  ...
  
  return (
    <FlatList 
      data={logs}
      style={styles.block}
      renderItem={({item}) => <FeedListItem log={item} />}
      keyExtractor={(log) => log.id}
      ItemSeparatorComponent={() => <View style={styles.separator} />}
      onScroll={onScroll}
      ListHeaderComponent={ListHeaderComponent}
    />
  );
}

...

```

- 그다음에는 CalendarScreen에서 현재 선택된 날짜로 로그를 필터링해 FeedList에 전달하고 ListHeaderComponent에는 CalendarView를 설정하세요.

> screens/CalendarScreen.js

```jsx
import {format} from 'date-fns';
import React, {useContext, useState} from 'react';
import CalendarView from '../components/CalendarView';
import FeedList from '../components/FeedList';
import LogContext from '../contexts/LogContext';

function CalendarScreen() {
  ...
  
  const filteredLogs = logs.filter(
    (log) => format(new Date(log.date), 'yyyy-MM-dd') === selectedDate),
  );
  
  return (
    <FeedList 
      logs={filteredLogs}
      ListHeaderComponent={
        <CalendarView 
          markedDates={markedDates}
          selectedDate={selectedDate}
          onSelectDate{setSelectDate}
        />
      }
    />
  );
}

export default CalendarScreen;
```

- 달력에서 날짜를 선택했을 때 달력 하단에 로그 목록이 나타나는지 확인

![image10](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/10.png)
> 달력 하단에 로그 보여주기

## useMemo Hook으로 최적화하기

- 현재 이 컴포넌트는 리렌더링될 때마다 markedDates를 생성합니다. 날짜가 변경될 때도 컴포넌트가 리렌더링되는데요. markedDates는 날짜가 바뀌어도 변하지 않기 때문에 매번 생성할 필요가 없습니다.
- 이러한 상황에는 useMemo라는 Hook을 사용해 값을 메모이제이션(memoization)해 최적화할 수 있습니다. 메모이제이션이란 동일한 계산을 반복해야 할 때 불필요한 연산을 제거하기 위해 이전에 계산한 값을 재사용해 처리를 최적화하는 것을 의미합니다.
- 이 Hook의 사용법은 다음과 같습니다.

```javascript
const value = useMemo(() => compute(a, b), [a, b]);
```

- 이렇게 Hook을 사용하면 a나 b의 값이 변경될 때만 값이 연산됩니다.
- CalendarScreen에서 useMemo Hook을 사용한다면 다음과 같이 사용할 수 있습니다.

> screens/CalendarScreen.js

```jsx
import {format} from 'date-fns';
import React, {useContext, useMemo, useState} from 'react';
import CalendarView from '../components/CalendarView';
import FeedList from '../components/FeedList';
import LogContext from '../contexts/LogContext';

function CalendarScreen() {
  const {logs} = useContext(LogContext);
  const [selectedDate, setSelectedDate] = useState(format(new Date(), 'yyyy-MM-dd'));
  
  const markedDates = useMemo(
    () => 
      logs.reduce((acc, current) => {
        const formattedDate = format(new Date(current.date), 'yyyy-MM-dd');
        acc[formattedDate] = {marked: true);
        return acc;        
      }, {}),
    [logs],
  );
  
  ...
  
}

...

```

- 이렇게 하면 logs 배열이 바뀔 때만 logs.reduce 함수가 수행됩니다
- filteredLogs 배열을 만드는 부분에 useMemo를 사용하는 것도 생각해볼 수 있는데요. 이 상황에 useMemo를 사용하는 것은 불필요합니다. 
- 이 컴포넌트가 리렌더링되는 시점을 생각해보세요. selectedDate가 바뀌거나 logs가 바뀔 때인데 어차피 매번 필터링을 다시 해야 하기 때문입니다.

## 날짜 및 시간 수정 기능 구현하기

- 이제 로그를 작성할 때, react-native-modal-datetime-picker라는 라이브러리를 사용해 로그의 날짜 및 시간을 수정하는 기능을 구현해보겠습니다. 우선 해당 라이브러리를 설치해주세요.

```
yarn add react-native-modal-datetime-picker @react-native-community/datetimepicker
```

- @react-native-community/datetimepicker는 iOS와 안드로이드 각각 플랫폼에 특화된 날짜/시간 선택 컴포넌트를 제공합니다. 그리고 react-native-modal-datetime-picker는 날짜/시간 선택 컴포넌트를 모달 형태로 쉽게 사용할 수 있게 해줍니다.
- 이 라이브러리는 네이티브 코드를 사용하기 때문에, 라이브러리를 설치한 후 시뮬레이터를 다시 실행해줘야 합니다.

```
yarn android
npx pod-install
yarn ios
```

## WriteHeader에서 날짜 및 시간 보여주기

- 우선 WriteScreen에서 date 상태를 만들어주세요. 그리고 onSave에서 수정하거나 새로 저장할 때 date 상태를 사용하도록 변경합니다. WriteHeader에는 date와 onChangeDate를 Props로 넣어주세요.

> screens/WriteScreen.js

```jsx
import {useNavigation} from '@react-navigation/native';
import React, {useContext, useState} from 'react';
import {Alert, KeyboardAvoidingView, Platform, StyleSheet} from 'react-native';
import {SafeAreaView} from 'react-native-safe-area-context';
import WriteEditor from '../components/WriteEditor';
import WriteHeader from '../components/WriteHeader';
import LogContext from '../contexts/LogContext';

function WriteScreen({route}) {
  const log = route.params?.log;
  
  const [title, setTitle] = useState(log?.title ?? '');
  const [body, setBody] = useState(log?.body ?? '');
  const navigation = useNavigation();
  const [date, setDate] = useState(log ? new Date(log.date) : new Date());
  const [onCreate, onModify, onRemove] = useContext(LogContext);
  
  ...
  
  const onSave = () => {
    if (log) {
      onModify({
        id: log.id,
        date: date.toISOString(),
        title,
        body,
      }); 
    } else {
      onCreate({
        title,
        body,
        date: date.toISOString(),
      });
    }
    navigation.pop();
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
          date={date}
          onChangeDate={setDate}
        />
        <WriteEditor 
          title={title}
          body={body}
          onChangeText={setTitle}
          onChangeBody={setBody}
        />
      </KeyboardAvoidingView>
    </SafeAreaView>
  );
}

...

```

- 그다음에는 WriteHeader에서 Props로 받아온 date를 보여주도록 수정해야 합니다. 이는 컴포넌트 중앙에 위치시켜봅시다. position: 'absolute' 속성을 가진 View를 사용해 간단하게 레이아웃을 구성해보겠습니다.

> components/WriteHeader.js

```jsx
import {useNavigation} from '@react-navigation/native';
import {format} from 'date-fns';
import {ko} from 'date-fns/locale';
import React from 'react';
import {Pressable, StyleSheet, Text, View} from 'react-native';
import TransparentCircleButton from './TransparentCircleButton';

function WriteHeader({onSave, onAskRemove, isEditing, date, onChangeDate}) {
  const navigation = useNavigation();
  const onGoBack = () => {
    navigation.pop();
  };
  
  return (
    <View style={styles.block}>
      ...
      <View style={styles.center}>
        <Pressable>
          <Text>
            {format(new Date(date), 'PPP', {
              locale: ko,
            })}
          </Text>
        </Pressable>
        <View style={styles.separator} />
        <Pressable>
          <Text>{format(new Date(date), 'p', {locale: ko})}</Text>
        </Pressable>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  ...
  
  center: {
    position: 'absolute',
    left: 0,
    right: 0,
    top: 0,
    bottom: 0,
    alignItems: 'center',
    justifyContent: 'center',
    zIndex: -1,
    flexDirection: 'row',
  },
  separator: {
    width: 8,
  },
});

export default WriteHeader;
```

- center 스타일에서는 position: 'absolute'를 설정하고 left, right, top, bottom 값을 모두 0으로 설정했는데, 이는 이 컴포넌트의 상위 컴포넌트 크기만큼 꽉 채우겠다는 의미
- zIndex라는 속성은 컴포넌트가 다른 컴포넌트와 위치가 중첩될 때 앞 레이어에 나타날지 뒤 레이어에 나타날지 결정하는 값인데, 기본적으로 더 높은 값이 더 낮은 값을 가진 컴포넌트를 가립니다.
- 만약 이 값을 -1로 설정하면 다른 컴포넌트들의 뒤 레이어에 나타나게 되고, 설정하지 않으면 투명한 레이어가 기존 컴포넌트를 모두 가리는 것이나 마찬가지이기 때문에 뒤로가기나 저장, 삭제 버튼이 눌리지 않게 됩니다.
- 컴포넌트를 수정한 뒤, 다음과 같이 날짜와 시간이 잘 나타나는지 확인해보세요.

![image11](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/11.png)
> WrhiteHeader에 날짜 및 시간 보여주기 

## DateTimePickerModal 컴포넌트 사용하기
- 이번에는 날짜 또는 시간을 누르면 DateTimePickerModal 컴포넌트를 보여줘서 날짜를 수정할 수 있게 만들어주겠습니다.
- 이 컴포넌트는 다음과 같이 사용합니다.

```jsx
<DateTimePickerModal
  onConfirm={onConfirm}
  onCancel={onCancel}
  mode={mode}
  date={date}
  isVisible={visible}
/>
```

- onConfirm: 날짜를 선택했을 때 호출되는 함수입니다. 함수의 파라미터는 Date 객체입니다.
- onCancel: 날짜 선택을 취소했을 때 호출되는 함수입니다.
- mode: 모달의 모드를 설정합니다. 설정할 수 있는 값은 date, time, datetime입니다.
- date: 컴포넌트에서 보여졌을 때 초깃값으로 설정할 Date 객체 타입의 값입니다.
- isVisible: 이 값을 true로 설정하면 모달이 보이고, false로 설정하면 모달이 사라집니다.

- 날짜를 눌렀을 때는 'date' 모드, 시간을 선택했을 때는 'time' 모드를 사용하겠습니다. 컴포넌트를 다음과 같이 수정해보세요.

> components/WriteHeader.js

```jsx
import {useNavigation} from '@react-navigation/native';
import {format} from 'date-fns';
import {ko} from 'date-fns/locale';
import React, {useState} from 'react';
import {Pressable, StyleSheet, Text, View} from 'react-native';
import TransparentCircleButton from './TransparentCircleButton';
import DateTimePickerModal from 'react-native-modal-datetime-picker';

function WriteHeader({onSave, onAskRemove, isEditing, date, onChangeDate}) {
  const navigation = useNavigation();
  const onGoBack = () => {
    navigation.pop();
  };
  
  const [mode, setMode] = useState('date');
  const [visible, setVisible] = useState(false);
  
  const onPressDate = () => {
    setMode('date');
    setVisible(true);
  };
  
  const onPressTime = () => {
    setMode('time');
    setVisible(true);
  };
  
  const onConfirm = (selectedDate) => {
    setVisible(false);
    onChangeDate(selectedDate);
  };
  
  const onCancel = () => {
    setVisible(false);
  };
  
  return (
    <View style={styles.block}>
      ...
      <View style={styles.center}>
        <Pressable onPress={onPressDate}>
          <Text>
            {format(new Date(date), 'PPP', {
              locale: ko,
            })}
          </Text>
        </Pressable>
        <View style={styles.seperator} />
        <Pressable onPress={onPressTime}>
          <Text>{format(new Date(date), 'p', {locale: ko})}</Text>
        </Pressable>
      </View>
      <DateTimePickerModal
        isVisible={visible}
        mode={mode}
        onConfirm={onConfirm}
        onCancel={onCancel}
        date={date}
      />
    </View>
  );
}

...

```

- 여기까지 작성했다면 이제 로그 작성 중에 날짜 또는 시간을 눌러서 수정할 수 있을 것입니다.

![image12](https://raw.githubusercontent.com/yonggyo1125/lecture_reactnative/master/07%20%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20II/images/12.png)
> DateTimePickerModal 사용하기

# useReducer Hook 함수 사용하기

- useReducer 함수는 상태를 관리할 때 사용할 수 있는 또 다른 Hook 함수입니다. 이 함수를 사용할 때는 다음과 같은 개념이 사용됩니다.
  - state: 상태
  - action: 변화를 정의하는 객체
  - reducer: state와 action을 파라미터로 받아와서 그다음 상태를 반환하는 함수
  - dispatch: action을 발생시키는 함수
- 이 Hook은 이번 예제와 같이 useState를 여러 번 사용하는 상황에서 사용하면 유용할 수 있습니다. 유용할 수도 있다는 건 무조건 이 Hook을 사용할 필요는 없다는 뜻입니다. 상황에 따라 useState로만 구현하는 게 편할 때도 있고, useReducer를 사용하는 게 편할 때도 있습니다.
- WriteHeader의 경우도 꼭 useReducer를 사용할 필요는 없습니다. 하지만 useReducer를 사용해보기에 적합한 상황이기도 해서 공부 삼아 사용법을 한번 알아보겠습니다.
- onPressDate 함수를 보면 setMode와 setVisible을 호출하고 있는데요.

```jsx
const onPressDate = () => {
  setMode('date');
  setVisible(true);
};
```

- 이렇게 각기 다른 상태를 동시에 업데이트하는 상황에는 useReducer로 구현하는 것을 고민해보면 좋습니다.
- 이 Hook을 사용해보기 전에 간단한 예시를 확인해보겠습니다.
- 카운터를 useReducer로 구현한다면 다음과 같이 구현할 수 있습니다.

```jsx
const initialState = {value: 1};

function reducer(state, action) {
  switch (action.type) {
    case 'increase':
      return {value: state.value + 1};
    case 'decrease':
      return {value: state.value - 1};
    default:
      throw new Error('Unhandled action type');
  }
}

function Counter() {
  const [sdate, dispatch] = useReducer(reducer, initialState);
  
  const onIncrease = () => dispatch({type: 'increase'});
  const onDecrease = () => dispatch({type: 'decrease'});
  
  ...
}
```

- 이 코드를 보고 ‘왜 이 상황에 useState를 사용하지 않고 이렇게 복잡하게 구현하지?’라고 생각할 수 있습니다. 이렇게 간단히 업데이트하는 상황이라면 useState를 사용하는 것이 더 적합합니다.
- useReducer는 상태를 업데이트하는 로직을 컴포넌트 바깥에 구현할 수 있다는 장점이 있습니다. 덧붙여 dispatch라는 함수 하나로 다양하게 업데이트할 수 있기 때문에 Context와 함께 사용하면 유용합니다.
- useReducer의 첫 번째 인자에는 reducer 함수를 넣고, 두 번째 인자에는 상태의 초깃값을 넣습니다. 이 함수의 결과물 배열의 첫 번째 원소는 현재 상태이고, 두 번째 원소는 dispatch 함수입니다.
- onIncrease 함수를 보면 dispatch({type: 'decrease'})를 실행했죠? 이렇게 액션 객체를 인자에 넣어 dispatch 함수가 호출되면 reducer 함수가 호출됩니다. 이때 state 파라미터는 현재 상태를 가리키고, action은 dispatch 함수의 인자로 넣은 액션 객체를 가리킵니다. reducer에서 반환하는 값이 그다음 업데이트할 값으로 사용됩니다.
- reducer를 보면 action.type에 따라 업데이트하도록 구현되어 있습니다. action 객체는 보통 이렇게 type이라는 키를 가지고 있습니다. 상황에 따라 다른 값을 action 객체에 자유롭게 넣을 수 있습니다. 

```jsx
const initialState = {value: 1};

function reducer(state, action) {
  switch (action.type) {
    case 'increase':
      return {value: state.value + action.diff};
    case 'decrease':
      return {value: state.value - action.diff};
    default:
      throw new Error('Unhandled action type');
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const onIncrease = () => dispatch({type: 'increase', diff: 1});
  const onDecrease = () => dispatch({type: 'decrease', diff: 1});
  
  ....
  
}
```

- WriteHeader를 useReducer로 구현하는 경우

> components/WriteHeader.js

```jsx
import {useNavigation} from '@react-navigation/native';
import {format} from 'date-fns';
import {ko} from 'date-fns/locale';
import React, {useReducer} from 'react';
import {Pressable, StyleSheet, Text, View} from 'react-native';
import TransparentCircleButton from './TransparentCircleButton';
import DateTimePickerModal from 'react-native-modal-datetime-picker';

const initialState = {mode: 'date', visible: false};
function reducer(state, action) {
  switch (action.type) {
    case 'open':
      return {
        mode: action.mode,
        visible: true,
      };
    case 'close':
      return {
        ...state,
        visible: false,
      };
    default: 
      throw new Error('Unhandled action type');
  }
}

function WriteHeader({onSave, onAskRemove, isEditing, date, onChangeDate}) {
  const navigation = useNavigation();
  const onGoBack = () => {
    navigation.pop();
  };
  
  const [state, dispatch] = useReducer(reducer, initialState);
  const open = (mode) => dispatch({type: 'open', mode});
  const close = () => dispatch({type: 'close'});
  
  const onConfirm = (selectedDate) => {
    close();
    onChangeDate(selectedDate);
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
      <View style={styles.center}>
        <Pressable onPress={() => open('date')}>
          <Text>
            {format(new Date(date), 'PPP', {
              locale: ko,
            })}
          </Text>
        </Pressable>
        <View style={styles.seperator} />
        <Pressable onPress={() => open('time')}>
          <Text>{format(new Date(date), 'p', {locale: ko})}</Text>
        </Pressable>
      </View>
      <DateTimePickerModal 
        isVisible={state.visible}
        mode={state.mode}
        onConfirm={onConfirm}
        onCancel={close}
        date={date}
      />
    </View>
  );
}

...

```

- 앞으로 상태를 관리할 때 기본적으로는 useState를 사용해 구현하고, 함께 바뀌는 상태 값들이 많아졌을 때는 useReducer를 사용할지 고민해보면 좋습니다.

# AsyncStorage로 데이터 유지하기

- AsyncStorage로 앱을 종료해도 데이터를 유지하도록 구현해주겠습니다.
- 라이브러리를 설치해주세요.

```
yarn add @react-native-community/async-storage
```

- 시뮬레이터를 다시 실행해주세요.

```
yarn android
npx pod-install 
yarn ios
```

- storages 디렉터리를 만들고 그 안에 logsStorage.js 파일을 다음과 같이 작성하세요.

> storages/logsStorage.js

```jsx
import AsyncStorage from '@react-native-community/async-storage';

const key = 'logs';

const logsStorage = {
  async get() {
    try {
      const raw = await AsyncStorage.getItem(key);
      const parsed = JSON.parse(raw);
      return parsed;
    } catch (e) {
      throw new Error('Failed to load logs');
    }
  },
  async set(data) {
    try {
      await AsyncStorage.setItem(key, JSON.stringify(data));
    } catch (e) {
      throw new Error('Failed to save logs');
    }
  },
};

export default logsStorage;
```

- logsStorage를 만들었으니 LogContext에서 사용해주겠습니다. 이제 logs 상태의 초깃값도 지우고, 빈 배열로 변경해주세요.

> contexts/LogContext.js

```jsx
import React, {useEffect, useRef} from 'react';
import {createContext, useState} from 'react';
import {v4 as uuidv4} from 'uuid';
import logStorage from '../storages/logsStorage';

const LogContext = createContext();

export function LogContextProvider({children}) {
  const initialLogsRef = useRef(null);
  const [logs, setLogs] = useState([]);
  
  const onCreate = ({title, body, date}) => {
    ...
  };
  
  useEffect(() => {
    // useEffect 내에서 async 함수를 만들고 바로 호출
    // IIFE 패턴
    (async () => {
      const savedLogs = await logsStorage.get();
      if (savedLogs) {
        initialLogsRef.current = savedLogs;
        setLogs(savedLogs);
      }
    })();
  }, []);
  
  useEffect(() => {
    if (logs === initialLogsRef.current) {
      return;
    }
    
    logsStorage.set(logs);
  }, [logs]);
   
   return (
    <LogContext.Provider value={{logs, onCreate, onModify, onRemove}}>
      {children}
    </LogContext.Provider>
   );
}, []);
```

- . 우선 딱 한 번만 호출되는 첫 번째 useEffect 내부에서 IIFE 패턴을 사용해 async 함수를 만들어 바로 호출해줬습니다. IIFE 패턴이 보기 복잡하면 함수를 선언하고 그다음 라인에서 호출해 사용해도 무방합니다. 다음 코드는 IIFE를 사용하지 않는 코드의 예시입니다.

```jsx
useEffect(() => {
  const save = async () => {
    const savedLogs = await logsStorage.get();
    if (savedLogs) {
      initialLogsRef.current = savedLogs;
      setLogs(savedLogs);
    }
  }
  save();
}, []);
```

- 두 번째 useEffect에서는 logs 배열이 바뀔 때마다 logsStorage에 저장하도록 만들었습니다. 앱을 켰을 때 데이터가 있다면 데이터를 불러와 setLogs를 호출하게 되는데, 불러오는 과정에서 호출될 때도 두 번째 useEffect가 실행되기 때문에 실제로는 데이터에 변경이 없는데도 한 번 더 저장하게 됩니다. 이를 방지하기 위해 불러온 초기 데이터를 useRef로 기억하도록 만들었습니다. 이렇게 하면 불필요한 저장을 하지 않아 앱을 가동할 때 사용하는 리소스를 아낄 수 있죠.