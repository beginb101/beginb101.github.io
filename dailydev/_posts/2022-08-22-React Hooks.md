---
layout: post
title: React 라이프사이클
description: >
  2022-08-07-React 라이프사이클
hide_description: false
category: dailydev
---

- Table of Contents
{:toc}

## useState
useState는 함수형 컴포넌트에서도 가변적인 상태를 지닐 수 있게 해주는 가장 기본적인 Hook 이다.
함수형 컴포넌트에서 상태를 관리해야 할때 이 Hook을 사용한다.<br>
다음 코드를 작성하고 랜더링 해보자
```javascript
//Counter.js
import React, { useState } from 'react';

const Counter = () => {
  const [value, setValue] = useState(0);

return (
    <div>
      <p>
        현재 카운터 값은 <b>{value}</b>입니다.
      </p>
      <button onClick={() => setValue(value + 1)}>+1</button>
      <button onClick={() => setValue(value - 1)}>-1</button>
    </div>
  );
};

export default Counter;

```
<br>

```javascript
//App.js
import React from 'react';
import Counter from './Counter';
 
const App = () => {
  return <Counter/>;
};
 
export default App;
```

![image](/assets/img/blog/counterstate.jpg)
<br> useState로 구현한 카운터
{:.figure}

useState는 코드 상단에서 import를 통해 불러오고 

```javascript
const [value, setValue] = useState(0);
```

이렇게 사용한다. useState 함수의 파라미터에는 상태의 기본값을 넣어 줄 수 있다. 위 코드는 기본값을 0으로 설정한다.
useState는 배열을 반환하며 첫 번째 요소는 상태 값, 두 번째 요소는 상태를 설정하는 setter함수이다. setter 함수에 파라미터를 넣어서 호출하면 전달받은 파라미터로 상태 값이 바뀌고 컴포넌트가 정상적으로 리렌더링 된다.

## useState 여러 번 사용하기
하나의 useState 함수는 하나의 상태 값만 관리할 수 있기 때문에 여러 개의 상태를 관리하려면 useState를 여러 번 사용해야 한다.
다음 예제는 useState를 여러 번 사용하는 예제이다
```javascript
///Info.js
import React, { useState } from 'react';

const Info = () => {
  const [name, setName] = useState('');
  const [nickname, setNickname] = useState('');
 
  const onChangeName = e => {
    setName(e.target.value);
  };
 
  const onChangeNickname = e => {
    setNickname(e.target.value);
  };
 
  return (
    <div>
      <div>
        <input value={name} onChange={onChangeName} />
        <input value={nickname} onChange={onChangeNickname} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임:</b> {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```
```javascript
//App.js
import React from 'react';
import Info from './Info';
 
const App = () => {
  return <Info />;
};
 
export default App;
```

![image](/assets/img/blog/state2.jpg)
<br> useState를 여러 번 사용하기
{:.figure}

## useEffect
useEffect는 리액트 컴포넌트가 렌더링될 때마다 특정 작업을 수행하도록 설정할 수 있는 Hook이다.
클래스형 컴포넌트의 componentDidMount와 componentDidUpdate를 합친 형태로 보아도 무방하다.
Info.js 를 다음과 같이 수정하면 useEffect를 사용할 수 있다.
```javascript
//Info.js
import React, { useState, useEffect } from 'react';

const Info = () => {
  const [name, setName] = useState('');
  const [nickname, setNickname] = useState('');
  useEffect(() => {
    console.log('렌더링이 완료되었습니다!');
    console.log({
      name,
      nickname
    });
  });
  const onChangeName = e => {
    setName(e.target.value);
  };
 
  const onChangeNickname = e => {
    setNickname(e.target.value);
  };
 
  return (
    <div>
      <div>
        <input value={name} onChange={onChangeName} />
        <input value={nickname} onChange={onChangeNickname} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임:</b> {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

![image](/assets/img/blog/useEffect.jpg)
<br> useEffect
{:.figure}


## 마운트될 때만 실행하고 싶을 때
useEffect에서 설정한 함수를 컴포넌트가 화면에 맨 처음 렌더링될 때만 실행하고, 업데이트될 때는 실행하지 않으려면 함수의 두 번째 파라미터로 비어 있는 배열을 넣어 주면 된다. 이전 예제에서 useEffect를 다음과 같이 수정하자
```javascript
useEffect(() => {
    console.log('마운트될 때만 실행됩니다.');
  }, []);
```


![image](/assets/img/blog/ifmount.jpg)
<br> 마운트될 때만 실행된다
{:.figure}


## 특정 값이 업데이트될 때만 실행하고 싶을때
useEffect를 사용할 때, 특정값이 변경될 때만 호출하고 싶을 경우, useEffect의 두 번째 파라미터로 전달되는 배열안에 검사하고 싶은 값을 넣어 주면 된다.

```javascript
useEffect(() => {
    console.log(name);
  }, [name]);
```


## 뒷정리하기
useEffect는 기본적으로 렌더링되고 난 직후마다 실행된다. 두 번째 파라미터 배열에 무엇을 넣는지에 따라 실행되는 조건이 달라진다. 만약 컴포넌트가 언마운트되기 전이나 업데이트되기 직전에 어떠한 작업을 수행하고 싶다면 useEffect에서 뒷정리 함수를 반환해 주어야 한다.

```javascript
//Info.js - useEffect
useEffect(() => {
    console.log('effect');
    console.log(name);
    return () => {
      console.log('cleanup');
      console.log(name);
    };
  });
```
```javascript
//App.js
import React, { useState } from 'react';
import Info from './Info';
 
const App = () => {
  const [visible, setVisible] = useState(false);
  return (
    <div>
        <button
        onClick={() => {
          setVisible(!visible);
          }}
        >
          {visible ? '숨기기' : '보이기'}
        </button>
        <hr />
      {visible && <Info />}
      </div>
  );
  };
 
export default App;
```
랜더링될 때마다 뒷정리 함수가 호출되고 뒷정리 함수가 호출될 떄는 업데이트 되기 직전의 값을 보여준다.


## useReducer
useReducer는 useState보다 더 다양한 컴포넌트 상황에 따라 다양한 상태를 다른 값으로 업데이트해 주고 싶을 때 사용하는 Hook입니다.
리듀서는 현재 상태, 그리고 업데이트를 위해 필요한 정보를 담은 액션값을 전달받아 새로운 상태를 반환하는 함수이다. 리듀서 함수에서 새로운 상태를 만들 때는 반드시 불변성을 지켜 주어야 한다.

```javascript
//Counter.js
import React, { useReducer } from 'react';

function reducer(state, action) {
    // action.type에 따라 다른 작업 수행
    switch (action.type) {
      case 'INCREMENT':
        return { value: state.value + 1 };
      case 'DECREMENT':
        return { value: state.value - 1 };
      default:
        // 아무것도 해당되지 않을 때 기존 상태 반환
        return state;
    }
  }

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { value: 0 });

return (
    <div>
      <p>
        현재 카운터 값은 <b>{state.value}</b>입니다.
      </p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+1</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-1</button>
    </div>
  );
};

export default Counter;
```
```javascript
//App.js
import React from 'react';
import Counter from './Counter';
 
const App = () => {
  return <Counter />;
}; 
 
export default App;
```

useReducer의 첫 번째 파라미터에는 리듀서 함수, 두 번째 파라미터에는 해당 리듀서의 기본값을 설정한다. 
useReducer는 state 값과 dispatch 함수를 받아 온다. 여기서 state는 현재 가리키고 있는 상태고, dispatch는 액션을 발생시키는 함수이다. dispatch(action)과 같은 형태로, 함수 안에 파라미터로 액션 값을 넣어 주면 리듀서 함수가 호출되는 구조다
useReducer를 사용했을 때의 가장 큰 장점은 컴포넌트 업데이트 로직을 컴포넌트 바깥으로 빼낼 수 있다.

## 인풋 상태 관리하기
useReduce를 사용하여 인풋 상태를 관리할 수 있다.

```javascript
//info.js
import React, { useReducer } from 'react';

function reducer(state, action) {
  return {
    ...state,
    [action.name]: action.value
  };
}

const Info = () => {
  const [state, dispatch] = useReducer(reducer, {
    name: '',
    nickname: ''
  });
  const { name, nickname } = state;
  const onChange = e => {
    dispatch(e.target);
  };

return (
    <div>
      <div>
        <input name="name" value={name} onChange={onChange} />
        <input name="nickname" value={nickname} onChange={onChange} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임: </b>
          {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

```javascript
//App.js
import React from 'react';
import Info from './Info';

const App = () => {
  return <Info />;
};
 
export default App;
```
useReducer에서의 액션은 그 어떤 값도 사용 가능하다. e.target 값 자체를 액션 값으로 사용했다.

## useMemo
useMemo를 사용하면 함수형 컴포넌트 내부에서 발생하는 연산을 최적화할 수 있다. 
먼저 리스트에 숫자를 추가하면 추가된 숫자들의 평균을 보여 주는 함수형 컴포넌트를 작성해 본다.

```javascript
//Average.js
import React, { useState } from 'react';

const getAverage = numbers => {
  console.log('평균값 계산 중..');
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
};

const Average = () => {
  const [list, setList] = useState([]);
  const [number, setNumber] = useState('');
 
  const onChange = e => {
    setNumber(e.target.value);
  };
  const onInsert = e => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
  };
 
  return (
    <div>
      <input value={number} onChange={onChange} />
      <button onClick={onInsert}>등록</button>
      <ul>
        {list.map((value, index) => (
          <li key={index}>{value}</li>
        ))}
      </ul>
      <div>
        <b>평균값:</b> {getAverage(list)}
      </div>
    </div>
  );
};

export default Average;
```
```javascript
import React from 'react';
import Average from './Average';
 
const App = () => {
  return <Average />;
};
 
export default App;
```

![image](/assets/img/blog/reatav.jpg)
<br> 평균 계산하기
{:.figure}

<br>
input 내용이 수정될 때도 getAverage 함수가 호출될 것이다. 낭비없이 등록버튼을 누를때만 평균값을 계산하게 할때 useMemo Hook을 사용하면 된다.
렌더링하는 과정에서 특정 값이 바뀌었을 때만 연산을 실행하고, 원하는 값이 바뀌지 않았다면 이전에 연산했던 결과를 다시 사용하는 방식이다.<br>

```javascript
//Average.js
import React, { useState, useMemo } from 'react';

const getAverage = numbers => {
  console.log('평균값 계산 중..');
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
};

const Average = () => {
  const [list, setList] = useState([]);
  const [number, setNumber] = useState('');
 
  const onChange = e => {
    setNumber(e.target.value);
  };
  const onInsert = e => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
  };
 
  const avg = useMemo(() => getAverage(list), [list]);

  return (
    <div>
      <input value={number} onChange={onChange} />
      <button onClick={onInsert}>등록</button>
      <ul>
        {list.map((value, index) => (
          <li key={index}>{value}</li>
        ))}
      </ul>
      <div>
        <b>평균값:</b> {avg}
      </div>
    </div>
  );
};

export default Average;
```
이제 list 배열의 내용이 바뀔 때만 getAverage 함수가 호출된다.

## useCallback
useCallback은 useMemo와 상당히 비슷한 함수다. 주로 렌더링 성능을 최적화해야 하는 상황에서 사용하며 이 Hook을 사용하면 이벤트 핸들러 함수를 필요할 때만 생성할 수 있다. 컴포넌트의 렌더링이 자주 발생하거나 렌더링해야 할 컴포넌트의 개수가 많아지면 이 부분을 최적화해 주는 것이 좋다.

```javascript
//Average.js
import React, { useState, useMemo, useCallback } from 'react';

const getAverage = numbers => {
  console.log('평균값 계산 중..');
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
};

const Average = () => {
  const [list, setList] = useState([]);
  const [number, setNumber] = useState('');

const onChange = useCallback(e => {
    setNumber(e.target.value);
}, []); // 컴포넌트가 처음 렌더링될 때만 함수 생성
const onInsert = useCallback(() => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
  }, [number, list]); // number 혹은 list가 바뀌었을 때만 함수 생성

const avg = useMemo(() => getAverage(list), [list]);

return (
    <div>
      <input value={number} onChange={onChange}  />
      <button onClick={onInsert}>등록</button>
      <ul>
        {list.map((value, index) => (
          <li key={index}>{value}</li>
        ))}
      </ul>
      <div>
        <b>평균값:</b> {avg}
      </div>
    </div>
  );
};

export default Average;
```
useCallBack의 첫 번째 파라미터에는 생성하고 싶은 함수를 넣고 두 번째 파라미터에는 배열을 넣으면 된다.
이 배열에는 어떤 값이 바뀌었을 때 함수를 새로 생성해야 하는지 __명시__ 해야 한다. 배열이 비어 있으면 컴포넌트가 렌더링될 때 단 한 번만 함수가 생성되며, onInsert처럼 배열 안에 number와 list를 넣게 되면 input 내용이 바뀌거나 새로운 항목이 추가될 때마다 함수가 생성된다.
함수 내부에서 __상태 값에 의존해야 할 때__ 는 그 값을 반드시 __두 번째 파라미터__ 안에 포함시켜 주어야 한다.<br>

숫자, 문자열, 객체처럼 일반 값을 재사용하려면 useMemo를 사용하고, 함수를 재사용하려면 useCallback을 사용하면 된다.

## useRef
useRef Hook은 함수형 컴포넌트에서 ref를 쉽게 사용할 수 있도록 해준다. Average 컴포넌트에서 등록 버튼을 눌렀을 때 포커스가 인풋 쪽으로 넘어가도록 코드를 작성해 보겠습니다.

```javascript
//Average.js
import React, { useState, useMemo, useCallback, useRef } from 'react';

const getAverage = numbers => {
  console.log('평균값 계산 중..');
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
};

const Average = () => {
  const [list, setList] = useState([]);
  const [number, setNumber] = useState('');
  const inputEl = useRef(null);
const onChange = useCallback(e => {
    setNumber(e.target.value);
}, []); // 컴포넌트가 처음 렌더링될 때만 함수 생성
const onInsert = useCallback(() => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
    inputEl.current.focus();
  }, [number, list]); // number 혹은 list가 바뀌었을 때만 함수 생성

const avg = useMemo(() => getAverage(list), [list]);

return (
    <div>
      <input value={number} onChange={onChange} ref={inputEl}/>
      <button onClick={onInsert}>등록</button>
      <ul>
        {list.map((value, index) => (
          <li key={index}>{value}</li>
        ))}
      </ul>
      <div>
        <b>평균값:</b> {avg}
      </div>
    </div>
  );
};

export default Average;
```
등록 버튼을 눌렀을 때 포커스가 input으로 넘어가는걸 확인할 수 있다..
useRef를 사용하여 ref를 설정하면 useRef를 통해 만든 객체 안의 current 값이 실제 엘리먼트를 가리킨다.

## 로컬 변수 사용하기
컴포넌트 로컬 변수를 사용해야 할 때도 useRef를 활용할 수 있다. 로컬 변수는 렌더링과 상관없이 바뀔 수 있는 값이다.

```javascript
import React, { useRef } from 'react';
 
const RefSample = () => {
  const id = useRef(1);
  const setId = (n) => {
    id.current = n;
  }
  const printId = () => {
    console.log(id.current);
  }
  return (
    <div>
      refsample
    </div>
  );
};
 
export default RefSample;

```
이렇게 ref안의 값이 바뀌어도 렌더링되지 않기 때문에 주의해야 한다. 렌더링과 관련되지 않은 값을 관리할 때만 이러한 방식을 사용한다.

## 커스텀 Hooks 만들기
여러 컴포넌트에서 비슷한 기능을 공유할 경우 이를 각자의 Hook으로 작성하여 로직을 사용할 수 있다.

```javascript
//useInputs.js
import { useReducer } from 'react';
 
function reducer(state, action) {
  return {
    ...state,
    [action.name]: action.value
  };
}
 
export default function useInputs(initialForm) {
  const [state, dispatch] = useReducer(reducer, initialForm);
  const onChange = e => {
    dispatch(e.target);
  };
  return [state, onChange];
}
```

```javascript
//Info.js
import React from 'react';
import useInputs from './useInputs';
 
const Info = () => {
  const [state, onChange] = useInputs({
    name: '',
    nickname: ''
  });
  const { name, nickname } = state;
 
  return (
    <div>
      <div>
        <input name="name" value={name} onChange={onChange} />
        <input name="nickname" value={nickname} onChange={onChange} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임: </b>
          {nickname}
        </div>
      </div>
    </div>
  );
};
 
export default Info;
```

이렇게 따로 Hooks을 만들어 사용할 수 있따.

## 다른 Hooks
다른 개발자가 만든 커스텀 Hooks도 라이브러리로 설치하여 사용할 수 있다.
다른 개발자가 만든 다양한 Hooks 리스트는 다음 링크에서 확인할 수 있다.

- [https://nikgraf.github.io/react-hooks/](https://nikgraf.github.io/react-hooks/)

- [https://github.com/rehooks/awesome-react-hooks](https://github.com/rehooks/awesome-react-hooks)

## 정리 
- 리액트에서 Hooks 패턴을 사용하면 클래스형 컴포넌트를 작성하지 않고도 대부분의 기능을 구현할 수 있다. 

- 리액트 매뉴얼에서는 새로 작성하는 컴포넌트의 경우 함수형 컴포넌트와 Hooks를 사용할 것을 권장하고 있다. 

- 기존의 클래스형 컴포넌트는 앞으로도 계속해서 지원될 예정이기 때문에 유지 보수하고 있는 프로젝트에서 클래스형 컴포넌트를 사용하고 있다면, 이를 굳이 함수형 컴포넌트와 Hooks를 사용하는 형태로 전환할 필요는 없다. 

-useEffect는 클래스형 컴포넌트의 componentDidMount와 componentDidUpdate를 합친 형태로 보아도 무방하다.

- useReducer는 useState보다 더 다양한 컴포넌트 상황에 따라 다양한 상태를 다른 값으로 업데이트해 주고 싶을 때 사용하는 Hook, 함수에서 새로운 상태를 만들 때는 반드시 불변성을 지켜 주어야 한다.

- useMemo는 함수형 컴포넌트 내부에서 발생하는 연산을 최적화할 수 있다. 

- 숫자, 문자열, 객체처럼 일반 값을 재사용하려면 useMemo를 사용하고, 함수를 재사용하려면 useCallback을 사용하면 된다.

## 참고 문헌

[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&linkClass=&barcode=9791160508796)