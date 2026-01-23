---
layout: default
title: 명시성
parent: Mental Model
nav_order: 6
permalink: /docs/mental-model/explicitness
---

# 명시성: 무엇을 암시하고 무엇을 명시할 것인가

## 들어가며 — 암시는 편리하지만 위험하다

코드를 작성할 때  
자연스럽게 이런 선택을 한다.

```tsx
// 암시적 설계
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);

// 사용
if (isLoading) return <Loading />;
if (isError) return <Error />;
if (isSuccess) return <Success />;
```

이 코드는 작동한다.  
하지만 여기에는 **암시된 규칙**이 있다.

> "이 세 상태는 동시에 true가 될 수 없다."

이 규칙은 코드 어디에도 명시되어 있지 않다.  
타입 시스템도 이를 막지 못한다.

```tsx
// 이게 가능하다 (하지만 말이 안 됨)
setIsLoading(true);
setIsSuccess(true);  // 동시에?
```

개발자는 이 규칙을 **기억**해야 한다.  
하나라도 잊으면 버그가 된다.

이 문서는  
**암시적 설계가 왜 문제가 되는지**,  
그리고 **명시적 설계가 무엇을 해결하는지**를  
다시 생각해보기 위해 쓰였다.

---

## 명시성과 암시성

### 명시적 설계

명시적 설계는  
**코드 자체가 모든 규칙을 드러내는** 설계다.

```tsx
// 명시적 설계
type Status = 'idle' | 'loading' | 'success' | 'error';
const [status, setStatus] = useState<Status>('idle');

// 사용
if (status === 'loading') return <Loading />;
if (status === 'error') return <Error />;
if (status === 'success') return <Success />;
```

이제 불가능한 상태는  
타입 시스템이 막아준다.

- `loading`과 `success`가 동시에 true? → 불가능
- 세 개의 상태를 동기화할 책임? → 사라짐
- 규칙을 기억해야 하는 부담? → 사라짐

### 암시적 설계

암시적 설계는  
**코드에 드러나지 않은 규칙을 가정하는** 설계다.

```tsx
// 암시적 설계
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);

// 암시된 규칙:
// - 이 세 상태는 동시에 true가 될 수 없다
// - 하나가 true가 되면 나머지는 false여야 한다
// - 하지만 이 규칙은 코드에 없다
```

이 규칙은:
- 코드에 없다
- 타입 시스템이 보호하지 않는다
- 개발자가 기억해야 한다
- 문서에 의존한다

---

## 암시적 설계의 비용

### 1. 기억해야 하는 부담

암시적 설계는  
**개발자가 규칙을 기억해야 한다**고 가정한다.

```tsx
// 암시적: 매번 세 개를 올바르게 설정해야 함
const handleSubmit = async () => {
  setIsLoading(true);
  setIsError(false);      // 기억해야 함
  setIsSuccess(false);     // 기억해야 함
  
  try {
    await submit();
    setIsLoading(false);
    setIsError(false);     // 기억해야 함
    setIsSuccess(true);
  } catch (error) {
    setIsLoading(false);
    setIsError(true);
    setIsSuccess(false);   // 기억해야 함
  }
};
```

하나라도 잊으면 버그가 된다.

### 2. 불가능한 상태를 허용한다

암시적 설계는  
**논리적으로 불가능한 상태**를 만들 수 있다.

```tsx
// 이게 가능하다 (하지만 말이 안 됨)
setIsLoading(true);
setIsSuccess(true);  // 동시에 로딩 중이고 성공?

// 이것도 가능하다 (하지만 말이 안 됨)
setIsError(true);
setIsSuccess(true);  // 동시에 에러이고 성공?

// 이것도 가능하다 (하지만 말이 안 됨)
setIsLoading(false);
setIsError(false);
setIsSuccess(false);  // 아무 상태도 아닌 상태?
```

이런 상태들이 실제로 발생하면  
버그를 찾기 어려워진다.

### 3. 동기화의 책임

암시적 설계는  
**여러 상태를 동기화하는 책임**을 개발자에게 전가한다.

```tsx
// 상태 간 의존성이 암시적
const [isEditing, setIsEditing] = useState(false);
const [editedValue, setEditedValue] = useState('');

// 규칙:
// - isEditing이 true일 때만 editedValue가 의미 있음
// - isEditing이 false가 되면 editedValue는?
// - 이 규칙은 코드에 없다
```

이런 의존성이 많아질수록  
동기화의 책임도 커진다.

### 4. 이해의 비용

암시적 설계는  
**코드를 읽는 사람이 규칙을 추론**해야 한다.

```tsx
// 이 코드를 읽는 사람은:
const [count, setCount] = useState(0);
const [isEven, setIsEven] = useState(true);

useEffect(() => {
  setIsEven(count % 2 === 0);
}, [count]);

// 다음을 추론해야 함:
// - isEven은 count로부터 계산 가능
// - count가 바뀌면 isEven도 바뀜
// - 하지만 이 관계는 코드에 명시되지 않음
```

코드를 읽는 사람은  
**암시된 규칙을 발견**해야 한다.

---

## 명시적 설계의 가치

### 1. 타입 시스템이 보호한다

명시적 설계는  
**타입 시스템이 불가능한 상태를 막아준다**.

```tsx
// 명시적: 타입이 불가능한 상태를 막음
type Status = 'idle' | 'loading' | 'success' | 'error';
const [status, setStatus] = useState<Status>('idle');

// 이건 컴파일 에러
setStatus('loading');
setStatus('success');  // 이미 'loading'인데?
```

타입 시스템이  
개발자가 실수할 수 있는 경우를 줄여준다.

### 2. 규칙을 기억할 필요가 없다

명시적 설계는  
**코드 자체가 규칙을 드러낸다**.

```tsx
// 명시적: 코드가 규칙을 드러냄
type EditMode = 
  | { type: 'viewing' }
  | { type: 'editing'; value: string };

const [mode, setMode] = useState<EditMode>({ type: 'viewing' });

// 규칙이 코드에 있다:
// - viewing 모드에는 value가 없음
// - editing 모드에는 value가 필수
// - 기억할 필요 없음
```

코드를 읽으면  
규칙이 바로 보인다.

### 3. 동기화 책임이 사라진다

명시적 설계는  
**상태 간 의존성을 구조로 표현**한다.

```tsx
// 명시적: 의존성이 구조에 있음
type CountState = {
  count: number;
  isEven: boolean;  // count로부터 계산 가능
};

// 또는 더 명시적으로
const [count, setCount] = useState(0);
const isEven = count % 2 === 0;  // 파생 값, 상태 아님
```

의존성이 구조에 있으면  
동기화할 필요가 없다.

### 4. 이해가 쉬워진다

명시적 설계는  
**코드를 읽는 사람이 추론할 필요가 없다**.

```tsx
// 명시적: 추론 불필요
type UserState = 
  | { type: 'loading' }
  | { type: 'success'; user: User }
  | { type: 'error'; error: Error };

const [state, setState] = useState<UserState>({ type: 'loading' });

// 가능한 상태가 명확함:
// - loading: 데이터 로딩 중
// - success: 사용자 데이터 있음
// - error: 에러 발생
// 추론할 필요 없음
```

코드를 읽으면  
가능한 상태가 바로 보인다.

---

## 암시적 설계가 생기는 이유

### 1. 편의성

암시적 설계는  
**처음에는 편리해 보인다**.

```tsx
// 간단해 보임
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);
```

여러 상태를 따로 관리하는 것이  
처음에는 단순해 보인다.

하지만 시간이 지나면서  
상태 간 의존성이 생기고,  
규칙이 복잡해지고,  
동기화 책임이 커진다.

### 2. 점진적 복잡도

암시적 설계는  
**점진적으로 복잡해진다**.

```tsx
// 처음: 단순
const [isLoading, setIsLoading] = useState(false);

// 나중: 에러 추가
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);

// 더 나중: 성공 추가
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);

// 어느 순간: 규칙이 복잡해짐
// 하지만 여전히 암시적
```

각 단계에서는  
여전히 관리 가능해 보인다.

하지만 어느 순간부터  
규칙을 기억하기 어려워진다.

### 3. 타입 시스템의 한계

일부 언어나 프레임워크는  
**명시적 설계를 표현하기 어렵다**.

```tsx
// JavaScript에서는 타입이 없어서
// 명시적 설계를 강제하기 어려움
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);

// TypeScript를 쓰면 가능
type Status = 'loading' | 'error' | 'success';
const [status, setStatus] = useState<Status>('loading');
```

하지만 TypeScript를 쓴다고 해서  
자동으로 명시적 설계가 되는 것은 아니다.

의도적으로 명시적 설계를 선택해야 한다.

---

## 명시적 설계의 비용

명시적 설계도 비용이 있다.

### 1. 초기 작성 비용

명시적 설계는  
**처음 작성할 때 더 많은 코드**가 필요하다.

```tsx
// 암시적: 간단
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);

// 명시적: 더 많은 코드
type Status = 'idle' | 'loading' | 'success' | 'error';
const [status, setStatus] = useState<Status>('idle');
```

타입을 정의하고,  
구조를 설계하는 데 시간이 걸린다.

### 2. 변경 비용

명시적 설계는  
**변경할 때 타입도 함께 수정**해야 한다.

```tsx
// 새 상태 추가 시
type Status = 
  | 'idle' 
  | 'loading' 
  | 'success' 
  | 'error'
  | 'retrying';  // 타입 수정 필요

// 모든 사용처 확인 필요
```

하지만 이 비용은  
**명시적 설계가 주는 이득**보다 작다.

### 3. 학습 곡선

명시적 설계는  
**패턴을 학습**해야 한다.

```tsx
// 명시적 설계 패턴
type State = 
  | { type: 'A'; value: string }
  | { type: 'B'; count: number };

// 이 패턴을 이해해야 함
```

하지만 한 번 이해하면  
다른 곳에서도 적용할 수 있다.

---

## 명시성의 판단 기준

모든 것을 명시적으로 만들 필요는 없다.

### 명시적으로 만들 가치가 있는 것

#### 1. 상태 간 의존성

```tsx
// ❌ 암시적: 의존성이 숨어 있음
const [isEditing, setIsEditing] = useState(false);
const [editedValue, setEditedValue] = useState('');

// ✅ 명시적: 의존성이 구조에 있음
type EditMode = 
  | { type: 'viewing' }
  | { type: 'editing'; value: string };
```

#### 2. 상호 배타적인 상태

```tsx
// ❌ 암시적: 동시에 true 가능
const [isLoading, setIsLoading] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);

// ✅ 명시적: 동시에 불가능
type Status = 'loading' | 'success' | 'error';
```

#### 3. 파생 가능한 값

```tsx
// ❌ 암시적: 파생 상태
const [items, setItems] = useState([]);
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(items.length);
}, [items]);

// ✅ 명시적: 파생 값
const [items, setItems] = useState([]);
const count = items.length;
```

### 암시적으로 두는 선택도 가능한 것

#### 1. 독립적인 상태

```tsx
// 독립적이면 암시적이어도 괜찮음
const [username, setUsername] = useState('');
const [theme, setTheme] = useState('light');

// 서로 무관하므로 명시적 설계 불필요
```

#### 2. 단순한 계산

```tsx
// 단순한 계산은 암시적이어도 괜찮음
const isEmpty = items.length === 0;
const isValid = input.length >= 3;

// 복잡하지 않으므로 명시적 설계 불필요
```

---

## 명시성의 단계

명시성은 단계적이다.

### 1단계: 암시적

```tsx
// 모든 규칙이 암시적
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
```

### 2단계: 주석으로 명시

```tsx
// 주석으로 규칙 명시 (하지만 타입 시스템이 보호하지 않음)
// 이 세 상태는 동시에 true가 될 수 없음
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
```

### 3단계: 타입으로 명시

```tsx
// 타입으로 명시 (타입 시스템이 보호함)
type Status = 'idle' | 'loading' | 'success' | 'error';
const [status, setStatus] = useState<Status>('idle');
```

### 4단계: 구조로 명시

```tsx
// 구조로 명시 (의존성까지 명시)
type State = 
  | { type: 'idle' }
  | { type: 'loading' }
  | { type: 'success'; data: Data }
  | { type: 'error'; error: Error };

const [state, setState] = useState<State>({ type: 'idle' });
```

---

## 정리하며 — 명시성은 선택이다

명시적 설계는  
항상 더 나은 것은 아니다.

하지만 **의존성과 규칙이 있는 곳**에서는  
명시적 설계가 비용을 줄인다.

### 암시적 설계의 비용

- 기억해야 하는 부담
- 불가능한 상태를 허용
- 동기화의 책임
- 이해의 비용

### 명시적 설계의 가치

- 타입 시스템이 보호
- 규칙을 기억할 필요 없음
- 동기화 책임이 사라짐
- 이해가 쉬워짐

### 명시적 설계의 비용

- 초기 작성 비용
- 변경 비용
- 학습 곡선

### 판단 기준

의존성과 규칙이 있는 곳에서는  
명시적으로 만드는 선택을 고려하고,  
독립적이고 단순한 곳에서는  
암시적으로 두는 선택도 가능하다.

> 명시성은  
> 규칙을 기억하지 않아도 되게 만드는 선택이다.  
> 의존성과 제약이 있다면,  
> 그 규칙을 구조로 옮기는 편이 더 설명 가능해질 때가 많다.

명시적 설계는  
코드를 읽는 사람이  
**추론하지 않아도 되게** 만든다.

이것이 명시성의 가치다.
