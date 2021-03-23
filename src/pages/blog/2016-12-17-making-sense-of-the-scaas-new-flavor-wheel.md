---
templateKey: blog-post
title: JavaScript ES6 Symbol properties
date: 2016-12-17T15:04:10.000Z
description: The Coffee Taster’s Flavor Wheel, the official resource used by
  coffee tasters, has been revised for the first time this year.
featuredpost: false
featuredimage: /img/products-full-width.jpg
tags:
  - flavor
  - tasting
---
![flavor wheel](/img/blog-index.jpg)

Symbol 프로퍼티는 자바스크립트 엔진이 디폴트로 처리하는 알고리즘 유형들입니다. 개발자는 이 알고리즘에 개입해서 엔진의 동작을 수정할 수 있습니다.

### **Symbol?**

ECMAScript6 에 Symbol이라는 타입이 추가되었습니다. `Symbol()` 명령어로 생성한 값은 중복되지 않는 고유한 값을 리턴합니다. 이를 객체의 프로퍼티 key으로 사용하여, 중복되지 않는 key를 생성할 수 있습니다.Symbol에 대한 자세한 설명은 [mozilla 블로그](http://hacks.mozilla.or.kr/2015/09/es6-in-depth-symbols/)에 잘 나와 있으니 참고하시기 바랍니다.

### **Symbol 프로퍼티**

앞서 이야기 한 것처럼 Symbol 프로퍼티는 자바스크립트 엔진이 디폴트로 처리하는 알고리즘 유형(`well-known Symbol`)의 이름들입니다. 자바스크립트 스펙에서 @@iterator 형태로 작성 됩니다.같은 이름의 Symbol 작성하면 엔진의 디폴트 처리 대신 프로그램에 작성된 코드를 **오버라이드** 해서 실행합니다. Symbol의 종류는 다음과 같습니다.

### **이터레이터(반복) 심볼**

* `Symbol.iterator` 객체의 기본 반복자(iterator)를 반환하는 메소드. for…of에서 사용됩니다. ECMAScript6의 대부분 스팩은 이터레이터와 연관이 있습니다. 따라서 이터레이터는 별도 포스트로 다룰 예정입니다.

### **정규표현식 심볼**

* `Symbol.match`: 문자열과 매치(match)되는 메소드, 또한 객체가 정규식으로서 사용되는지 확인하는데도 사용. String.prototype.match()에서 사용됩니다.
* `Symbol.replace`: 문자열 중 매치되는 일부 문자열을 대체하는 메소드. String.prototype.replace()에서 사용됩니다.
* `Symbol.search`: 정규식과 매치되는 문자열의 인덱스(index)를 반환하는 메소드. String.prototype.search()에서 사용됩니다.
* `Symbol.split`: 문자열을 정규식과 매치되는 인덱스들에서 나누는 메소드. String.prototype.split()에서 사용됩니다.

### **그 외 심볼들**

### **toStringTag**

`[object Object]` 형태에서 `Object`를 `Symbol.toStringTag` 값으로 표시합니다.`Symbol.toStringTag`를 사용하여 `[object Object]`의 `Object`에 표시될 문자열을 지정할 수 있습니다.

```jsx
let Sports = function(){};
let sportsObj = new Sports;
console.log(sportsObj.toString()); //[object Object]

Sports.prototype[Symbol.toStringTag] = "Sports-Function";
console.log(sportsObj.toString()); //[object Sports-Function]
```

### **isConcatSpreadable**

Array 오프젝트의 concat()에서 배열을 결합할 때 결합하는 배열의 펼침 여부를 지정합니다. 디폴트는 true입니다.

```javascript
let one = [11, 12], two = [21, 22];
let result = one.concat(two);
console.log(result, result.length); //[11, 12, 21, 22] 4

two[Symbol.isConcatSpreadable] = false;
result = one.concat(two);
console.log(result, result.length); //[11, 12, Array(2)] 3

two[Symbol.isConcatSpreadable] = true;
result = one.concat(two);
console.log(result, result.length); //[11, 12, 21, 22] 4
```

유사배열 객체에서도 사용할 수 있습니다. 디폴트는 false입니다.

```javascript
let one = [11, 12];
let fiveSix = {
  0: "five",
  1: "six",
  length: 2
}; //유사배열
let result = one.concat(fiveSix);
console.log(result, result.length); //[11, 12, Object] 3

fiveSix[Symbol.isConcatSpreadable] = true;
result = one.concat(fiveSix);
console.log(result, result.length); //[11, 12, "five", "six"] 4
```

### **unscopables**

with문에서 사용하며, 값이 `true`이면 프로퍼티를 전개하지 않습니다.

with란? with 이후에 오는 구문을 위해 scope chain에 인자로 받는 object를 추가합니다. with의 인자로 받는 object는 그 이후의 statement나 블럭 {} 안에 scope chain이 추가되어서 실행이 가능합니다.

```javascript
let obj = {
	name:'owen',
  gender:'male'
}

with(obj){
	console.log(gender); //male
}
```

unscopables를 사용해보면…

```javascript
let obj = {
	name:'owen',
  gender:'male'
};
obj[Symbol.unscopables] = {
	gender: true
};
with(obj){
	console.log(gender);
};
//Error: gender is not defined
```

### **species**

`Symbol.species`는 `constructor`를 반환합니다. `constructor`를 반환한다는 것은 `constructor`로 인스턴스를 생성하여 변환하는 것과 같습니다. 이를 오버라이드 함으로써 개발자 코드로 반환되는 인스턴스를 변경할 수 있습니다.

```javascript
class ExtendArray extends Array {
  getValue(){}
}
let newArray = new ExtendArray(1, 2, 3);
let newInst = newArray.slice(1, 2);
console.log(newInst instanceof ExtendArray); // true
```

인스턴스의 매서드를 호출했을 때 인스턴스를 반환하도록 하는 것이 `Symbol.species`입니다.

```javascript
class ExtendArray extends Array {
  static get [Symbol.species]() {
    return Array;
  }
};
let oneInstance = new ExtendArray(1, 2, 3);
let twoInstance = oneInstance.slice(1, 2);
console.log(oneInstance instanceof ExtendArray); //true
console.log(twoInstance instanceof Array); //true
console.log(twoInstance instanceof ExtendArray); //false
```

위 예제에서는 상속받은 Array 생성자를 `Symbol.species`로 설정했지만, 전혀 다른 Class를 설정 할 수도 있습니다.또한 `[Symbol.species]()` 가 `null`을 반환하면 디폴트 `[Symbol.species]()`가 호출됩니다.

### **toPrimitive**

오브젝트를 프리미티브 타입으로 변환합니다.

자바스크립트의 프리미티브 값은 number, string, boolean, undefined, null, Symbol입니다.연산 대상이 Number가 아닐 경우 toPrimitive모듈을 기준으로 값을 변환합니다.

```javascript
let obj = {
  [Symbol.toPrimitive](hint){
    if (hint === "number"){
      return 30;
    };
    if (hint === "string"){
      return "문자열";
    };
    return "디폴트";
  }
};
console.log("1:", 20 + obj);//1: 20디폴트
console.log("2:", 20 * obj);//2: 600
console.log("3:", obj + 50);//3: 디폴트50
console.log("4:", +obj + 50);//4: 80
console.log("5:", `${obj}` + 123);//5: 문자열123
```

### **참조**

* ECMAScript6책\[김영보 지음]: <http://www.rubypaper.co.kr/entry/ECMAScript6>
* HACKS-웹기술블로그: <http://hacks.mozilla.or.kr/2015/09/es6-in-depth-symbols/>