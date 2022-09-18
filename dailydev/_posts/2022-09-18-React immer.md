---
layout: post
title: React 일정관리
description: >
  2022-09-04-React 일정관리 웹 애플리케이션 만들기
hide_description: false
category: dailydev
---

- Table of Contents
{:toc}

리액트에서 상태를 업데이트 할때는 불변성을 유지하는것이 중요하다. 전개 연산자와 배열의 내장 함수를 사용하면서 불변성을 유지한다. 하지만 배열과 객체가 깊어지거나 복잡해지면 불변성을 유지하는 것이 힘들어진다. 전개 연산자는 가장 바깥쪽만 복사하기 때문에 만약 구조가 많이 깊다면 전개연산자를 여러번 사용해야하기 때문에 코드도 길어지고 복잡해진다.
이러한 상황에 immer 라이브러리를 사용하면 매우 쉽고 짧은 코드로 불변성을 유지할 수 있다.

## immer를 사용하지 않고 불변성 유지
우선 새로운 프로젝트를 생성하고 immer를 설치해준다.
```bash
$ yarn create react-app 프로젝트명
$ cd 프로젝트명
$ yarn add immer
```
App.js를 작성해준다
```javascript
//App.js
import React, { useRef, useCallback, useState } from 'react';

const App = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: '', username: ''});
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  });

  // input 수정을 위한 함수
  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setForm({
        ...form,
        [name]: [value]
      });
    },
    [form]
  );

// form 등록을 위한 함수
  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      };

      // array에 새 항목 등록
      setData({
        ...Date,
        array: data.array.concat(info)
      });

      // form 초기화
      setForm({
        name: '',
        username: ''
        });
        nextId.current += 1;
        },
        [data, form.name, form.username]
    );

    // 항목을 삭제하는 함수
  const onRemove = useCallback(
    id => {
      setData({
        ...data,
        array: data.array.filter(info => info.id !== id)
      });
    },
    [data]
  );

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          name="username"
          placeholder="아이디"
          value={form.username}
          onChange={onChange}
        />
        <input
          name="name"
          placeholder="이름"
          value={form.name}
          onChange ={onChange}
        />
        <button type="submit">등록</button>
      </form>
      <div>
        <ul>
          {data.array.map(info => (
            <li key={info.id} onClick={() => onRemove(info.id)}>
              {info.username} ({info.name})
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default App;
```

form 에서 입력을 하면 하단 리스트에 추가되고 항목을 클릭하면 삭제되는 컴포넌트이다. 전개 연산자와 배열 내장 함수를 사용하여 불변성을 유지했다.

## immer 사용법
immer를 사용하면 불변성을 유지하는 작업을 매우 간단하게 처리할 수 있다.
```javascript
//예시 코드
import produce from 'immer';
const nextState = produce(originalState, draft => {
  // 바꾸고 싶은 값 바꾸기
  draft.somewhere.deep.inside = 5;
})
```
produce 함수는 다음과 같은 파라미터를 받는다.
produce(수정하고 싶은 상태, 상태를 어떻게 업데이트할지 정의하는함수)
두번째 파라미터로 전달되는 함수 내부에서 원하는 값을 변경하면, produce 함수가 불변성을 유지를 대신해 주면서 새로운 상태를 생성해 준다.
다음 코드는 좀 더 복잡한 데이터를 불변성을 유지하면서 업데이트 하는 예시이다.
```javascript
//예시코드
import produce from 'immer';

const originalState = [
  {
    id: 1,
    todo: '전개 연산자와 배열 내장 함수로 불변성 유지하기',
    checked: true,
  },
  {
    id: 2,
    todo: 'immer로 불변성 유지하기',
    checked: false,
  }
];

const nextState = produce(originalState, draft => {
  // id가 2인 항목의 checked 값을 true로 설정
  const todo = draft.find(t => t.id === 2); // id로 항목 찾기
  todo.checked = true;
    // 혹은 draft[1].checked = true;

// 배열에 새로운 데이터 추가
  draft.push({
    id: 3,
    todo: ‘일정 관리 앱에 immmer 적용하기‘,
    checked: false,
  });

// id = 1인 항목을 제거하기
  draft.splice(draft.findIndex(t => t.id === 1), 1);
});
```

## App 컴포넌트에 immer 적용하기
```javascript
//App.js
import React, { useRef, useCallback, useState } from 'react';
import produce from 'immer';

const App = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: '', username: ''});
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  });

  // input 수정을 위한 함수
  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setForm(
        produce(form, draft => {
          draft[name] = value
        })
      )
    },
    [form]
  );

// form 등록을 위한 함수
  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      };

      // array에 새 항목 등록
      setData(
        produce(data, draft =>{
          draft.array.push(info);
        })
      );

      // form 초기화
      setForm({
        name: '',
        username: ''
        });
        nextId.current += 1;
        },
        [data, form.name, form.username]
    );

    // 항목을 삭제하는 함수
  const onRemove = useCallback(
    id => {
      setData( produce(data, draft => {
          draft.array.splice(draft.array.findIndex(info => info.id === id), 1);
        })
      );
    },
    [data]
  );

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          name="username"
          placeholder="아이디"
          value={form.username}
          onChange={onChange}
        />
        <input
          name="name"
          placeholder="이름"
          value={form.name}
          onChange ={onChange}
        />
        <button type="submit">등록</button>
      </form>
      <div>
        <ul>
          {data.array.map(info => (
            <li key={info.id} onClick={() => onRemove(info.id)}>
              {info.username} ({info.name})
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default App;
```
immer를 사용하면 위 코드와 같이 push, splice 등의 직접적인 변화를 일으키는 함수를 사용해도 무방하다. 자바스크립트에 익숙하다면 immer를 사용하면 쉽게 불변성을 유지할 수 있다. 굳이 immer룰 적용할 필요없이 복잡하지 않다면 사용하지 않아도 무방하다. 객체나 배열구조가 복잡할때 적용해도 충분하다.

## useState의 함수형 업데이트와 immer 함께 쓰기
immer에서 제공하는 produce 함수를 호출할 때, 첫 번째 파라미터가 함수 형태라면 업데이트 함수를 반환한다.
```javascript
//예시 코드
const update = (draft => {
  draft.value = 2;
});
const originalState = {
  value: 1,
  foo: 'bar',
};
const nextState = update(originalState);
console.log(nextState); // { value: 2, foo: 'bar' }
```

이러한 immer의 속성과 useState의 함수형 업데이트를 다음 코드처럼 함께 활용할 수 있다.
```javascript
//App.js
import React, { useRef, useCallback, useState } from 'react';
import produce from 'immer';

const App = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: '', username: ''});
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  });

  // input 수정을 위한 함수
  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setForm(
        produce(draft => {
          draft[name] = value
        })
      )
    },
    []
  );

// form 등록을 위한 함수
  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      };

      // array에 새 항목 등록
      setData(
        produce(draft =>{
          draft.array.push(info);
        })
      );

      // form 초기화
      setForm({
        name: '',
        username: ''
        });
        nextId.current += 1;
        },
        [form.name, form.username]
    );

    // 항목을 삭제하는 함수
  const onRemove = useCallback(
    id => {
      setData( produce(data, draft => {
          draft.array.splice(draft.array.findIndex(info => info.id === id), 1);
        })
      );
    },
    [data]
  );

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          name="username"
          placeholder="아이디"
          value={form.username}
          onChange={onChange}
        />
        <input
          name="name"
          placeholder="이름"
          value={form.name}
          onChange ={onChange}
        />
        <button type="submit">등록</button>
      </form>
      <div>
        <ul>
          {data.array.map(info => (
            <li key={info.id} onClick={() => onRemove(info.id)}>
              {info.username} ({info.name})
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default App;
```

## 참고 문헌

[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&linkClass=&barcode=9791160508796)