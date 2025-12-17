---
title: 이모지 입력 성능 개선기
description: 2000개 이모지를 다루는 방법과 성능 최적화 여정
# author: d_o0o_b
categories: [OrbitMe]
tags: [typography]
# pin: true
math: true
mermaid: true
---


## 개요

현재 저희 서비스의 주 기능이라고 생각이 들 정도로
중요한 화면에서 자주 나오기 때문에 제일 개선을 해야할 부분이었습니다.

앱의 핵심 경험이 이모지 선택에서 시작되기 때문에 이 부분의 성능은 앱 전체의 인상을 좌우합니다.


## 문제 발견

### 초기 구현: 라이브러리 사용

처음 miniMe를 통해 이모지 바텀시트를 라이브러리를 이용해서 기능을 구현하였습니다.
하지만, 불러오는 속도는 빠르지만 입력하는 속도가 눈에 띄게 오래걸렸습니다.

**사용한 라이브러리:**
```bash
npm install rn-emoji-keyboard
```

**라이브러리의 장점**
- 설치 후 바로 사용 가능
- 카테고리별 분류 자동 지원
- 검색 기능 내장
- 최신 이모지 자동 업데이트

**하지만 심각한 문제가 있었습니다!**

### 성능 문제 측정

**이모지 선택 시 렌더링 지연**
- 이모지 바텀시트 열기: 약 300ms
- 이모지 클릭 후 반영: 약 800ms
- 총 대기 시간: 1.1초

사용자가 이모지를 선택하고 실제로 화면에 반영되기까지 1초 이상 걸리는 것은 치명적인 UX 문제였습니다.

<!-- **성능 프로파일링 결과:**
```
[Profiler] EmojiKeyboard render: 756ms
[Profiler] EmojiGrid render: 512ms
[Profiler] EmojiItem render (x2000): 1834ms total
``` -->

### 외면했던 이유

사실, 지금까지 수정 안한거는 모른척 하고 있었습니다..
하지만 계속 외면을 하기에는 주변 테스터분들도 동일한 문제를 얘기해 주셨기에 더이상 회피할 순 없었습니다.

**외면했던 핑계들**
1. "핵심 기능은 아니니까..."
2. "나중에 최적화하면 되지..."
3. "다른 급한 기능부터 만들고..."
4. "라이브러리 문제니까 어쩔 수 없어..."

하지만 테스터들의 피드백은 명확했습니다
- "이모지 선택할 때마다 버벅여요"
- "왜 이렇게 느리죠?"
- "이모지 선택하는 게 스트레스예요"

더 이상 외면할 수 없었습니다.


## 해결 시도 1: 라이브러리 대체

### 다른 라이브러리 탐색

여러 이모지 라이브러리를 테스트해봤습니다:

**1. emoji-mart-native**
```bash
npm install emoji-mart-native
```
→ React Native 지원 불안정

**2. react-native-emoji-selector**
```bash
npm install react-native-emoji-selector
```
→ 성능은 비슷, 커스터마이징 어려움

**3. react-native-emoji-board**
```bash
npm install react-native-emoji-board
```
→ 유지보수 중단됨

### 결론: 라이브러리로는 한계

이제 어느정도 프론트도 감이 잡히다보니 라이브러리를 사용해야할까? 라는 생각이 들었습니다.

**라이브러리의 공통적인 문제**
- 2000개 이상의 이모지를 한 번에 렌더링
- 불필요한 기능들로 인한 번들 크기 증가

직접 만들면 우리 서비스에 최적화된 성능과 디자인을 구현할 수 있을 것 같았습니다.


## 해결 시도 2: 직접 구현

### 1차 구현

그래서, 직접! 만들기로 했습니다.


### 1차 구현의 문제

하지만 직접 구현을 해도 총 이모지는 2천개가 넘는 상태다보니, 속도 측면에서 개선된 점이 없었습니다.

근본적인 문제는 2000개의 컴포넌트를 한 번에 렌더링한다는 것이었습니다.


## 해결 시도 3: 이모지 개수 줄이기

### 디자이너와의 논의

그래서 디자이너분과 필요하다고 생각이 드는 이모지들만 가져가자! 로 의견을 통일해서
필요한 이모지 천개로 줄여가져갔습니다.

**선별 기준**
1. 사용 빈도가 높은 이모지
2. 목표 설정에 어울리는 이모지
3. 긍정적이고 동기부여가 되는 이모지
4. 직관적으로 의미를 알 수 있는 이모지

<!-- ## 추가 최적화

### 1. FlatList로 가상화

```typescript
const EmojiGrid = () => {
  return (
    <FlatList
      data={emojis}
      renderItem={({ item }) => <EmojiButton emoji={item} />}
      numColumns={8}
      // 핵심: 화면에 보이는 것만 렌더링
      windowSize={5}
      maxToRenderPerBatch={40}
      removeClippedSubviews={true}
      // 성능 최적화
      getItemLayout={(data, index) => ({
        length: 44,
        offset: 44 * index,
        index,
      })}
    />
  );
};
```

### 2. 메모이제이션

```typescript
const EmojiButton = React.memo(({ emoji, onPress }) => {
  return (
    <Pressable onPress={() => onPress(emoji)}>
      <Text style={styles.emoji}>{emoji.unicode}</Text>
    </Pressable>
  );
}, (prev, next) => prev.emoji.unicode === next.emoji.unicode);
```

### 3. 카테고리별 lazy loading

```typescript
const CategoryTab = ({ category, onSelect }) => {
  const [emojis, setEmojis] = useState([]);

  useEffect(() => {
    // 카테고리 선택 시에만 해당 이모지 로드
    if (category === selectedCategory) {
      setEmojis(getEmojisByCategory(category));
    }
  }, [selectedCategory]);

  return <EmojiGrid emojis={emojis} />;
};
``` -->


## 토스 이모지 적용

### 왜 토스 이모지?

그러다니보니 속도 개선도 되었고 저희는 토스 이모지를 사용하고 있는데 라이브러리를 사용한 것보다 더 예쁘게 나오게 되어서
수정하길 잘했다고 생각이 듭니다!


### 시각적 비교

**시스템 이모지 (Before):**
- iOS와 Android에서 다르게 표시
- 크기 조절 시 픽셀화
- 통일감 부족

**토스 이모지 (After):**
- 모든 플랫폼에서 동일한 디자인
- 벡터 기반으로 선명함
- 우리 앱 디자인과 조화로움



## 배운 점

### 1. 라이브러리는 만능이 아니다

**라이브러리 선택 기준**
- 우리 서비스에 정말 필요한 기능인가?
- 커스터마이징이 가능한가?
- 성능이 요구사항을 만족하는가?
- 유지보수가 활발한가?

작은 기능이라면 직접 구현하는 것이 더 나을 수 있습니다.

### 2. 성능 문제는 외면하면 커진다

**문제를 발견했을 때 바로 해결해야 하는 이유**
1. 시간이 지날수록 수정 비용 증가
2. 사용자 경험 저하가 누적
3. 다른 기능 개발에 영향

"나중에 최적화"는 대부분 영원히 안 옵니다.

### 3. 데이터 최적화의 중요성

**코드 최적화 전에 데이터 최적화**
- 2000개 → 1000개로 줄이기: 즉각적인 효과
- 복잡한 코드 최적화: 시간 많이 걸림

때로는 "덜어내는" 것이 최고의 최적화입니다.

### 4. 사용자 피드백의 가치

테스터들이 지적한 문제는 실제 사용자들도 느낄 문제입니다.
피드백을 무시하지 말고 진지하게 받아들여야 합니다.

### 5. 디자이너와의 협업

**기술적 제약을 공유**
"2000개 이모지는 성능상 어렵습니다"

**함께 해결책 찾기**
"정말 필요한 이모지만 선별하면 어떨까요?"

**Win-Win 결과**
- 개발: 성능 개선
- 디자인: 더 예쁜 토스 이모지 사용


## 향후 개선 방향

### 1. 이모지 검색 기능

```typescript
const [searchQuery, setSearchQuery] = useState('');

const filteredEmojis = useMemo(() => {
  if (!searchQuery) return emojis;
  return emojis.filter(emoji =>
    emoji.name.includes(searchQuery) ||
    emoji.keywords.some(k => k.includes(searchQuery))
  );
}, [searchQuery, emojis]);
```

### 2. 최근 사용 이모지

```typescript
const [recentEmojis, setRecentEmojis] = useState([]);

const handleEmojiSelect = (emoji) => {
  // 최근 사용 목록 업데이트
  setRecentEmojis(prev => {
    const filtered = prev.filter(e => e.unicode !== emoji.unicode);
    return [emoji, ...filtered].slice(0, 24);
  });

  // AsyncStorage에 저장
  await AsyncStorage.setItem('recentEmojis', JSON.stringify(recentEmojis));
};
```

### 3. 자주 쓰는 이모지 상단 고정

사용 빈도를 분석하여 인기 있는 이모지를 상단에 배치합니다.

### 4. 이모지 애니메이션

```typescript
const AnimatedEmoji = ({ emoji, onPress }) => {
  const scale = useSharedValue(1);

  const handlePress = () => {
    scale.value = withSequence(
      withTiming(1.2, { duration: 100 }),
      withTiming(1, { duration: 100 })
    );
    onPress(emoji);
  };

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }]
  }));

  return (
    <Animated.View style={animatedStyle}>
      <Pressable onPress={handlePress}>
        <Text>{emoji.unicode}</Text>
      </Pressable>
    </Animated.View>
  );
};
```


## 마치며

작은 성능 문제라도 무시하지 말아야 한다는 것을 배웠습니다.

**핵심 교훈:**
1. 라이브러리보다 직접 구현이 나을 때가 있다
2. 성능 문제는 발견 즉시 해결하자
3. 데이터를 줄이는 것이 최고의 최적화
4. 사용자 피드백을 진지하게 받아들이자
5. 디자이너와 함께 해결책을 찾자

이모지 선택이라는 작은 인터랙션 하나도, 사용자 경험에 큰 영향을 미칩니다.
디테일에 집중하는 것이 좋은 앱을 만드는 길이라고 생각합니다!
