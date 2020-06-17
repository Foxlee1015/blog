---
layout: post
title: React study
date: 2020-06-17 01:00:00 +0300
description: "React-study"
tags: [react]
img: react.png
---

## useState, useRef
* useState, useRef 를 input의 value 값으로 사용할떄 두 개의 차이점은..
* [참고1][참고1]- rerender ,[참고2][참고2]- comparison,[참고3][참고3]

1. useState
* 비동기
* 값이 변경될떄 해당 상태 값을 가지는 컴포넌트의 리렌더링

2. useRef
* 동기
* Dom 에 직접 연관
* 결국 useRef 는 useState({current: initialValue })[0] 으로 만들어짐


## Ref 사용 예

{% highlight ruby %}

import { useState, useEffect, useRef } from 'react';
 
function App() {
    // Create a ref that we add to the element for which we want to detect outside clicks
    const ref = useRef();

    // State for our modal
    const [isModalOpen, setModalOpen] = useState(false);

    // Call hook passing in the ref and a function to call on outside click
    useOnClickOutside(ref, () => setModalOpen(false));

    return (
        <div> 
            {isModalOpen ? (
                  <div ref={ref}> 👋 Hey, I'm a modal. Click anywhere outside of me to close. </div>
               ) : (
                  <button onClick={() => setModalOpen(true)}>Open Modal</button> 
            )}
        </div> 
        ); 
}

function useOnClickOutside(ref, handler) {

    useEffect( () => {
        const listener = event => {
        // Do nothing if clicking ref's element or descendent elements
        if (!ref.current || ref.current.contains(event.target)) {
            return;
        }
        handler(event);
    };
    document.addEventListener('mousedown', listener);
    document.addEventListener('touchstart', listener);

    return () => {
        document.removeEventListener('mousedown', listener);
        document.removeEventListener('touchstart', listener); };
    },
    // Add ref and handler to effect dependencies
    // It's worth noting that because passed in handler is a new ...
    // ... function on every render that will cause this effect ...
    // ... callback/cleanup to run every render. It's not a big deal ...
    // ... but to optimize you can wrap handler in useCallback before ...
    // ... passing it into this hook.
    [ref, handler]
    );
}

{% endhighlight %}


[참고1]: https://www.codebeast.dev/usestate-vs-useref-re-render-or-not/         
[참고2]: http://quabr.com/60592465/react-usestate-vs-useref
[참고3]: https://twitter.com/dan_abramov/status/1099842565631819776