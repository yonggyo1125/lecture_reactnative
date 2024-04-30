> 1. 내비게시션 설정
> 2. Context API를 사용한 전역 상태 관리
> 3. date-fns를 사용한 날짜 포맷팅 
> 4. Animated를 사용한 애니메이션 효과

# 프로젝트 준비하기 
- 새로운 프로젝트 생성 

```
npx react-native init DayLog --version 0.70
```

- 필요한 라이브러리 설치 

```
cd DayLog
yarn add @react-navigation/native @react-navigation/native-stack @react-navigation/bottom-tabs react-native-screens react-native-safe-area-context react-native-vector-icons
```

- 설치가 끝났으면 iOS를 위해 Pod도 설치

```
npx pod-install
```

- 이 명령어는 프로젝트 루트 디렉터리에서 Pod를 바로 설치할 수 있게 하는 명령어입니다. 매번 Pod를 설치할 때마다 ios 디렉터리에 들어갔다가 다시 나오는 게 번거롭게 느껴질 때 사용하면 됩니다.

## react-native-vector-icons 적용하기

- react-native-vector-icons의 MaterialIcons를 사용할 수 있도록 Info.plist를 수정

```xml

```

---
# Context API 사용하기 

---
# 새 글 작성하기 

---
# 글 목록 보여주기

---
# Animated로 애니메이션 적용하기 