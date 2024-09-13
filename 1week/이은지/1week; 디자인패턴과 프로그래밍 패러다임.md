## 1.디자인 패턴

프로그램을 설계할 때 발생했던 문제점들을 객체 간의 상호 관계 등을 이용하여 해결할 수 있도록 하나의 ‘규약’형태로 만들어 놓은 것을 의미
### 1.1 싱글톤 패턴
애플리케이션에서 **오직 하나의 인스턴스만 생성**하여 어디서든 동일한 인스턴스를 사용하게 하는 디자인 패턴 
- 장점
  - 하나의 인스턴스를 만들어 놓고 해당 인스턴스를 다른 모듈들이 공유하며 사용하기 때문에 자원을 낭비하지 않고 한 번 생성한 객체를 재사용하고자할 때 유용 
- 단점
  - 의존성이 높아짐
  - 다수의 클라이언트가 해당 인스턴스에 접근하고 수정가능하기 때문에 상태 변화를 조심스럽게 다루어야 함
  
- 구현예시
  - 상태관리 라이브러리에서의 싱글톤
    - 프론트엔드에서 자주 사용하는 Redux는 본질적으로 싱글톤 패턴의 개념을 따름 
    - Redux스토어는 애플리케이션 내에서 **하나의 전역 상태**를 관리하고, 이 스토어를 애플리케이션 어디서나 접근할 수 있도록 함
   
    ```
    import { createStore } from 'redux';
    import rootReducer from './reducers';

    // Redux 스토어는 애플리케이션 전체에서 하나만 생성된다.
    const store = createStore(rootReducer);

    export default store;
    ```

  - Axios와 같은 API 클라이언트에서 싱글톤 
    - 서버에 HTTP요청을 보낼 때, 동일한 설정(기본 URL, 헤더 등)을 가진 Axios 인스턴스를 사용하고, 여러 곳에서 재사용 가능 
  
    ```
    import axios from 'axios';

    // Axios 싱글톤 인스턴스 생성
    const apiClient = axios.create({
    baseURL: 'https://api.example.com',
    timeout: 1000,
    headers: { 'Authorization': 'Bearer token' }
    });

    export default apiClient;
    ```

### 1.2 팩토리 패턴
객체 생성 로직을 별도의 클래스 또는 메서드로 분리하여, 객체 생성 방식을 캡슐화하는 디자인 패턴으로 이 패턴을 사용하면 객체 생성 과정을 숨기고, 객체를 생성할 때 필요한 정보만으로 원하는 객체를 생성할 수 있음 
- 다음과 같은 상황에서 유용
    - 객체 생성 로직을 중앙에서 관리하고 싶을 때
    - 객체 생성에 필요한 다양한 파라미터나 조건에 따라 적절한 클래스의 인스턴스를 반환하고 싶을 때
    - 코드의 확장성을 높이고, 객체 생성 로직을 유연하게 변경하고 싶을 때 
> 팩토리 패턴은 객체 생성 방법을 한 곳에 모아 결합도를 낮추고 코드의 유연성을 높이는데 중점을 둠 
- 구현 예시: 버튼 생성 팩토리
    - 웹에서 다양한 종류의 버튼을 생성할 때, 버튼 타입에 따라 다른 디자인, 동작을 구현 가능함. 이 때 팩토리 패턴을 사용하면 버튼 타입에 따라 알맞은 버튼 객체를 쉽게 생성 가능 
  
    1. 각 버튼 컴포넌트 정의
        ```
        import React from 'react';

        const PrimaryButton = ({ text, onClick }) => (
        <button className="btn btn-primary" onClick={onClick}>
            {text}
        </button>
        );

        const SecondaryButton = ({ text, onClick }) => (
        <button className="btn btn-secondary" onClick={onClick}>
            {text}
        </button>
        );

        const DangerButton = ({ text, onClick }) => (
        <button className="btn btn-danger" onClick={onClick}>
            {text}
        </button>
        );

        export { PrimaryButton, SecondaryButton, DangerButton };
        ```

     2. 버튼 팩토리 컴포넌트 
        ```
        import React from 'react';
        import { PrimaryButton, SecondaryButton, DangerButton } from './Buttons';

        const ButtonFactory = ({ type, text, onClick }) => {
        switch (type) {
            case 'primary':
            return <PrimaryButton text={text} onClick={onClick} />;
            case 'secondary':
            return <SecondaryButton text={text} onClick={onClick} />;
            case 'danger':
            return <DangerButton text={text} onClick={onClick} />;
            default:
            return <button onClick={onClick}>{text}</button>; // 기본 버튼
        }
        };

        export default ButtonFactory;  
        ```

    2. 사용예시
        ```
        import React from 'react';
        import ButtonFactory from './ButtonFactory';

        const App = () => {
        const handleClick = (type) => {
            alert(`${type} button clicked!`);
        };

        return (
            <div>
            <h1>Button Factory Example</h1>
            <ButtonFactory type="primary" text="Primary Button" onClick={() => handleClick('Primary')} />
            <ButtonFactory type="secondary" text="Secondary Button" onClick={() => handleClick('Secondary')} />
            <ButtonFactory type="danger" text="Danger Button" onClick={() => handleClick('Danger')} />
            <ButtonFactory text="Default Button" onClick={() => handleClick('Default')} /> {/* Default case */}
            </div>
        );
        };

        export default App;
        ```
### 1.3 전략 패턴
객체의 행위를 바꾸고 싶은 경우, 직접 수정하지 않고 전략이라고 부르는 '캡슐화한 알고리즘'을 컨텍스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴 
> 해당 패턴의 핵심은 동작(알고리즘)과 그 동작을 사용하는 클라이언트를 분리함으로써, 클라이언트 코드에서 알고리즘을 변경하지 않고도 쉽게 교체할 수 있다는 것임 

- 구현 예시: 결제 시스템
  - 결제 시 카카오페이, 네이버페이 등을 사용하듯이 결제방식의 전략만 바꿔서 결제하는 것을 예시로 들 수 있음 
- 구현 예시: 유저 인증 시스템
  - 다양한 인증 방법(OAuth, JWT, 세션)을 구현할 때, 전략 패턴을 사용하여 인증 방식을 유연하게 선택할 수 있음

### 1.4 옵저버 패턴
일대다 의존 관계를 정의하여, 하나의 객체 상태가 변경될 때 그와 의존 관계에 있는 다른 객체들에게 자동으로 알림을 보내는 디자인 패턴
- 옵저버 패턴 구조
  - Subject(발행자): 상태를 관리하고, 옵저버를 등록하거나 해지하는 인터페이스를 정의함. 상태가 변경되면 등록된 옵저버들에게 알림을 보냄
  - Observer(구독자): 발행자의 상태 변화를 감지하고, 그에 따라 행동을 취하는 인터페이스를 정의. 발행자가 알림을 보내면, 이를 받아 특정 동작을 수행
- 구현예시
  - 리액트 Context API와 useContext훅
    - 다음과 같이 ThemeContext라는 발행자를 만들어 전역 상태를 관리하고, 여러 컴포넌트에서 테마 변경을 감지하여 UI를 업데이트 가능
    1. 발행자 생성
    ```
    import React, { createContext, useState, useContext } from 'react';

    // ThemeContext 생성
    const ThemeContext = createContext();

    // ThemeProvider: 발행자 역할
    const ThemeProvider = ({ children }) => {
    const [theme, setTheme] = useState('light');

    // 테마를 변경하는 함수
    const toggleTheme = () => {
        setTheme((prevTheme) => (prevTheme === 'light' ? 'dark' : 'light'));
    };

    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
        {children}
        </ThemeContext.Provider>
        );
    };

    export { ThemeContext, ThemeProvider };
    ```

    2. 구독자(옵저버)컴포넌트 생성
    - useContext훅을 사용하여 ThemeCotnext의 상태를 구독하고, 상태가 변경될 때 자동으로 UI를 업데이트 
    ```
    import React, { useContext } from 'react';
    import { ThemeContext } from './ThemeProvider'; // ThemeContext 가져오기

    // 테마를 구독하는 버튼 컴포넌트
    const ThemeButton = () => {
    const { theme, toggleTheme } = useContext(ThemeContext);

    return (
        <button
        onClick={toggleTheme}
        style={{
            backgroundColor: theme === 'light' ? '#fff' : '#333',
            color: theme === 'light' ? '#000' : '#fff',
            padding: '10px',
            border: '1px solid',
        }}
        >
        Toggle Theme (Current: {theme})
        </button>
    );
    };

    export default ThemeButton;
    ```


### 1.5 프록시 패턴과 프록시 서버
- 프록시 패턴
  - 대상 객체에 접근하기 전 그 접근에 대한 흐름을 가로채 대상 객체 앞단의 인터페이스 역할을 하는 디자인 패턴
  - 이를 통해 객체의 속성, 변환 등을 보완하며 보안, 데이터 검증, 캐싱, 로깅에 사용 
- 프록시 서버
  - 서버와 클라이언트 사이에서 클라이언트가 자신을 통해 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 컴퓨터 시스템이나 응용 프로그램을 가리킴
  - 예시: nginx
    - 비동기 이벤트 기반의 구조와 다수의 연결을 효과적으로 처리 간으한 웹 서버이며, 주로 서버 앞단의 프록시 서버로 활용
    - 서버를 구축할 때 앞단에 nginx를 두게 되면 익명 사용자의 직접적인 서버로의 접근을 차단하고, 간접적인 단계를 한단계 더 거침으로서 보안성이 강화됨

### 1.6 MVC패턴
모델(Model), 뷰(View), 컨트롤러(Controller)로 이루어진 디자인 패턴
- 장점
  - 애플리케이션의 구성요소를 세 가지 역할로 구분하여 개발 프로세스에서 각각의 구성 요소에만 집중해서 개발할 수 있으며, 재사용성과 확장성이 용이함
- 단점
  - 애플리케이션이 복잡해질수록 모델과 뷰의 관계가 복잡해짐 
- 구조
  - Model: 애플리케이션의 데이터인 데이터베이스, 상수, 변수 등을 뜻함
  - View: inputbox, checkbox, textarea 등 사용자 인터페이스 요소를 나타내며 모델을 기반으로 사용자가 볼 수 있는 화면을 뜻함
  - Controller: 하나 이상의 모델과 하나 이상의 뷰를 잇는 다리 역할을 하며, 메인 로직을 담당함. 모델과 뷰의 생명주기도 관리하며모델이나 뷰의 변경 통지를 받으면 각각의 구성 요소에 해당 내용에 대해 알려줌
- 사용예시: React에서 MVC 패턴
  - Model: State 또는 store
  - View: JSX
  - Controller: 컴포넌트에 있는 로직 
  
> 프론트엔드에서 MVC패턴에 대해 찾아보니, state 구현 방식에 따라 MVC와 FLUX로 구분되는 거 같다. 이부분에 대해 따로 더 찾아봐야겠다. 


## 2. 프로그래밍 패러다임
### 2.1 선언형과 함수형 프로그래밍
- 정의 
  - '무엇을' 풀어내는가에 집중하는 패러다임으로, '순수함수'들을 블록처럼 쌓아 로직을 구현하고, '고차 함수'를 통해 재사용성을 높인 프로그래밍 패러다임
  - 함수형 프로그래밍은 선언형 프로그래밍의 일종
  - 자바스크립트의 경우, 단순하고 유연한 언어이며 함수가 일급객체이기 때문에 객체지향 프로그래밍 보다는 함수형 프로그래밍 방식이 선호됨 
- 순수함수 
  - 출력이 입력에만 의존하는 것을 의미하며, 만약 다른 전역변수 등이 출력에 영향을 주면 순수함수가 아님
- 고차함수
  - 함수가 함수를 값처럼 매개변수로 받아 로직을 생성할 수 있는 것 
  - 고차함수를 쓰기 위해서는 해당 언어가 일급 객체라는 특징을 가져야 하며, 그 특징은 다음과 같음
    - 변수나 메서드에 함수를 할당할 수 있음
    - 함수 안에 함수를 매개변수로 담을 수 있음
    - 함수가 함수를 반환할 수 있음 

### 2.2 객체지향 프로그래밍
- 정의
  - 객체들의 집합으로, 프로그램의 상호작용을 표현하며 데이터를 객체로 취급하여 객체 내부에 선언된 메서드를 활용하는 방식
  - 설계에 많은 시간이 소요되며, 처리 속도가 다른 프로그래밍 패러다임에 비해 상대적으로 느림
- 특징
  - 추상화
    - 복잡한 시스템으로부터 핵심적인 개념 또는 기능을 간추려내는 것을 의미 
  - 캡슐화
    - 객체의 속성과 메서드를 하나로 묶고 일부를 외부에 감추어 은닉하는 것
  - 상속성
    - 상위 클래스의 특성을 하위 클래스가 이어받아서 재사용하거나 추가, 확장하는 것 
  - 다형성 
    - 하나의 메서드나 클래스가 다양한 방법으로 동작하는 것
    - 대표적으로 오버로딩, 오버라이딩이 있음 
- 설계원칙: SOLID 원칙
  - 단일 책임 원칙
    - 모든 클래스는 각각 하나의 책임만 가져야하는 원칙
  - 개방-폐쇄 원칙
    - 유지 보수 사항이 생기낟면 코드를 쉽게 확장할 수 있도록 하고, 수정할 때는 닫혀 있어야 하는 원칙
  - 리스코프 치환 법칙
    - 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 함
    - 부모 객체에 자식 객체를 넣어도 시스템이 문제없이 돌아가게 만드는 것
  - 인터페이스 분리 원칙
    - 하나의 일반적인 인터페이스보다 구체적인 여러 개의 인터페이스를 만들어야 하는 원칙
  - 의존 역전 원칙
    - 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 시운 것의 변화에 영향받지 않게 하는 원칙
### 2.3 절차형 프로그래밍
- 로직이 수행되어야 할 연속적인 계산 과정으로 이루어져 있음
- 일이 진행되는 방식으로 코드를 구현하면 되므로, 코드의 가독성이 좋으며 실행 속도가 빠름 
- 사용예시: 머신러닝의 배치 작업 등
- 단점: 모듈화하기가 어렵고, 유지보수성이 떨어짐 
### 2.4 패러다임의 혼합
- 여러 패러다임의 장접만 조합해 상황과 맥락에 따라 개발하는 것이 중요!
- 예를 들어 백엔드에 머신러닝 파이프라인과 거래 관련 로직이 있다면, 머신 러닝 파이프라인은 절차지향형 패러다임, 거래 관련 로직은 함수형 프로그래밍을 적용하는 것이 좋음 