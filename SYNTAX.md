# Forwardy 문법 설명 (v0.1)

Forwardy는 영어 문장처럼 읽히는 스마트컨트랙트 언어입니다. 소스에는 아래 문자만 쓸 수 있습니다.

- 영문자 `A-Z a-z`, 숫자 `0-9`, 한글 완성형 음절(가~힣)
- 공백, 줄바꿈 (탭, 유니코드 공백, 제로폭 문자는 불가)
- 기호 7개: `;` `( )` `[ ]` `{ }` `=`

`+ - * / < > . , : " _` 같은 기호는 쓸 수 없습니다. 그래서 연산자는 모두 단어(`plus`, `is greater than`)입니다.

---

## 1. 한눈에 보기

```
contract Vault {
  note (한 주소당 잔액 하나를 가진 금고)
  storage {
    balances is a map from address to amount;
    total is an amount;
  }
  capability transfer taking dest as address and sum as amount;

  public action deposit taking amt as amount {
    require amt is greater than 0 otherwise [zero amount];
    set balances at caller = balances at caller plus amt;
    set total = total plus amt;
  }

  public action balanceOf taking who as address returning amount {
    return balances at who;
  }
}
```

한 파일에는 `contract`가 하나만 있습니다. 문장 끝은 `;`, 블록은 `{ }`입니다.

---

## 2. 어휘

### 2.1 단어(식별자)
- 영문 이름은 `[A-Za-z][A-Za-z0-9]*` 형식입니다. 예: `balanceOf`, `total2`
- 한글 이름은 한글 음절만 이어 씁니다. 예: `잔액조회`
- **한 단어 안에서 영문과 한글을 섞을 수 없습니다.** (`abc가`는 에러)
- 밑줄, 하이픈은 없습니다. 여러 단어는 camelCase로 씁니다.
- 아래 예약어는 이름으로 쓸 수 없습니다. 대소문자를 구분하므로 `Vault`처럼 대문자로 시작하는 이름은 안전합니다.

### 2.2 리터럴
| 종류 | 형식 | 예 |
|---|---|---|
| 정수 | 십진수만. 앞자리 0 금지, 최대 78자리 | `0` `42` |
| 단위 정수 | 숫자 뒤에 공백과 단위 | `5 ether` `3 gwei` `7 wei` |
| 주소 | `0x` + 16진수 정확히 40자 (소문자로 정규화) | `0x1111111111111111111111111111111111111111` |
| 텍스트 | `[ ]` 안에 허용 문자만. `[` 와 줄바꿈 불가, 최대 1024자 | `[zero amount]` |
| 불리언 | `true` `false` / `참` `거짓` | |

- 단위는 컴파일 때 정수로 환산됩니다 (`gwei` = 10^9, `ether` = 10^18). 소수점과 float은 없습니다.
- 숫자와 단위 사이에는 반드시 공백이 있어야 합니다 (`5ether`는 에러).
- 텍스트에는 쉼표, 점, 따옴표를 쓸 수 없습니다.

### 2.3 주석
```
note (이 안은 무시됩니다 (괄호는 짝이 맞으면 중첩 가능))
주석 (한글 키워드도 됩니다)
```
- 주석은 `note` 또는 `주석` 뒤에 `( ... )`가 따라오는 형태입니다.
- 주석 안에도 허용 문자만 쓸 수 있습니다. 즉 마침표나 쉼표를 쓸 수 없습니다.
- 일반 `( )`는 식을 묶는 용도로만 씁니다.

---

## 3. 키워드 (영어 / 한국어)

두 언어를 한 파일에서 섞어 써도 됩니다.

| 의미 | English | 한국어 |
|---|---|---|
| 계약 | `contract` | `계약` |
| 저장소 블록 | `storage` | `저장소` |
| 능력 선언 | `capability` | `능력` |
| 공개 | `public` | `공개` |
| 액션 | `action` | `행동` |
| 매개변수 목록 | `taking` | `받음` |
| 타입 지정 | `as` | `형식` |
| 반환 타입 | `returning` | `반환형` |
| 반환 | `return` | `반환` |
| 지역 변수 | `let` | `정의` |
| 대입 | `set` | `설정` |
| 조건 검사 | `require` | `필요` |
| 아니면 | `otherwise` | `아니면` |
| 조건문 | `if` | `만약` |
| 고정 반복 | `repeat` | `반복` |
| 유계 반복 | `while` | `동안` |
| 상한 | `at most` | `최대` |
| 회 | `times` | `번` (또는 `times`) |
| 내부 호출 | `call` | `호출` |
| 능력 호출 | `ask` | `요청` |
| 인자 시작 | `with` | `함께` |
| 논리 | `and` `or` `not` | `그리고` `또는` `아님` |
| 음수 | `negative` | `음수` |
| 산술 | `plus` `minus` `times` `divided by` `modulo` | `더하기` `빼기` `곱하기` `나누기` `나머지` |
| 비율 계산 | `scale` `by` `over` | `비율` `곱` `나눔` |
| 맵 접근 | `at` | `항목` |
| 호출자 | `caller` | `호출자` |
| 참/거짓 | `true` `false` | `참` `거짓` |
| 맵 타입 | `map` `from` `to` | `지도` `키` `값` |
| 선언의 "이다" | `is` | `는` |
| 관사(선택) | `a` `an` | (없음) |
| 비교 | `is` `is not` `is greater than` `is less than` `is at least` `is at most` | `같다` `다르다` `초과` `미만` `이상` `이하` |
| 단위 | `wei` `gwei` `ether` | `웨이` `지웨이` `이더` |
| 주석 | `note` | `주석` |

예약어이기도 한 타입 이름: `bool` `address` `text` `amount` `u8`~`u256` `i8`~`i256`, 한글 `불` `주소` `글` `금액`.

> 이름으로 자주 충돌하는 단어: `a`, `an`, `to`, `from`, `by`, `at`, `over`, `scale`, `caller`, `set`, `gwei`, `ether`. 매개변수 이름으로 `to` 대신 `dest`를 쓰는 식으로 피하세요.

---

## 4. 타입

| 타입 | 설명 |
|---|---|
| `u8` `u16` … `u256` | 부호 없는 정수. 8의 배수 비트 |
| `i8` … `i256` | 부호 있는 정수 |
| `amount` | `u256`의 별칭 |
| `bool` | 참/거짓 |
| `address` | 주소 |
| `text` | 텍스트 |
| `map from K to V` | 맵. **저장소에서만** 선언·사용 가능 |

- 맵의 키는 맵이 아닌 타입이어야 합니다. 값은 맵도 가능합니다 (중첩 맵).
- 맵은 값처럼 복사하거나 매개변수·반환값·`let`에 쓸 수 없습니다. 순회도 불가합니다.
- 없는 키를 읽으면 0, `false`, 영 주소, 빈 텍스트가 나옵니다.
- 저장소 필드는 처음에 모두 0 값입니다.

---

## 5. 선언

### 5.1 contract
```
contract 이름 { 멤버 ... }
```
멤버는 `storage` 블록, `capability`, `action`을 순서 무관하게 쓸 수 있습니다. 액션끼리는 뒤에 정의된 것도 호출할 수 있습니다.

### 5.2 storage
```
storage {
  owner is an address;
  balances is a map from address to amount;
  nested is a map from address to map from u8 to bool;
}
```
형식은 `이름 is [a|an] 타입;` 입니다. 한글은 `이름 는 타입;` 입니다.

### 5.3 capability (외부 능력)
```
capability transfer taking dest as address and sum as amount;
capability price returning amount;
```
- 호스트(실행 환경)가 구현하는 외부 기능의 이름과 타입을 선언합니다.
- 호스트가 허용하지 않은 능력을 호출하면 되돌려집니다.
- 개인키는 언어에 없습니다. 서명도 호스트가 정책을 적용해 처리하는 능력일 뿐입니다.

### 5.4 action
```
[public] action 이름 [taking 이름 as 타입 and 이름 as 타입 ...] [returning 타입] { ... }
```
- `public` 액션만 외부에서 호출할 수 있습니다. 나머지는 내부 액션입니다.
- 반환 타입이 있으면 **모든 경로에서** `return 값;`으로 끝나야 합니다.
- 매개변수와 지역 변수는 저장소 필드와 같은 이름을 쓸 수 없습니다.
- 여러 매개변수는 `and`로 구분합니다. (쉼표 없음)

---

## 6. 문장

| 문장 | 형식 | 설명 |
|---|---|---|
| let | `let x = 식;` | 지역 변수. 타입은 식에서 추론. 같은 이름 재선언·가림 금지 |
| set | `set 대상 = 식;` | 지역 변수·저장소 필드·맵 항목에 대입 |
| require | `require 조건 otherwise [메시지];` | 거짓이면 전체를 되돌림 |
| if | `if 조건 { } otherwise if 조건 { } otherwise { }` | 조건문 |
| repeat | `repeat N times { }` | N번 반복. N은 숫자 리터럴, 최대 10000 |
| while | `while 조건 at most N times { }` | 조건이 참인 동안 반복. 상한 N번을 넘으면 되돌림 |
| return | `return 식;` 또는 `return;` | 액션 종료 |
| call / ask | `call 이름 with 식 and 식;` | 값을 쓰지 않는 호출 |

- `set` 대상 예: `total`, `balances at caller`, `nested at x at y`
- `return` 뒤에는 문장을 쓸 수 없습니다 (도달 불가 코드는 에러).
- `if`의 `otherwise` 블록은 생략할 수 있습니다.

---

## 7. 식과 연산자

### 7.1 우선순위 (낮음 → 높음)
1. `or`
2. `and`
3. `not`
4. 비교 (`is`, `is not`, `is greater than`, `is less than`, `is at least`, `is at most`). **한 식에 하나만** 쓸 수 있고 연쇄할 수 없습니다.
5. `plus` `minus`
6. `times` `divided by` `modulo`
7. `negative`
8. `at` (맵 접근, 왼쪽부터 결합)
9. 괄호 `( )`, 리터럴, 변수, `call`, `ask`, `scale`

`not a is b`는 `not (a is b)`로 읽힙니다.

### 7.2 산술
- 두 피연산자는 **같은 정수 타입**이어야 합니다. 리터럴은 상대 타입에 맞춰집니다.
  - `set n = n plus 1;`에서 `n`이 `u8`이면 `1`도 `u8`입니다.
  - 타입이 정해지지 않으면 `u256`, `negative` 리터럴은 `i256`이 기본입니다.
- 모든 연산은 **범위를 검사**합니다. 넘치면 에러로 되돌립니다. (조용한 wrap-around 없음)
- `divided by`는 0 방향으로 내림, `modulo`의 부호는 왼쪽 값을 따릅니다. 0으로 나누면 에러입니다.
- `negative`는 부호 있는 타입에서만 쓸 수 있습니다. 음수 리터럴은 해당 타입의 최솟값(예: i8의 -128)까지 쓸 수 있습니다.
- `scale a by b over c`는 a×b÷c입니다. 중간 곱을 정밀도 손실 없이 계산한 뒤 0 방향으로 내리고 범위를 검사합니다.

### 7.3 비교와 논리
- `is`, `is not`은 정수, bool, address, text에 쓸 수 있습니다.
- 대소 비교는 정수만 가능합니다.
- `and`, `or`, `not`은 bool에만 씁니다. `and`, `or`는 앞쪽이 결정되면 뒤쪽을 계산하지 않습니다.

### 7.4 맵 접근과 호출자
```
balances at who
nested at a at b
caller
```
- `caller`는 현재 액션을 호출한 주소입니다.

### 7.5 호출
```
call helper with a and b
ask transfer with caller and amt
```
- 인자 목록은 `with`로 시작하고 `and`로 구분합니다.
- 각 인자는 비교 수준까지의 식입니다. **논리 `and`를 인자 안에 쓰려면 `( )`로 감싸세요.**
- 값을 반환하는 호출은 식으로도 쓸 수 있습니다: `let r = call helper with 1;`
- 값이 없는 호출은 식으로 쓸 수 없고 문장으로만 씁니다.

---

## 8. 실행 규칙

- **원자성**: 액션이 끝까지 성공하거나 아무 변화가 없거나 둘 중 하나입니다. 에러, `require` 실패, 가스 소진, 호스트 거부 시 저장소가 호출 전 상태로 복구됩니다.
- **재귀 금지**: 직접·간접 재귀는 컴파일 에러입니다.
- **가스**: 모든 연산이 가스를 소모합니다. `forwardy check`가 액션별 최악 가스를 정적으로 계산해 보여 줍니다.

  | 항목 | 가스 |
  |---|---|
  | 문장·식 노드 | 1 |
  | 저장소 값 읽기(맵 포함) | +50 |
  | 저장소 쓰기 | +200 |
  | 내부 호출 | +5 |
  | 능력 호출 | +1000 |

- **재진입 차단**: 능력 처리 중에 호스트가 같은 컨트랙트를 다시 호출하면 거부됩니다.
- **결정론**: float, 시간, 난수, 맵 순회, 파일, 네트워크가 언어에 없습니다. 같은 입력이면 항상 같은 결과가 나옵니다.
- **한도**: 소스 64KB, 토큰 5만, 중첩 깊이 64, 텍스트 1024자, 저장 항목 10만, 호출 깊이 32.

---

## 9. 자주 나오는 에러

| 메시지 | 원인 |
|---|---|
| `character ',' (U+002C) is not allowed` | 허용되지 않은 기호 사용 |
| `a word may not mix Latin and Hangul` | 영문과 한글을 한 단어에 섞음 |
| `a number must be followed by a space` | `5ether`처럼 단위를 붙여 씀 |
| `expected an action name but found 'a'` | 예약어를 이름으로 사용 |
| `type mismatch: u8 and u256` | 서로 다른 정수 타입끼리 연산 |
| `literal 256 does not fit in u8` | 리터럴이 타입 범위를 넘음 |
| `recursion is not allowed` | 재귀 호출 |
| `loop bound of N exceeded` | `while` 상한 초과 (실행 시) |
| `integer overflow in u8` | 산술 결과가 범위를 넘음 (실행 시) |
| `require failed: ...` | `require` 조건이 거짓 (실행 시) |
| `out of gas` | 가스 한도 초과 (실행 시) |

---

## 10. 실행 방법

```
forwardy check 파일.fw
forwardy run [-caller 0x..] [-gas N] [-mock] 파일.fw 액션 [인자...]
forwardy version
```
- `check`는 문법과 타입을 검사하고 액션별 최악 가스를 출력합니다.
- `run`은 매번 빈 저장소에서 시작하는 개발용 실행기입니다.
- `-mock`은 모든 능력을 허용하고 0 값을 돌려줍니다. 이 옵션이 없으면 `ask`는 거부됩니다.
- 인자는 매개변수 타입에 맞춰 해석됩니다: 정수는 십진수, bool은 `true`/`false`, 주소는 `0x…`, 텍스트는 그대로.
