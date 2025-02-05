# 27장 배열

## 배열과 일반 객체 차이점

|  구분   |           객체           |     배열     |
| :-----: | :----------------------: | :----------: |
|  구조   | 프로퍼티 키, 프로퍼티 값 | 인덱스, 요소 |
| 값 참조 |       프로퍼티 키        |    인덱스    |
| 값 순서 |            x             |      o       |
| length  |            x             |      o       |

- 인덱스, length 프로퍼티를 통해 정방향, 역방향, 특정 위치부터 접근이 가능해졌다.

---

## JS배열은 해시 테이블이다.

자바스크립트에서의 배열은 객체이다. 각 요소는 프로퍼티 값으로 **모든 값은 객체의 프로퍼티 값**이 될 수 있어 **모든 타입의 값도 배열의 요소**가 될 수 있다.

\* JS의 배열은 해시테이블로 구현되어 있다.

| 구분 |          일반 배열           |             JS의 배열              |
| :--: | :--------------------------: | :--------------------------------: |
| 장점 | 인덱스로 빠르게 값 접근 가능 | 검색, 삽입, 삭제가 상대적으로 빠름 |
| 단점 | 검색, 삽입, 삭제가 비효율적  |      인덱스로 접근시 비효율적      |

> **밀집 배열**
> 동일한 타입의 요소가 서로 연속적으로 접해있는 배열 <br>
> **희소 배열**
> 메모리 공간이 일정하지 않으며, 연속적이지도 않은 배열.
> length 프로퍼티를 통해 공간을 만들 경우 특정 구역의 요소가 빈다면 이는 희소배열이 됨.

## new Array - 생성자 함수

- `new Array(n)`은 n개의 빈 값이 존재한다.
  - 이 경우 length는 n을 반환해주지만 실제로는 요소가 존재하지 않아 빈 배열과 같은 상황이 된다.
- 전달한 인수가 없다면 빈 배열을 반환한다.
  - 암묵적인 데이터 공간을 확보한 `new Array(n)`과 달리 정말 비어있는 배열을 생성한 꼴
- new 연산자와 함께 호출하지 않아도 생성자 함수로 동작한다.
  - Array 생성자 함수 내부에 *new.target*을 확인하고 있음
- 전달 한 인수가 2개 이상일 경우 해당 인수를 요소로 받는다
- 전달 한 인수가 1개지만, 숫자가 아닐 경우 해당 요소로 받는 배열을 갖는다.

## 배열의 추가, 삭제시 주의 사항

- 배열 추가시 정수(혹은 정수 형태의 문자열)이 아닐 경우 프로퍼티가 생성된다. length에 영향을 주지 않아 정확한 길이를 알 수 없어진다.

```js
const arr = [];
arr[0] = 1;
arr['1'] = 2;
arr['foo'] = 3;
arr.bar = 4;
arr[1.1] = 5;
arr[-1] = 6;

console.log(arr);
// [1, 2, foo: 3, bar: 4, '1.1': 5, '-1': 6]
```

- 객체이기 때문에 delete 연산자 사용이 가능하지만 희소 배열로 만들게 되어 사용하지 않는 것을 권장한다.
  - 희소 배열이 되지 않으며, 요소를 완전히 삭제하려면 splice 메서드를 권장한다.

```js
const arr = [1, 2, foo: 3, bar: 4, '1.1': 5, '-1': 6];

delete arr[3];
console.log(arr); //[1, 2, foo: 3, undefined, '1.1': 5, '-1': 6]

arr.splice(2, 3);
console.log(arr); //[1, 2, '-1': 6]
```

## ES6이후 추가된 메서드

- `Array.of`
  전달된 인수를 요소로 갖는 배열을 말한다. 인수가 숫자이며 1개일 경우에도 해당 인수를 요소로 받고 싶다면 사용.

```js
// 기존 상황
const arr1 = new Array(5);
console.log(arr1); // [undefined, undefined, undefined, undefined, undefined]

//Array.of
const arr2 = Array.of(5);
console.log(arr2); // [5]
```

- `Array.from`
  유사 배열 객체, 이터러블 객체를 인수로 전달 받아 배열로 변환하여 반환해준다. 두번째 인수는 콜백함수를 통해 값을 만들며 요소를 채울 수 있게 된다.

```js
// 유사 배열 객체 사용
Array.from({ length: 2, 0: 'a', 1: 'b' }); // ['a', 'b']
Array.from({ length: 3 }); // [undefined, undefined, undefined]
Array.from({ length: 3 }, (_, i) => i); // [0, 1, 2]

// 이터러블 객체 사용
Array.from('Hello'); // ['H', 'e', 'l', 'l', 'o']
```

세 번째의 경우, 두 번째 인수로 전달한 콜백 함수가 인덱스 번호를 반환하며 인덱스 번호를 배열의 요소 값으로 사용하게 됐다.

> **유사 배열 객체:** 인덱스로 프로퍼티 값에 접근이 가능하며 length 프로퍼티를 갖는 객체. for문 사용 가능.<br>
> **이터러블 객체:** 스프레드 문법, 배열 디스트럭처링 할당의 대상으로 사용 가능한 객체. for of문 사용 가능

- `Array.prototype.fill`
  모든 요소를 같은 요소로 채울 때 사용. 다른 값으로 배열을 채우려면 `from`메서드를 사용할 것.
- `Array.prototype.includes`
  포함 여부를 찾는 메서드. `indexOf`메서드와 유사하지만, 반환 값이 -1인지, 배열 내 NaN이 포함 되어있는지 확인하기가 어렵기 때문에 includes를 추천한다.
- `Array.prototype.flat`
  중첩하여 사용할 수 있으나 n번 연결하지 않고 flat(n)으로 작성하는 것이 가독성이 좋다.
- `Array.prototype.find`
  콜백 함수의 조건을 처음으로 true가 되는 요소를 찾는 메서드. 전체를 찾는다면 filter를 사용하는 것이 좋음.
- `Array.prototype.findIndex`
  콜백 함수의 조건을 처음으로 true가 되는 인덱스 번호를 찾는 메서드. find는 반환 값이 요소 값이라면 findIndex는 인덱스 번호를 반환한다.
- `Array.prototype.flatMap`
  map과 flat 메서드를 순차적으로 실행하는 효과를 보인다. 여러 단계를 평탄화 할 경우에는 적합하지 않음.

## 배열 메서드 특징

배열 메서드의 this가 가리키는 객체를 직접 변경하는 메서드와, 새로운 배열을 생성하여 반환하는 메서드가 존재한다. <br>
초창기 배열 메서드는 원본 배열을 직접 변경하는 경우가 많았고, ES5 이후 도입된 배열 메서드는 대부분 배열을 직접 변경하지는 않는다. <br>
가급적 원본 배열을 수정하지 않는 메서드를 사용하는 것을 권장하고 있다. (외부 상태 변경 방지)

## 배열 메서드 정리

| 메서드                      | 설명                                                                                                                                                                                                                                                                                                                                                                                                           |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| isArray(value)              | 전달된 인수가 배열이라면 `true`, 배열이 아니라면 `false`를 반환.                                                                                                                                                                                                                                                                                                                                                   |
| indexOf(value)              | 배열에 value가 존재하는지 확인시 사용. <br> 값이 있다면 `처음 검색된 인덱스 값`을, 없다면 `-1`를 반환한다.                                                                                                                                                                                                                                                                                                         |
| push(value)                 | 전달된 인수를 마지막 요소에 추가하고 변경된 length 값을 반환. <br> 성능도 좋지 않으며, 원본 값을 변경하기 때문에 `스프레드 문법`을 통해 직접 추가하는 것을 추천.                                                                                                                                                                                                                                                 |
| pop()                       | 마지막 요소를 제거하고 `제거된 요소`를 반환. (원본 배열 수정, 빈 배열일 경우 undefined)                                                                                                                                                                                                                                                                                                                          |
| unshift(value)              | 전달된 인수를 맨 앞에 추가하고 변경된 length 값을 반환 <br> 원본 배열을 직접 수정하기 때문에 `스프레드 문법`을 이용하는 것이 좋다                                                                                                                                                                                                                                                                                |
| shift()                     | 배열의 첫번째 요소를 제거하고 `제거된 요소`를 반환. (원본 배열 수정, 빈 배열의 경우 undefined)                                                                                                                                                                                                                                                                                                                   |
| concat(value)               | 원본 배열에 전달된 값(배열, 원시값)을 마지막 요소로 추가하여 `새로운 배열`로 반환한다. <br> 원본 배열은 변경되지 않아 변수에 반환값을 할당 받아야 한다. (스프레드 문법으로 대체 가능)                                                                                                                                                                                                                            |
| splice(start, count, items) | start 인덱스 값부터 count의 개수만큼 제거하여 제거한 위치에 items를 추가할 수 있는 메서드. <br> start는 필수 값이며 -1은 마지막 요소 -n은 마지막에서 n번째 요소를 의미한다. <br> count의 인수가 0 혹은 생략할 경우 start 이후 인덱스의 값을 모두 제거한다. <br> items를 생략하면 요소 제거만 수행하게 된다. <br> \* 특정 요소를 골라 제거할 경우 indexOf or filter로 인덱스 값을 받아와서 삭제하는 방법도 존재 |
| slice(start, end)           | 시작 값부터 종료 값까지의 요소를 복사하여 배열로 반환해준다. <br> `start` 인수가 -1이라면 마지막 값을 복사하며, -n은 끝에서 n개를 복사한다. <br> `end` 인수는 옵션으로 생략시 start이후 모든 값을 복사하여 반환한다. <br> 모두 생략할 경우 원본 배열을 얕은 복사하게 된다.                                                                                                                                         |
| join(\_)                    | 모든 요소를 문자열로 변환하여 인수로 전달 받은 문자열을 기준으로 연결한다. 기본 값은 콤마(,)                                                                                                                                                                                                                                                                                                                   |
| reverse()                   | 원본 배열의 순서를 반대로 뒤집는다. 원본 배열이 변경되는 메서드다.                                                                                                                                                                                                                                                                                                                                             |
| fill(\_,start, end)         | 인수로 전달 받은 값을 배열의 처음부터 끝까지 채운다. 원본 배열이 수정되는 메서드. <br> 두번째 인수는 요소를 채우기 시작할 인덱스 번호, <br> 세번째 인수는 요소 채우기를 멈출 인덱스 번호를 전달한다. <br> \* 두번째, 세번째 인수는 선택사항                                                                                                                                                                         |
| includes(x, start)          | 전달받은 값이 배열 내 포함되어있는지를 `true/false`로 반환한다.<br> 두번째 인수는 검색을 시작할 인덱스로 사용할 수 있는 옵션 값.                                                                                                                                                                                                                                                                                 |
| flat(n)                     | 인수로 전달한 깊이만큼 재귀적으로 배열을 평탄화 할 수 있다.<br> 중첩 배열을 평탄화할 수 있으며 기본 값은 `1`. 값으로 `Infinity`을 전달 받는다면 모든 배열을 평탄화 할 수 있다.                                                                                                                                                                                                                                 |

## 고차함수

인수를 함수로 받거나, 함수를 반환하는 함수. 외부 상태의 변경이나 가변 데이터를 피하고 불변성을 지향한다.

| 메서드                                             | 설명                                                                                                                                                                                                                                                                                                                                                                                                                    |
| -------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sort(arr)                                          | 배열의 요소를 정렬하며 원본 배열을 직접 변경한다. <br> 기본적으로 아스키코드 기준의 오름차순 정렬. 숫자 정렬시 정렬 순서를 정의하는 비교함수를 인수로 전달할 것. <br> 비교 연산 뒤, `연산 값이 음수일 경우 우선 정렬`.                                                                                                                                                                                                    |
| forEach(arr)                                       | for문을 대체할 수 있는 고차 함수. forEach `메서드 내부에서 반복문을 실행`하여 자신을 반복 호출한다. <br> 호출한 배열의 요소, 인덱스, this(원본 배열)를 순차적으로 전달하고 있는 것이다. 원본 배열을 변경하지 않으나 콜백 함수를 사용하여 원본 배열을 변경할 수도 있다. <br> for문과 달리 break, continue를 사용할 수 없어 모든 요소를 순회하지만 존재하지 않는 값은 순회 대상에서 제외된다. <br> 항상 반환값은 undefined. |
| map(arr)                                           | 자신을 호출한 배열의 모든 요소를 순회하며 인수로 받은 콜백 함수를 반복 호출, `새로운 배열을 반환`한다. 원본 배열을 변경하지 않음. <br> forEach와 다른 점은 map 메서드는 반환 값이 새로운 배열이라는 점. 같은 점은 콜백 함수 호출시 요소값, 인덱스, 호출 배열(this)을 전달한다는 점이다.                                                                                                                                   |
| filter(arr)                                        | 전달받은 콜백 함수를 반복호출 하며 `반환값이 true인 요소`만 모아 새로운 배열로 반환한다. 원본 배열은 변경되지 않는 메서드. 모든 값을 반환하는 map과 달리 조건에 성립해야만 반환한다는 점이 다르다.<br> filter 역시 콜백함수 호출시 요소, 인덱스, this를 전달한다.                                                                                                                                                         |
| reduce(callback, init)                             | 배열 내 모든 요소를 순회하며 초기값과 배열의 첫번째 요소 값을 콜백함수에 전달, 다음 순회시 이전 콜백 함수 반환 값과 두번째 요소를 전달하며 `계속 자신을 콜백함수의 인수로 활용`하는 메서드이다. 원본 배열은 수정하지 않음. <br> 콜백 함수의 인수 값으로는 초기값, 현재값, 인덱스, 호출 배열을 전달한다. <br> 초기값은 옵션이지만 생략하지 않는 것이 안전하다.                                                             |
| some(callback)                                     | 자신을 호출한 배열을 순회하며 인수로 전달된 콜백함수를 호출. 반환 값이 `한번이라도 true`라면 true를 반환한다. 콜백 함수에서는 요소 값, 인덱스, this를 전달한다.                                                                                                                                                                                                                                                           |
| every(callback)                                    | 자신을 호출한 배열을 순회하며 인수로 전달 된 콜백 함수를 호출. `모든 반환 값이 true`여야 true를 반환한다. 콜백 함수에서는 요소 값, 인덱스, this를 전달한다.                                                                                                                                                                                                                                                               |
| find(callback)                                     | 자신을 호출한 배열을 순회하며 인수로 전달된 콜백함수를 호출. `반환 값이 true인 첫번째 요소`를 반환. 모두 false일 경우 undefined를 반환한다. 콜백 함수에서는 요소 값, 인덱스, this를 전달한다. filter와 달리 첫번째 요소를 반환하기 때문에 결과 값은 요소 값이다.                                                                                                                                                          |
| findIndex(callback)                                | 자신을 호출한 배열을 순회하며 인수로 전달된 콜백함수를 호출. `반환 값이 true인 첫번째 요소의 인덱스 값`을 반환. 모두 false일 경우 -1를 반환한다. 콜백 함수에서는 요소 값, 인덱스, this를 전달한다.                                                                                                                                                                                                                        |
| flatMap(callback)                                  | map메서드를 통해 생성된 `새로운 배열의 평탄화`를 진행한다. map과 flat를 순차적으로 진행하는 것과 동일. <br> flat메서드와 달리 깊이를 지정할 수 없고 `1단계만 평탄화` 할 수 있다. 여러 단계 진행을 원할 경우 map과 flat을 따로 사용하는 것을 권장한다.                                                                                                                                                                       |
| `forEach, map, filter, some, every, find, findIndex` | 위 메서드는 콜백 함수를 전달한다. 이 때 요소 값, 인덱스, 메서드를 호출한 배열(this)를 전달하는데 화살표 함수를 이용하면 함수 자체의 this 바인딩을 갖게 되어 더 가독성 있게 사용할 수 있다.                                                                                                                                                                                                                              |
