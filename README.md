# pwd-week3 · TypeScript 기본 계산기

HTML·CSS·TypeScript로 만든 기본 계산기입니다. TypeScript 소스(`*.ts`)를 컴파일해 `js/` 폴더의 JavaScript를 브라우저에서 실행합니다.

- 배포 주소: https://kyurimyang.github.io/pwd-week3/

## 실행 방법

```bash
npm install
npm run check   # 타입 검사
npm run build   # js/ 폴더에 JavaScript 생성
```

빌드 후 `index.html`을 브라우저로 엽니다. `.ts`를 수정하면 다시 `npm run build` 후 새로고침합니다.

## 파일 구성

| 파일 | 역할 |
| --- | --- |
| `index.html` | 표시 영역(`#expression`, `#display`, `#message`)과 `data-key` 버튼 20개 |
| `styles.css` | Grid 4열 배치, 원형 버튼, 연산자 주황색 |
| `operations.ts` | 사칙연산 함수(`add`, `subtract`, `multiply`, `divide`)와 `calculate()` |
| `calculator.ts` | 계산 상태 `state`와 입력 처리(`inputDigit`, `selectOperator`, `equals`, `handleKey`) |
| `app.ts` | 버튼 클릭 이벤트 연결과 화면 갱신(`render`, `formatDisplay`) |

## 동작 확인 결과

| 입력 | 결과 | 담당 코드 |
| --- | --- | --- |
| 12 + 3 = | 15 (계산식 `12 + 3 =`) | `operations.ts`의 `add`, `calculator.ts`의 `equals`·`pendingExpression` |
| 12 − 3 = / 12 × 3 = / 12 ÷ 3 = | 9 / 36 / 4 | `operations.ts`의 `subtract`·`multiply`·`divide` |
| 0.1 + 0.2 = | 0.3 | `calculator.ts`의 `formatNumber` (`toPrecision(12)`로 오차 정리) |
| 123 ⌫ | 12 | `calculator.ts`의 `handleKey` (`delete`) |
| 5 +/− | -5 | `calculator.ts`의 `handleKey` (`sign`) |
| 50 % | 0.5 | `calculator.ts`의 `handleKey` (`percent`) → `setResult` |
| 6110000 | 6,110,000 | `app.ts`의 `formatDisplay` |
| 2 + 3 × 4 = | 20 (입력 순서대로 계산) | `calculator.ts`의 `selectOperator` |
| 2 + × 3 = | 6 (마지막 연산자 적용) | `calculator.ts`의 `selectOperator` (`hasOperand` 검사) |
| 200 + 10 % = | 200.1 | `percent` → `equals` |
| 2 + = | 2 유지 | `equals`의 `hasOperand` 검사 |
| 2 + 3 = = | 5 유지 | `equals` 후 `operator`가 `null`이 되어 반복 계산 없음 |
| 12 ÷ 0 = | Error, "0으로 나눌 수 없습니다." | `divide`에서 오류 발생 → `handleKey`의 `catch` → `state.error` → `render` |
| (오류 후) 7 | 7, 오류 설명 사라짐 | `inputDigit`에서 `clear()` 호출 |

## 관찰한 점

- 컴파일 후 `type`, `interface`, 매개변수 타입 표기는 사라지고 `const add = (left, right) => left + right`처럼 실제 동작만 남습니다.
- `if (right === 0)` 같은 실행 중 검사는 JavaScript에도 그대로 남습니다.
- 세 스크립트는 전역 범위를 공유하므로 `index.html`의 로딩 순서(operations → calculator → app)가 중요합니다.
