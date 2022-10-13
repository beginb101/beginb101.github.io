---
layout: post
title: React Context API 
description: >
  2022-10-09-React Context API 만들기
hide_description: false
category: dailydev
---

- Table of Contents
{:toc}

## Context API
Context API는 리액트 프로젝트에서 전역적으로 사용할 데이터가 있을 때 유용한 기능이다.
이 기능은 리액트 관련 라이브러리 리덕스, 리액트 라우터, styled components 등의 기반이기도 하다.

## Context API를 사용한 전역 상태 흐름
리액트 애플리케이션에서는 데이터를 props로 전달하기 때문에 최상위 컴포넌트인 App의 state에 넣어서 관리한다. 즉 컴포넌트가 많아지면 prop를 전달하기 위해 App에서 부터 많은 컴포넌트를 거쳐야할 가능성이 있다. 이런 방식을 사용하면 유지 보수성이 낮아질 가능성이 있기 때문에 리액트 v16.3 부터는 context API를 사용하여 전역 상태를 손쉽게 관리할 수 있다.

![600X480](/assets/img/blog/392_2.jpg)
<br> 일반적인 전역 상태 관리 흐름
{:.figure}

![600X480](/assets/img/blog/392_2.jpg)
<br> Context API를 사용한 전역 상태 관리 흐름
{:.figure}

위 그림처럼 Context를 만들어 한 번에 원라는 값을 받아와서 사용할 수 있다.

## Context API 사용법

```bash
$ yarn create react-app context-tutorial
```

새 Context 만들기
```javascript
//src/contexts/color.js
import React from "react";

const ColorContext = React.createContext({ color: "black" });

export default ColorContext;
```

새 Context를 만들 때는 creactContext 함수를 사용한다. 파라미터에는 해당 Context의 기본 상태를 지정한다.

## Consumer 사용하기

```javascript
//src/components/ColorBox.js
import React from "react";
import ColorContext from "../contexts/color";

const ColorBox = () => {
  return (
    <ColorContext.Consumer>
      {(value) => (
        <div
          style={
            {
              width: "64px",
              height: "64px",
              background: value.color,
            }
          }
        />
      )}
    </ColorContext.Consumer>
  );
};

export default ColorBox;
```

ColorContext안에 들어있는 색상을 보여주는 ColorBox컴포넌트를 만들었다. Context.Consumer는 함수 컴포넌트에서 context의 값을 이용해서 랜더링할 수 있다 Consumer의 value값은 context의 Provider 중 상위 트리에서 가장 가까운 Provider의 value prop과 동일하다. Context.Consumer의 자식은 함수여야 한다. 이러한 패턴을 Funtion as a child 혹은 Render props라고 한다.
컴포넌트의 children이 있어야 할 자리에 JSX,문자열이 아닌 함수를 전달하는것이다.

App에서 랜더링하면 다음과 같은 화면이 나타난다.

![600X480](/assets/img/blog/397.jpg)
<br> 검정색 ColorBox
{:.figure}

## Provider
Provider는 Context의 value를 변경할 수 있다. 또한 context를 구독하는 컴포넌트들에게 context의 변화를 알리는 역할을 한다.<br>

Context.Provider 사용법
```javascript
<MyContext.Provider value={/* 어떤 값 */}>
```

```javascript
//App.js
import React from "react";
import ColorBox from "./contexts/ColorBox";
import ColorContext from "./contexts/color";
const App = () => {
  return(
    <ColorContext.Provider value={ {color:'red'} }>
      <div>
        <ColorBox/>
      </div>
    </ColorContext.Provider>
  );
};

export default App;
```
Provider 컴포넌트는 value prop을 받아서 이 값을 하위에 있는 컴포넌트에게 값을 전달하므로 빨간색 정사각형을 확인할 수 있을 것이다.
Provider를 사용할 때는 value 값을 명시해 주어야 제대로 작동한다.
또한 Provider의 하위에 Provider가 있을 경우 하위 Provider의 값이 우선시된다.

## 동적 Context

```javascript
import { createContext, useState } from "react";

const ColorContext = createContext({
  state: { color: "black", subcolor: "red" },
  actions: {
    setColor: () => {},
    setSubColor: () => {},
  },
});

const ColorProvider = ({ children }) => {
  const [color, setColor] = useState("black");
  const [subcolor, setSubColor] = useState("red");

  const value = {
    state: { color, subcolor },
    actions: { setColor, setSubColor },
  };

  return (
    <ColorContext.Provider value={value}>{children}</ColorContext.Provider>
  );
};

// const ColorConsumer = ColorContext.Consumer와 같은 의미
const { Consumer: ColorConsumer } = ColorContext;

// ColorProvider와 ColorConsumer 내보내기
export { ColorProvider, ColorConsumer };

export default ColorContext;
```
이렇게 하면 Context의 값을 바꿀 수 있다. 상태는 state로 업데이트 함수는 actions로 묶어서 Provider에 value로 전달했다. 
createContext의 기본값은 실제 Provider의 value에 넣는 객체의 형태와 일치시켜 주는 것이 좋다. 그래야 context코드를 볼때 내부 값이 어떻게 구성되어 있는지 파악하기 쉽고 실수로 Provider를 사용하지 않았을 때 리액트 애플리케이션에서 에러가 발생하지 않는다.

## Context 반영

```javascript
//App.js
import React from "react";
import ColorBox from "./contexts/ColorBox";
import {ColorProvider} from "./contexts/color";
const App = () => {
  return(
    <ColorProvider>
      <div>
        <ColorBox/>
      </div>
    </ColorProvider>
  );
};

export default App;
```

```javascript
//components/colorBox.js
import React from "react";
import { ColorConsumer } from "../contexts/color";

const ColorBox = () => {
  return (
    <ColorConsumer>
      {({ state }) => (
        <>
          <div
            style={
              {
                width: "64px",
                height: "64px",
                background: state.color,
              }
            }
          />
          <div
            style={
              {
                width: "32px",
                height: "32px",
                background: state.subcolor,
              }
            }
          />
        </>
      )}
    </ColorConsumer>
  );
};

export default ColorBox;
```
위 코드는 비구조화 할당 문법을 사용하여 value를 조회하는 것을 생략했다.
랜더링 하면 검정색 정사각형과, 밑에 빨간색 정사각형을 확인할 수 있다.

## 색상 선택 컴포넌트 만들기
우선 색상을 선택하는 UI 컴포넌트를 components디렉토리에 생성한다.

```javascript
//components/SelectColors.js
import React from "react";

const colors = ["red", "orange", "yellow", "green", "blue", "indigo", "violet"];

const SelectColors = () => {
  return (
    <div>
      <h2>색상을 선택하세요.</h2>
      <div style={{ display: "flex" }}>
        {colors.map((color) => (
          <div
            key={color}
            style={
              {
                background: color,
                width: 24,
                height: 24,
                cursor: "pointer",
              }
            }
          />
        ))}
      </div>
      <hr />
    </div>
  );
};

export default SelectColors;
```

![600X480](/assets/img/blog/selcColor.png)
<br> SelectColors UI
{:.figure}


이번에는 Context의 actions에 넣어 준 함수를 호출해본다. 

```javascript
//components/SelectColors.js
import React from "react";
import { ColorConsumer } from "../contexts/color";

const colors = ["red", "orange", "yellow", "green", "blue", "indigo", "violet"];

const SelectColors = () => {
  return (
    <div>
      <h2>색상을 선택하세요.</h2>
      <ColorConsumer>
        {({ actions }) => (
          <div style={{ display: "flex" }}>
            {colors.map((color) => (
              <div
                key={color}
                style={{
                  background: color,
                  width: 24,
                  height: 24,
                  cursor: "pointer",
                }}
                onClick={() => actions.setColor(color)}
                onContextMenu={(e) => {
                  e.preventDefault(); // 오른쪽 버튼 클릭 시 메뉴가 뜨는 것을 무시
                  actions.setSubColor(color);
                }}
              />
            ))}
          </div>
        )}
      </ColorConsumer>
      <hr />
    </div>
  );
};

export default SelectColors;
```
왼쪽 마우스 클릭시 actions.setColor 함수 오른쪽 마우스 클릭시 actions.setSubColor함수가 실행되는 코드이다.
onContextMenu 마우스 오른쪽 클릭시 발생하는 이벤트이며 e.preventDefault()를 호출해주어야 브라우저 메뉴가 나타나지 않는다.

## Consumer 대신 Hook 또는 static contextType 사용하기
리액트에 내장되어 있는 Hooks 중에서 useContext라는 Hook을 사용하면 Context를 편하게 사용할 수 있다.

```javascript
//components/SelectColors.js
import React, { useContext } from "react";
import ColorContext from "../contexts/color";

const ColorBox = () => {
    const { state } = useContext(ColorContext);
    return (
            <>
                <div
                    style={{
                    width: "64px",
                    height: "64px",
                    background: state.color,
                    }}
                />
                <div
                    style={{
                    width: "32px",
                    height: "32px",
                    background: state.subcolor,
                    }}
                />
            </>
    );
};

export default ColorBox;
```

클래스형 컴포넌트에서 Context를 좀 더 쉽게 사용하고 싶다면 static contextType을 정의하는 방법이 있다.

```javascript
//components/SelectColors.js
import React, { Component } from "react";
import ColorContext from "../contexts/color";

const colors = ["red", "orange", "yellow", "green", "blue", "indigo", "violet"];

class SelectColors extends Component {
  static contextType = ColorContext;

  handleSetColor = (color) => {
    this.context.actions.setColor(color);
  };
  handleSetSubcolor = (subcolor) => {
    this.context.actions.setSubColor(subcolor);
  };

  render() {
    return (
      <div>
        <h2>색상을 선택하세요.</h2>
        <div style={{ display: "flex" }}>
          {colors.map((color) => (
            <div
              key={color}
              style={{
                background: color,
                width: 24,
                height: 24,
                cursor: "pointer",
              }}
              onClick={() => this.handleSetColor(color)}
              onContextMenu={(e) => {
                e.preventDefault();
                this.handleSetSubcolor(color);
              }}
            />
          ))}
        </div>
        <hr />
      </div>
    );
  }
}

export default SelectColors;
```

static contextType을 정의하면

- 장점 : 클래스 메서드에서도 Context에 넣어 둔 함수를 호출할 수 있다.
- 단점 : 한 클래스에서 하나의 Context밖에 사용하지 못한다.

앞으로 새로운 컴포넌트를 작성할 때 클래스형으로 작성하는 일은 많지 않기 때문에 useContext를 사용하는 것이 좋다.


## 참고 문헌

[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&linkClass=&barcode=9791160508796)