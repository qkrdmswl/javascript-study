# 자바스크립트

**<모던 자바스크립트 Deep Dive>**

**JS**

- **9. 타입변환**

    타입변환이란?

    - 명시적 타입 변환(타입 캐스팅) : 개발자에 의해 의도적으로 값의 타입 변환
    - 암묵적 타입 변환(타입 강제 변환)  : 자바스크립트 엔진에 의해 암묵적으로 타입이 자동 변환

    ```jsx
    var x = 10;

    // 명시적 타입 변환
    var str = x.toString(); // 숫자를 문자열로 타입 캐스팅한다.
    console.log(typeof str); // string
    ```

    ```jsx
    var x = 10;

    // 암묵적 타입 변환
    // 숫자 타입 x의 값을 바탕으로 새로운 문자열 타입의 값을 생성해 표현식을 평가한다.
    var str = x + '';

    console.log(typeof str, str); // string 10

    // 변수 x의 값이 변경된 것은 아니다.
    console.log(x); // 10
    ```

    명시적 타입 변환은 타입을 변경하겠다는 개발자의 의지가 코드에 명백히 드러난다. 하지만 암묵적 타입 강제 변환은 자바스크립트 엔진에 의해 암묵적으로, 즉 드러나지 않게 타입이 자동 변환되기 때문에 타입을 변경하겠다는 개발자의 의지가 코드에 명백히 나타나지 않는다.

    따라서 자신이 작성한 코드에서 암묵적 타입 변환이 발생하는지, 발생한다면 어떤 타입의 어떤 값으로 변환되는지, 그리고 타입 변환된 값으로 표현식은 어떻게 평가될 것인지 **예측 가능해야 한다. 만약 예측하지 못하거나 예측한 내용이 결과와 일치하지 않는다면 버그를 생산할 가능성이 높아진다.**

    그렇다면 **명시적 타입 변환 만을 사용**하고 암묵적 타입 변환은 발생하지 않도록 코드를 작성하면 어떨까? 좋은 생각이긴 하지만 이러한 논리는 옳지 않다. 때로는 명시적 타입 변환보다 **암묵적 타입 변환이 가독성 면에서 더 좋을 수**도 있다. 예를 들어 자바스크립트 문법을 잘 이해하고 있는 개발자에게는 `(10).toString()`보다 `10 + ''`이 더욱 간결하고 이해하기 쉬울 수 있다.

    중요한 것은 코드를 예측할 수 있어야 한다는 것이다. 동료가 작성한 코드를 정확히 이해할 수 있어야 하고 자신의 코드는 타인에 의해 쉽게 이해될 수 있어야 한다. 이를 위해 타입 변환이 어떻게 동작하는지 정확히 이해하고 사용하도록 하자.

    - 암묵적 타입 변환

        자바스크립트 엔진은 표현식을 평가할 때, 컨텍스트를 고려하여 암묵적 타입 변환 

        ```jsx
        // 표현식이 모두 문자열 타입이여야 하는 컨텍스트
        '10' + 2               // '102'
        `1 * 10 = ${ 1 * 10 }` // "1 * 10 = 10"

        // 표현식이 모두 숫자 타입이여야 하는 컨텍스트
        5 * '10' // 50

        // 표현식이 불리언 타입이여야 하는 컨텍스트
        !0 // true
        if (1) { }
        ```

        **2.1 문자열 타입으로 변환**

        ```jsx
        1 + '2' // "12"
        ```

        - 피연산자 중 하나 이상이 문자열 → 문자열 연결 연산자로 동작

        문자열 타입 아닌 값을 문자열 타입으로 암묵적 타입 변환 수행 시, 아래와 같이 동작

        ```jsx
        // 숫자 타입
        0 + ''              // "0"
        -0 + ''             // "0"
        1 + ''              // "1"
        -1 + ''             // "-1"
        NaN + ''            // "NaN"
        Infinity + ''       // "Infinity"
        -Infinity + ''      // "-Infinity"
        // 불리언 타입
        true + ''           // "true"
        false + ''          // "false"
        // null 타입
        null + ''           // "null"
        // undefined 타입
        undefined + ''      // "undefined"
        // 심볼 타입
        (Symbol()) + ''     // TypeError: Cannot convert a Symbol value to a string
        // 객체 타입
        ({}) + ''           // "[object Object]"
        Math + ''           // "[object Math]"
        [] + ''             // ""
        [10, 20] + ''       // "10,20"
        (function(){}) + '' // "function(){}"
        Array + ''          // "function Array() { [native code] }"
        ```

        **2.2 숫자 타입으로 변환**

        ```jsx
        1 - '1'    // 0
        1 * '10'   // 10
        1 / 'one'  // NaN
        ```

        **비교 연산자 있을 때** : 비교 연산자 표현식을 평가하기 위해 숫자 타입이 아닌 피연산자를 숫자 타입으로 변환

        ```jsx
        '1' > 0   // true
        ```

        ```jsx
        // 문자열 타입
        +''             // 0
        +'0'            // 0
        +'1'            // 1
        +'string'       // NaN
        // 불리언 타입
        +true           // 1
        +false          // 0
        // null 타입
        +null           // 0
        // undefined 타입
        +undefined      // NaN
        // 심볼 타입
        +Symbol()       // TypeError: Cannot convert a Symbol value to a number
        // 객체 타입
        +{}             // NaN
        +[]             // 0
        +[10, 20]       // NaN
        +(function(){}) // NaN
        ```

        **2.3 Boolean 타입으로 변환**

        ```jsx
        if ('') console.log(x);
        ```

        if 문이나 for 문과 같은 제어문의 조건식(conditional expression)은 불리언 값, 즉 논리적 참, 거짓을 반환해야 하는 표현식이다. 자바스크립트 엔진은 제어문의 조건식을 평가 결과를 불리언 타입으로 암묵적 타입 변환한다.

        **아래 값들은 false로 평가되는 Falsy 값이다.**

        - false
        - undefined
        - null
        - 0, -0
        - NaN
        - ’’ (빈문자열)

        ```jsx
        if (!false)     console.log(false + ' is falsy value');
        if (!undefined) console.log(undefined + ' is falsy value');
        if (!null)      console.log(null + ' is falsy value');
        if (!0)         console.log(0 + ' is falsy value');
        if (!NaN)       console.log(NaN + ' is falsy value');
        if (!'')        console.log('' + ' is falsy value');
        ```

- **10. 객체**

    객체란?

    ⇒ 키(key)와 값(value)로 구성된 **프로퍼티(property)**들의 집합

    ⇒ 자바스크립트를 이루고 있는 거의 "모든 것"이 객체

    ⇒ (함수, 배열, 정규표현식 등)

    **1.1 프로퍼티**

    키와 값으로 구성되어 있음 / 키로 식별 가능

    - 프로퍼티 키 : 빈 문자열을 포함하는 모든 문자열 또는 symbol 값
    - 프로퍼티 값 : 모든 값

    **1.2 메소드**

    프로퍼티 값이 함수일 경우도 있기 때문에, 일반 함수와 구분하기 위해 이를 메소드라고 부름 ( 즉, 메소드는 객체에 제한되어 있는 함수를 의미)

    1. **객체 생성 방법**

    **2.1 객체 리터럴**

    ```jsx
    var emptyObject = {};
    console.log(typeof emptyObject); // object

    var person = {
      name: 'Lee',
      gender: 'male',
      sayHello: function () {
        console.log('Hi! My name is ' + this.name);
      }
    };

    console.log(typeof person); // object
    console.log(person); // {name: "Lee", gender: "male", sayHello: ƒ}

    person.sayHello(); // Hi! My name is Lee
    ```

    **2.2 Object 생성자 함수**

    ```jsx
    // 빈 객체의 생성
    var person = new Object();
    // 프로퍼티 추가
    person.name = 'Lee';
    person.gender = 'male';
    person.sayHello = function () {
      console.log('Hi! My name is ' + this.name);
    };

    console.log(typeof person); // object
    console.log(person); // {name: "Lee", gender: "male", sayHello: ƒ}

    person.sayHello(); // Hi! My name is Lee
    ```

    **new 연산자와 [Object 생성자 함수](https://poiemaweb.com/js-built-in-object#11-object)**를 호출하여 빈 객체를 생성할 수 있다. 빈 객체 생성 이후 프로퍼티 또는 메소드를 추가하여 객체를 완성하는 방법이다.

    생성자(constructor) 함수란 new 키워드와 함께 객체를 생성하고 초기화하는 함수를 말한다. 생성자 함수를 통해 생성된 객체를 **인스턴스(instance)**라 한다. 자바스크립트는 Object 생성자 함수 이외에도 String, Number, Boolean, Array, Date, RegExp 등의 빌트인 생성자 함수를 제공한다. 일반 함수와 생성자 함수를 구분하기 위해 생성자 함수의 이름은 파스칼 케이스(PascalCase)를 사용하는 것이 일반적이다.

    객체가 소유하고 있지 않은 프로퍼티 키에 값을 할당하면 해당 객체에 프로퍼티를 추가하고 값을 할당한다. 이미 존재하는 프로퍼티 키에 새로운 값을 할당하면 프로퍼티 값은 할당한 값으로 변경된다.

    **2.3 생성자 함수**

    Object 생성자 함수 방식 → 값만 다른 여러 개의 객체를 생성할 때 불편함

    따라서 생성자 함수 사용

    ```jsx
    var person1 = {
      name: 'Lee',
      gender: 'male',
      sayHello: function () {
        console.log('Hi! My name is ' + this.name);
      }
    };

    var person2 = {
      name: 'Kim',
      gender: 'female',
      sayHello: function () {
        console.log('Hi! My name is ' + this.name);
      }
    };
    ```

    이거 대신,

    ```jsx
    // 생성자 함수
    function Person(name, gender) {
      this.name = name;
      this.gender = gender;
      this.sayHello = function(){
        console.log('Hi! My name is ' + this.name);
      };
    }

    // 인스턴스의 생성
    var person1 = new Person('Lee', 'male');
    var person2 = new Person('Kim', 'female');

    console.log('person1: ', typeof person1);
    console.log('person2: ', typeof person2);
    console.log('person1: ', person1);
    console.log('person2: ', person2);

    person1.sayHello();
    person2.sayHello();
    ```

    이런식으로!

    - 생성자 함수 이름은 일반적으로 대문자로 시작한다. 이것은 생성자 함수임을 인식하도록 도움을 준다.
    - 프로퍼티 또는 메소드명 앞에 기술한 `this`는 생성자 함수가 생성할 **인스턴스(instance)**를 가리킨다.
    - this에 연결(바인딩)되어 있는 프로퍼티와 메소드는 `public`(외부에서 참조 가능)하다.
    - 생성자 함수 내에서 선언된 일반 변수는 `private`(외부에서 참조 불가능)하다. 즉, 생성자 함수 내부에서는 자유롭게 접근이 가능하나 외부에서 접근할 수 없다.

    ```jsx
    function Person(name, gender) {
      var married = true;         // private
      this.name = name;           // public
      this.gender = gender;       // public
      this.sayHello = function(){ // public
        console.log('Hi! My name is ' + this.name);
      };
    }

    var person = new Person('Lee', 'male');

    console.log(typeof person); // object
    console.log(person); // Person { name: 'Lee', gender: 'male', sayHello: [Function] }

    console.log(person.gender);  // 'male'
    console.log(person.married); // undefined
    ```

    1. **객체 프로퍼티 접근**

        **3.1 프로퍼티 키**

        프로퍼티 키는 문자열이므로 따옴표(‘’ 또는 ““)를 사용한다.

        **3.2 프로퍼티 값 읽기**

        객체의 프로퍼티 값에 접근하는 방법은 마침표(.) 표기법과 대괄호([]) 표기법이 있다.

        ```jsx
        var person = {
          'first-name': 'Ung-mo',
          'last-name': 'Lee',
          gender: 'male',
          1: 10
        };

        console.log(person);

        console.log(person.first-name);    // NaN: undefined-undefined
        console.log(person[first-name]);   // ReferenceError: first is not defined
        console.log(person['first-name']); // 'Ung-mo'

        console.log(person.gender);    // 'male'
        console.log(person[gender]);   // ReferenceError: gender is not defined
        console.log(person['gender']); // 'male'

        console.log(person['1']); // 10
        console.log(person[1]);   // 10 : person[1] -> person['1']
        console.log(person.1);    // SyntaxError
        ```

    프로퍼티 이름이 유효한 자바스크립트 이름이 아니거나 예약어인 경우 프로퍼티 값은 대괄호 표기법으로 읽어야 한다. 대괄호([]) 표기법을 사용하는 경우, **대괄호 내에 들어가는 프로퍼티 이름은 반드시 문자열이어야 한다.**

    **3.3 프로퍼티 값 갱신**

    객체가 소유하고 있는 프로퍼티에 새로운 값을 할당하면 프로퍼티 값은 갱신된다.

    ```jsx
    var person = {
      'first-name': 'Ung-mo',
      'last-name': 'Lee',
      gender: 'male',
    };

    person['first-name'] = 'Kim';
    console.log(person['first-name'] ); // 'Kim'
    ```

    **3.4 프로퍼티 동적 생성**

    객체가 소유하고 있지 않은 프로퍼티 키에 값을 할당하면 하면 주어진 키와 값으로 프로퍼티를 생성하여 객체에 추가한다.

    ```jsx
    var person = {
      'first-name': 'Ung-mo',
      'last-name': 'Lee',
      gender: 'male',
    };

    person.age = 20;
    console.log(person.age); // 20
    ```

    **3.5 프로퍼티 삭제**

    delete 연산자를 사용하면 객체의 프로퍼티를 삭제할 수 있다. 이때 **피연산자는 프로퍼티 키**이어야 한다.

    ```jsx
    var person = {
      'first-name': 'Ung-mo',
      'last-name': 'Lee',
      gender: 'male',
    };

    delete person.gender;
    console.log(person.gender); // undefined

    delete person;
    console.log(person); // Object {first-name: 'Ung-mo', last-name: 'Lee'}
    ```

    **3.6 for-in 문**

    for-in 문을 사용하면 객체(배열 포함)에 포함된 모든 프로퍼티에 대해 루프를 수행할 수 있다.

    ```jsx
    var person = {
      'first-name': 'Ung-mo',
      'last-name': 'Lee',
      gender: 'male'
    };

    // prop에 객체의 프로퍼티 이름이 반환된다. 단, 순서는 보장되지 않는다.
    for (var prop in person) {
      console.log(prop + ': ' + person[prop]);
    }

    /*
    first-name: Ung-mo
    last-name: Lee
    gender: male
    */

    var array = ['one', 'two'];

    // index에 배열의 경우 인덱스가 반환된다
    for (var index in array) {
      console.log(index + ': ' + array[index]);
    }

    /*
    0: one
    1: two
    */
    ```

    **for-in 문은 객체의 문자열 키(key)를 순회하기 위한 문법**이다. 배열에는 사용하지 않는 것이 좋다. 이유는 아래와 같다.

    1. 객체의 경우, 프로퍼티의 순서가 보장되지 않는다. 그 이유는 원래 **객체의 프로퍼티에는 순서가 없기** 때문이다. 배열은 순서를 보장하는 데이터 구조이지만 객체와 마찬가지로 순서를 보장하지 않는다.
    2. 배열 요소들만을 순회하지 않는다.

    이와 같은 for-in 문의 단점을 극복하기 위해 ES6에서 **for-of** 문이 추가되었다.

    ```jsx
    const array = [1, 2, 3];
    array.name = 'my array';

    for (const value of array) {
      console.log(value);
    }

    /*
    1
    2
    3
    */

    for (const [index, value] of array.entries()) {
      console.log(index, value);
    }

    /*
    0 1
    1 2
    2 3
    */
    ```

    for–in 문은 객체의 프로퍼티를 순회하기 위해 사용하고 for–of 문은 배열의 요소를 순회하기 위해 사용한다.

    1. **Pass-by-reference**

    ```jsx
    // Pass-by-reference
    var foo = {
      val: 10
    }

    var bar = foo;
    console.log(foo.val, bar.val); // 10 10
    console.log(foo === bar);      // true

    bar.val = 20;
    console.log(foo.val, bar.val); // 20 20
    console.log(foo === bar);      // true
    ```

    foo 객체를 **객체 리터럴 방식**으로 생성하였다. 이때 변수 foo는 객체 자체를 저장하고 있는 것이 아니라 생성된 **객체의 참조값(address)를 저장**하고 있다.

    변수 bar에 변수 foo의 값을 할당하였다. 변수 foo의 값은 생성된 객체를 가리키는 참조값이므로 변수 bar에도 같은 참조값이 저장된다. **즉, 변수 foo, bar 모두 동일한 객체를 참조하고 있다. 따라서 참조하고 있는 객체의 val 값이 변경되면 변수 foo, bar 모두 동일한 객체를 참조하고 있으므로 두 변수 모두 변경된 객체의 프로퍼티 값을 참조하게 된다.** 객체는 참조(Reference) 방식으로 전달된다. 결코 복사되지 않는다.

    1. **Pass-by-value**

        ```jsx
        // Pass-by-value
        var a = 1;
        var b = a;

        console.log(a, b);    // 1  1
        console.log(a === b); // true

        a = 10;
        console.log(a, b);    // 1  10
        console.log(a === b); // false
        ```

    변수 a는 원시 타입인 숫자 타입 1을 저장하고 있다. 원시 타입의 경우 값이 복사되어 변수에 저장된다. **즉, 참조 타입으로 저장되는 것이 아니라 값 자체가 저장**되게 된다. 변수 b에 변수 a를 할당할 경우, 변수 a의 값 1은 복사되어 변수 b에 저장된다.

**ES6**

- **1. let, const**

    ### Var?

    ES5까지는 변수 선언 = **var 이용**

    하지만, var 사용시 발생되는 문제점들이 있었음

    **<var 키워드로 선언된 변수의 특징 4>**

    1. 함수 레벨 스코프 (Function-level scope)
        1. 함수의 코드 블록만을 스코프로 인정함. 따라서 전역 함수 외부에서 생성한 변수는 모두 전역 변수이다. → 전역변수 남발할 가능성 다분
        2. for 문의 변수 선언문에서 선언한 변수를 for 문의 코드 블록 외부에서 참조 가능
    2. var 키워드 생략 허용
        1. 암묵적 전역 변수를 양산할 가능성이 크다.
    3. 변수 중복 선언 허용
        1. 의도하지 않은 변수값의 변경이 일어날 가능성이 크다.
    4. 변수 호이스팅
        1. 변수를 선언하기 이전에 참조할 수 있음

    대부분의 문제 = **전역변수**로 인해 발생

    **전역변수**는 간단한 애플리케이션의 경우, 사용이 편리하다는 장점이 있지만

    유효 범위(scope)가 넓어서 어디에서 어떻게 사용될 것인지 파악하기 힘들고

    비순수 함수(Impure function)에 의해 의도하지 않게 변경될 수 있어 복잡성을 증가시키는 원인이 되기 때문. 

    따라서, 변수의 스코프는 좁을수록 좋다.

    이러한 var의 단점을 보완하기 위해 ES6에서는 let과 const를 도입함

    ### let

    1. 블록 레벨 스코프
        1. 대부분의 프로그래밍 언어는 **블록 레벨 스코프**를 따르지만

            자바스크립트는 **함수 레벨 스코프**를 따른다.

    - 블록 레벨 스코프
        - 모든 코드 블록 (함수, If문, for문, while문, try/catch문 등) 내에서 선언된 변수는 코드 블록 내에서만 유효하며 코드 블록 외부에서는 참조 할 수 없음. 즉, 코드 블록 내부에서 선언한 변수는 지역변수이다.
    - 함수 레벨 스코프
        - 함수 내에서 선언된 변수는 함수 내에서만 유효하며 함수 외부에서는 참조할 수 없다. 즉, 함수 내부에서 선언한 변수는 지역 변수이며 함수 외부에서 선언한 변수는 모두 전역 변수이다.

    ```jsx
    var foo = 123; // 전역 변수

    console.log(foo); // 123

    {
      var foo = 456; // 전역 변수
    }

    console.log(foo); // 456
    ```

    블록 레벨 스코프를 따르지 않는 var 키워드의 특성 상, 코드 블록 내의 변수 foo는 **전역 변수**이다. 그런데 이미 전역 변수 foo가 선언되어 있다. var 키워드를 사용하여 선언한 변수는 중복 선언이 허용되므로 위의 코드는 문법적으로 아무런 문제가 없다. 단, 코드 블록 내의 변수 foo는 전역 변수이기 때문에 전역에서 선언된 전역 변수 foo의 값 123을 새로운 값 456으로 재할당하여 덮어쓴다.

    ES6는 **블록 레벨 스코프를 따르는 변수**를 선언하기 위해 let 키워드를 제공한다.

    ```jsx
    let foo = 123; // 전역 변수

    {
      let foo = 456; // 지역 변수
      let bar = 456; // 지역 변수
    }

    console.log(foo); // 123
    console.log(bar); // ReferenceError: bar is not defined
    ```

    **let 키워드로 선언된 변수는 블록 레벨 스코프를 따른다.** 위 예제에서 코드 블록 내에 선언된 변수 foo는 블록 레벨 스코프를 갖는 지역 변수이다. 전역에서 선언된 변수 foo와는 다른 별개의 변수이다. 또한 변수 bar도 블록 레벨 스코프를 갖는 지역 변수이다. 따라서 전역에서는 변수 bar를 참조할 수 없다.

    1. 변수 중복 선언 금지

        var 키워드로는 동일한 이름을 갖는 변수를 중복해서 선언할 수 있었다. 하지만, let 키워드로는 동일한 이름을 갖는 변수를 중복해서 선언할 수 없다. 변수를 중복 선언하면 문법 에러(SyntaxError)가 발생한다.

        ```jsx
        var foo = 123;
        var foo = 456;  // 중복 선언 허용

        let bar = 123;
        let bar = 456;  // Uncaught SyntaxError: Identifier 'bar' has already been declared
        ```

        1. 호이스팅

        **호이스팅(Hoisting)**이란, var 선언문이나 function 선언문 등을 해당 스코프의 선두로 옮긴 것처럼 동작하는 특성을 말한다.

        자바스크립트는 ES6에서 도입된 let, const를 포함하여 모든 선언(var, let, const, function, function*, class)을 호이스팅한다.

        하지만 var 키워드로 선언된 변수와는 달리 **let 키워드로 선언된 변수를 선언문 이전에 참조하면 참조 에러(ReferenceError)가 발생**한다. 이는 let 키워드로 선언된 변수는 스코프의 시작에서 변수의 선언까지 일시적 사각지대(Temporal Dead Zone; TDZ)에 빠지기 때문이다.

        변수 생성 3단계

        1. 선언
        2. 초기화
        3. 할당

        **var 키워드로 선언된 변수는 선언 단계와 초기화 단계가 한번에 이루어짐. 그래서 에러가 안나는 것 (대신 undefined 내놓음)**

        **→ 이러한 현상을 변수 호이스팅 (Variable Hoisting) 이라고 한다.**

        **반면, let 키워드로 선언된 변수는 선언 단계와 초기화 단계가 분리되어 진행된다. 그래서 에러가 나는 것**

        4. 클로저

        ```jsx
        var funcs = [];

        // 함수의 배열을 생성하는 for 루프의 i는 전역 변수다.
        for (var i = 0; i < 3; i++) {
          funcs.push(function () { console.log(i); });
        }

        // 배열에서 함수를 꺼내어 호출한다.
        for (var j = 0; j < 3; j++) {
          funcs[j]();
        }
        ```

        위 코드의 실행 결과로 0, 1, 2를 기대할 수도 있지만 결과는 3이 세 번 출력된다. 그 이유는 for 루프의 var i가 전역 변수이기 때문이다. 0, 1, 2를 출력하려면 아래와 같은 코드가 필요하다.

        ```jsx
        var funcs = [];

        // 함수의 배열을 생성하는 for 루프의 i는 전역 변수다.
        for (var i = 0; i < 3; i++) {
          (function (index) { // index는 자유변수다.
            funcs.push(function () { console.log(index); });
          }(i));
        }

        // 배열에서 함수를 꺼내어 호출한다
        for (var j = 0; j < 3; j++) {
          funcs[j]();
        }
        ```

        자바스크립트의 함수 레벨 스코프로 인하여 for 루프의 초기화 식에 사용된 변수가 전역 스코프를 갖게 되어 발생하는 문제를 회피하기 위해 [클로저](https://poiemaweb.com/js-closure)를 활용한 방법이다.

        ES6의 let 키워드를 for 루프의 초기화 식에 사용하면 클로저를 사용하지 않아도 위 코드와 동일한 동작을 한다.

        ```jsx
        var funcs = [];

        // 함수의 배열을 생성하는 for 루프의 i는 for 루프의 코드 블록에서만 유효한 지역 변수이면서 자유 변수이다.
        for (let i = 0; i < 3; i++) {
          funcs.push(function () { console.log(i); });
        }

        // 배열에서 함수를 꺼내어 호출한다
        for (var j = 0; j < 3; j++) {
          console.dir(funcs[j]);
          funcs[j]();
        }
        ```

        for 루프의 let i는 for loop에서만 유효한 지역 변수이다. 또한, i는 자유 변수로서 for 루프의 생명주기가 종료되어도 변수 i를 참조하는 함수가 존재하는 한 계속 유지된다.

    ### const

    1. **선언과 초기화**
        1. let은 재할당이 자유로우나 const는 재할당이 금지된다.
        2. 주의할 점은 const는 반드시 **선언과 동시에 할당**이 이루어져야 한다는 것이다. 그렇지 않으면 다음처럼 문법 에러(SyntaxError)가 발생한다.
        3. const는 let과 마찬가지로 **블록 레벨 스코프**를 갖는다.
        4. const는 객체에도 사용할 수 있다. 물론 이때도 재할당은 금지된다.

    2. **상수**
        1. 상수는 가독성과 유지보수의 편의를 위해 적극적으로 사용해야 함

        ```jsx
        // 10의 의미를 알기 어렵기 때문에 가독성이 좋지 않다.
        if (rows > 10) {
        }

        // 값의 의미를 명확히 기술하여 가독성이 향상되었다.
        const MAXROWS = 10;
        if (rows > MAXROWS) {
        }
        ```

    3. **const와 객체**
        1. **const는 재할당이 금지**된다. 이는 const 변수의 타입이 객체인 경우, 객체에 대한 **참조를 변경하지 못한다는 것**을 의미한다. 하지만 **이때 객체의 프로퍼티는 보호되지 않는다.** 다시 말하자면 재할당은 불가능하지만 할당된 **객체의 내용(프로퍼티의 추가, 삭제, 프로퍼티 값의 변경)은 변경할 수 있다.**

            ```jsx
            const user = { name: 'Lee' };

            // const 변수는 재할당이 금지된다.
            // user = {}; // TypeError: Assignment to constant variable.

            // 객체의 내용은 변경할 수 있다.
            user.name = 'Kim';

            console.log(user); // { name: 'Kim' }
            ```

        2. 객체의 내용이 변경되더라도 객체 타입 변수에 할당된 주소값은 변경되지 않는다. 따라서 **객체 타입 변수 선언에는 const를 사용**하는 것이 좋다. 만약에 명시적으로 **객체 타입 변수의 주소값을 변경(재할당)하여야 한다면 let을 사용한다.**

    4. **var vs let vs const**
        1. 변수 선언에는 기본적으로 const를 사용하고 let은 재할당이 필요한 경우에 한정해 사용하는 것이 좋다. 원시 값의 경우, 가급적 상수를 사용하는 편이 좋다. 그리고 객체를 재할당하는 경우는 생각보다 흔하지 않다. const 키워드를 사용하면 의도치 않은 재할당을 방지해 주기 때문에 보다 안전하다.

        var와 let, 그리고 const는 다음처럼 사용하는 것을 추천한다.

        - ES6를 사용한다면 var 키워드는 사용하지 않는다.
        - 재할당이 필요한 경우에 한정해 let 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
        - 변경이 발생하지 않는(재할당이 필요 없는 상수) 원시 값과 객체에는 const 키워드를 사용한다. const 키워드는 재할당을 금지하므로 var, let 보다 안전하다.

        변수를 선언하는 시점에는 재할당이 필요할지 잘 모르는 경우가 많다. 그리고 객체는 의외로 재할당을 하는 경우가 드물다. 따라서 변수를 선언할 때에는 일단 const 키워드를 사용하도록 하자. 반드시 재할당이 필요하다면(반드시 재할당이 필요한지 한번 생각해 볼 일이다.) 그때 const를 let 키워드로 변경해도 결코 늦지 않는다.

- **2.템플릿 리터럴**

    ES6는 **템플릿 리터럴**(Template literal)이라고 불리는 새로운 문자열 표기법을 도입하였다. 템플릿 리터럴은 일반 문자열과 비슷해 보이지만, ‘ 또는 “ 같은 통상적인 따옴표 문자 대신 **백틱(backtick) 문자 `**를 사용한다.

    ```jsx
    // ex1
    const template = `템플릿 리터럴은 '작은따옴표(single quotes)'과 "큰따옴표(double quotes)"를 혼용할 수 있다.`;

    console.log(template);

    // ex2
    const template = `<ul class="nav-items">
      <li><a href="#home">Home</a></li>
      <li><a href="#news">News</a></li>
      <li><a href="#contact">Contact</a></li>
      <li><a href="#about">About</a></li>
    </ul>`;

    console.log(template);
    ```

    템플릿 리터럴은 + 연산자를 사용하지 않아도 간단한 방법으로 새로운 문자열을 삽입할 수 있는 기능을 제공한다. 이를 문자열 인터폴레이션(String Interpolation)이라 한다.

    ```jsx
    const first = 'Ung-mo';
    const last = 'Lee';

    // ES5: 문자열 연결
    console.log('My name is ' + first + ' ' + last + '.');
    // "My name is Ung-mo Lee."

    // ES6: String Interpolation
    console.log(`My name is ${first} ${last}.`);
    // "My name is Ung-mo Lee."
    ```

    문자열 인터폴레이션은 ${ … }으로 표현식을 감싼다. 문자열 인터폴레이션 내의 표현식은 문자열로 강제 타입 변환된다.

    ```jsx
    console.log(`1 + 1 = ${1 + 1}`); // "1 + 1 = 2"
    ```