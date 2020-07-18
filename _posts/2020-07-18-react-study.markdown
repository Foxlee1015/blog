---
layout: post
title: React - why to use setState/useState 
date: 2020-07-18 00:00:00 +0300
description: React - why to use setState/useState 
tags: [react]
img: react.png
---

### The Wise Guide to React useState() Hook
* [The Wise Guide to React useState() Hook][The Wise Guide to React useState() Hook]
* 상태들은 컴포넌트 내부에 숨겨진 정보들로 컴포넌트는 그 컴포넌트의 부모가 관여없이 상태를 수정할 수 있다.
* 컴포넌트에 상태를 추가하기 위해서는 4가지 스텝을 거친다. enabling the state, initializing, reading and updating.


{% highlight ruby %}

// enabling the state
import React, { useState } from 'react';

function Bulbs() {
  ... = useState(...);                      // useState 를 통해 컴포넌트에 상태를 추가시킴(stateful component) -> 이를 리액트가 인지함***
  return <div className="bulb-off" />;
}

// initializing

function Bulbs() {
  ... = useState(false);                        // initial value added
  return <div className="bulb-off" />;
}

// reading

function Bulbs() {
  const stateArray = useState(false);                // useState returns an array, array[0] = stateValue
  return <div className={stateArray[0] ? 'bulb-on' : 'bulb-off'} />;
}

function Bulbs() {
  const [on] = useState(false);                              // on = fasle
  return <div className={on ? 'bulb-on' : 'bulb-off'} />;
}

// updateing
const [state, setState] = useState(initialState);

setState(newState); // changes state to `newState` and triggers re-rendering***
// after re-render `state` has the value of `newState`


setState(prevState => nextState); // 이전 값을 고려하기
setState(prevState => { 
    nextState = prevState + 1;
    return nextState
})

// use cases
// Toggle a boolean
const [toggled, setToggled] = useState(false);
setToggled(toggled => !toggled);

// Increase a counter
const [count, setCount] = useState(0);
setCount(count => count + 1);

// Add an item to array
const [items, setItems] = useState([]);
setItems(items => [...items, 'New Item']);

{% endhighlight %}

* useState() 는 리액트 함수 안에서만 사용가능하고, loops 나 if 절 또는 nested 함수 안에서는 호출 불가능
* Don’t call Hooks from regular JavaScript functions.

{% highlight ruby %}
function Switch({ isSwitchEnabled }) {
  if (isSwitchEnabled) {  
    //bad
    const [on, setOn] = useState(false);
  }
  // ...
}

function Switch() {
  let on = false;
  let setOn = () => {};

  function enableSwitch() {           // Switch 단계에서 useState 호출해야함, 이벤트 함수가 아닌 실제 on state를 가지는 컴포넌트라면 다름
    // Bad                           
    [on, setOn] = useState(false);    //  Don’t call Hooks from regular JavaScript functions. [참고][참고]
  }

  return (
    <button onClick={enableSwitch}>
      Enable light switch state
    </button>
  );
}

{% endhighlight %}

### Why React setState/useState does not update immediately
* [Why React setState/useState does not update immediately][Why React setState/useState does not update immediately]

* setState/useState 는 그 value를 바로,직접 변경하지 않으며 (리액트 코어를 위해서) 리액트의 상태 object를 업데이트하기 위해  큐를 만든다. 리액트 상태 업데이트 과정은 퍼포먼스를 우해 비동기이며, 그것이 바로 상태가 즉시 변경되지 않는(것 같은 느낌을 받는) 이유이다.


### Why can't I directly modify a component's state, really?
* [Why can't I directly modify a component's state, really?][hy can't I directly modify a component's state, really?]

* [공식문서][공식문서]
    * NEVER mutate this.state directly, as calling setState() afterwards may replace the mutation you made. Treat this.state as if it were immutable.
    * (state를 직접 변경 금지, 왜냐면 나중에 setState, useState를 통해 변경할때 직접 변경한 값으로 바뀔 수 있으므로, 불변한 것으로 취급해라 변경은 this.setState, useState를 통해 변경해라 )
  
    * setState() does not immediately mutate this.state but creates a pending state transition. Accessing this.state after calling this method can potentially return the existing value. There is no guarantee of synchronous operation of calls to setState and calls may be batched for performance gains.
    * (변경은 즉시 이루어지지 않고, 상태 변화 펜딩을 생성하고, setState, useState로 변경 후 바로 로그를 찍으면 이전 값이 나올수도 있음)
  
    * __setState() will always trigger a re-render(***)__ unless conditional rendering logic is implemented in shouldComponentUpdate(). If mutable objects are being used and the logic cannot be implemented in shouldComponentUpdate(), calling setState() only when the new state differs from the previous state will avoid unnecessary re-renders.
    * (조건 렌더링이 없다면 setState, useState 은 리렌더링을 트리거시키고, sate가 그전값과 다를때만 setState를 호출하여 불필요한 리렌더링을 피함)


* this.state에 직접 변경하면 변경사항들이 overwritten 될 수 있음.

* setState() is not immediate. __It queues a state transition based on what it thinks is going on which may not include the direct changes to this.state(***)__. Since it's queued rather than applied immediately, it's entirely possible that something is modified in between such that your direct changes get overwritten.

* If nothing else, you might be better off just considering that not directly modifying this.state can be seen as good practice. You may know personally that your code interacts with React in such a way that these over-writes or other issues can't happen but you're creating a situation where other developers or future updates can suddenly find themselves with weird or subtle issues.


* states are a data store which contains the data of a component.
* The view of a component renders based on the state.
* When the view needs to change something on the screen, that value should be supplied from the store.
* To make this happen, __React provides setState() function which takes in an object of new states and does an compare and merge(similar to object.assign()) over the previous state and adds the new state to the state data store.(***)__
* Whenever the data in the state store changes, react will trigger an re-render with the new state which the view consumes and shows it on the screen.



[The Wise Guide to React useState() Hook]: https://linguinecode.com/post/why-react-setstate-usestate-does-not-update-immediately
[Why React setState/useState does not update immediately]: https://dmitripavlutin.com/react-usestate-hook-guide/
[hy can't I directly modify a component's state, really?]: https://stackoverflow.com/questions/37755997/why-cant-i-directly-modify-a-components-state-really
[참고]: https://reactjs.org/docs/hooks-rules.html
[공식문서]: https://reactjs.org/docs/hooks-intro.html