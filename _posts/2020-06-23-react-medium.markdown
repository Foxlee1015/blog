---
layout: post
title: 'The most important lessons I’ve learned after a year of working with React'
date: 2020-06-23 01:00:00 +0300
description: "The most important lessons I’ve learned after a year of working with React"
tags: [react, medium]
img: medium.jpg
---

* [원본][원본] 을 읽고 간략하게 요약한 글입니다.
  
## 1년동안 리액트를 사용하면서 배운 가장 중요한 부분들...
* 새로운 기술을 시작, 시도할때는 다소 골칫거리가 될 수 있습니다. 많은 튜토리얼와 블로그들, 많은 사람들의 의견들 속에서 더 힘들 수도 있고 또한, 내가 고른 튜토리얼이 과연 시간낭비가 될지 않을지에 대한 고민도 하게 됩니다.  
  * --> 공감되는 부분이네요. 저의 경우에도 항상 새로운 것을 배울 때 튜토리얼을 처음에 많이 참고하게 되는 데 포스트된 시간이 쫌 지난 경우의 예제와 내가 진행하고 있는 환경의 버전 차이 또는 튜토리얼 에러 등으로 예상치 못한 시간 낭비를 하게 되네요.. 그래서 가능하다면 최근에 나온, 버전 확인, 많이 사용되는 예제 등인지 등을 습관적으로 의심을 하게 되는 거 같아요.
  
* 리액트를 시작하기전에는 리액트처럼 생각을 먼저 해야합니다. 그리고 이후에 다른 부분들을 적용 또는 혼합해라.

## 리액트는 진화중이고, 최신 업데이트를 계속 따라가야 합니다.
* 중요한 것은 어떻게 작동되는지, 왜 개발이 되었는지, 어떤 문제가 해결되었고, 어떤 개발이 더 쉽게 만들어 졌는지 배워보세요. (Know how things work and why they are being developed. Learn what problems are being solved and how development is being made easier. It’ll really help you out.)

## 코드를 작은 chucks 로 쪼개는 것을 두려워하지 마라
* 리액트는 컴포넌트 기반이며, 이 컨셉을 최대한 이용해야한다.
* 컴포넌트가 4~5줄이 되더라도 전혀 문제 될게 없으며, 새로운 사람이 투입되었을 때 빠르게 이해할 수 있을 것이다.
* 컴포넌트에 반드시 복잡한 비지니스 로직이 있어야할 필요도 없고, 시각적인 역할만 할 수 있다. 결국, 가독성 증가, 테스팅 효율 증가 등을 가져올 것이다.

{% highlight ruby %}
// isn't this easy to understand?
return (
  [
   <ChangeButton
    onClick={this.changeUserApprovalStatus}
    text="Let’s switch it!"
   />,
   <UserInformation status={status}/> 
  ]
);

{% endhighlight %}

* 아래 예는, properties이 정적이고, pure 컴포넌트이 이고, 사이트의 not found 에러메시지 일뿐이다.

{% highlight ruby %}
import ErrorMessage from './ErrorMessage';
const NotFound = () => (
  <ErrorMessage
    title="Oops! Page not found."
    message="The page you are looking for does not exist!"
    className="test_404-page"
  />
);
{% endhighlight %}

* css 파일을 따로 만들기 싫다면 아래처럼 styled 컴포넌트를 추천한다.

{% highlight ruby %}
const Number = styled.h1`
  font-size: 36px;
  line-height: 40px;
  margin-right: 5px;
  padding: 0px;
`;

<Container>
  <Number>{skipRatePre}</Number>
  <InfoName>Skip Rate</InfoName>
</Container>

{% endhighlight %}

## 더 많은 컴포넌트를 만드는 것에 고민이 될때에는 폴더 구조를 개선하는 것을 추천한다. 
* [fractal structure][fractal structure] 도 참고  


## 기초에만 머무르지 말고 advanced가 되어라
* 이해하기 어려운 또는 충분히 이해하지 못한 것들에 두려워하지만 말고 일단 적용해보고 도전/시도해봐라, 이 과정을 통해 기초를 더 탄탄히 하고 그 기초들이 어려운 부분에서 어떻게 사용되는지 이해할 수 있게 된다.
* 예를 들면
    * Compound Components
    * High Order Components
    * Render Props
    * Smart/Dumb Components
* 위와 같은 다른 패턴들을 직접 느껴보도 왜, 어디에 쓰이는 지 알게 되면 리액트를 사용하기 더 편해질 것이다.
* 개인 프로젝트에만 적용하지 말고 회사에서도 써보고, 동료들이 왜? 라고 당연히 물어볼 것이다. 너의 임무는 적용하고자 하는 결정, 이유 등으로 논리적으로 설득시켜야 한다.
  
## 현재 존재하는 문제들을 해결하고, 더 나아가 개발을 쉽게하고, 클린 코드하되, 너무 복잡하게 만들지 말아라
* 자랑하기 위함이 아닌 실용적이고 현재의 문제의 목적을 수행할 수 있고 이해하기 쉬운 코드를 짜야한다. 예를 들면, 목적을 모르고 다른 사람들이 리덕스를 쓰기 때문에 리덕스 적용을 고려한다면 쓰지 말고, 너 자신의 의견을 가져라. 
* 팀으로써 너의 일 뿐만 아니라 동료의 일도 신경 써라.. 동료의 일을 하는데 하루종일 걸리는 것도 아니고, 1시간이면 끝날 일일 것이다. 


## 리팩토링 
* 너의 코드는 계속 변하게 될것이다. 배움의 자연스러운 과정이며, 결함 또는 에러 없이는 향상될 수 없고, 더 많이 실패할수록 더 쉽게 다시 일어설 수 있다.
* 리팩토링 과정을 거치면
    * 동료에게 어떻게 이게 작동하는 지 설명할 필요가 없으며,
    * 동료에게 왜 이게 안되는 지 설명할 필요가 없으며,
    * 동료들을 위해 버그를 고칠 필요가 없으며,
    * 3주 후에 발견된 버그를 고칠 필요가 없으며,
    * 너가 원하는 걸 할 시간을 가지게 될 것이다.

[원본]: https://medium.com/free-code-camp/mindset-lessons-from-a-year-with-react-1de862421981
[fractal structure]: https://hackernoon.com/fractal-a-react-app-structure-for-infinite-scale-4dab943092af