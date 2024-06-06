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

