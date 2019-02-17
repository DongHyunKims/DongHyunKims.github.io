---
layout: post
title: "지도 데이터 크롤링"
author: Alex
date: 2019-01-25
categories: javascript
cover:  "/assets/map.jpeg"
comments: true
---
## 지도데이터 크롤링하기

### 1. 개요

지도 위에 제공된 데이터를 정제하여 데이터베이스를 구축해야 하는 일.

### 2. 문제점

chrome network tab에서 확인해보니 데이터는 json 형태로 제공되어 정제만 하면 된다는 생각에 신나 있었다. 하지만 지도위에 데이터를 어떻게 크롤링 할지가 가장 큰 문제였다...
효과적인 크롤링을 실행하기위해 여러가지 방법을 시도해 보았다.

1) 지도영역을 제일 큰 영역으로 잡고 api call을 보낸후 크롤링.

- 영역 반복을 하지 않아도 된다는 장점.
- 너무 큰 영역으로 call을 보내니 해당 사이트의 서버가 버티지 못해 죽는다... 워낙 많은 데이터가 있어 전국에 모든 데이터를 내려보내다보니 죽을수 밖에...

2) 시군구, 읍면동을 선택 후 해당 동에 존재 하는 데이터를 하나씩 크롤링.

- 읍면동 별로 call을 보내게 되면 너무 많은 call을 보내게 되어 해당 데이터를 수집할수 없게 될수도 있다.
- 너무 많은 반복.
- 영역값 추출 필요

3) 클러스터링 되어있지 않는 ZOOM단계에서 우리나라 최동단, 최남단 좌표 값까지 이동하며 해당 영역의 데이터를 크롤링.

- 2번의 시군구, 읍면동 별데이터를 가져오는 것보다 적은 반복.
- 클러스터링 전zoom 단계로 영역만 잘 잡아주고, 큰 화면이 있다면 반복을 더 줄일 수 있다. 한마디로 더 큰영역을 잡을수 있다면 반복을 줄일수 있다.

### 3. 해결방법

1) promise의 반복이 해결되지 않아 function 으로 싸는 방식으로 해결.

promise형식을 while문에서 사용하기 위해 코드를 작성했는데 전달되는 값이 반복문의 마지막 값으로 실행이 되어 promise의 비동기 실행이 되지 않는 문제가 발생하였다. 이러한 문제를 해결하기 위해 **function scope** 을 사용하였다. 자바스크립트에서는 block scope이 존재하지 않기 때문에 block 내부의 값을 외부에서도 사용가능하다. 그렇기 때문에  반복되는 값을  promise의 callback에서 사용하기위해 function scope 만들어 매번 새로운 closure을 만들어주면 해결된다.

~~~ javascript
(function (cnt1, cnt2) {
    let outerPromise = Q.resolve();

    while (cnt1 < 5) {
        (function (cnt2) {
            outerPromise = outerPromise.then(() => {
            return (function (cnt2) {
                let innerPromise = Q.resolve();

                while (cnt2 < 5) {
                    (function () {
                        innerPromise = innerPromise.then(() => {
                        return Q.resolve();
                        });
                    })();

                    cnt2++
                    }
                    return innerPromise;
                })(cnt2);
            });
        })(cnt2);

        cnt1++;
    }
    return outerPromise;
})(cnt1, cnt2)
~~~

2) 시작영역, 끝좌표 입력을 통해 원하는 곳까지만 수집이 가능하도록 함.

실패 하더라도 다시 실행할 수 있도록 시작 영역과 끝좌표를 입력 가능하도록 task를 구성하였다.

### 4. 배운점

- 지도크롤링 방식
- promise 반복문
- 각 gis마다 사용하는 좌표계가 따로 존재한다. 위경도 좌표만 있다면 각각의 gis에서 사용중인 다양한 좌표계로 변환해서 사용할 수 있다.

### 참조
- 좌표계: <https://blog.acronym.co.kr/283>
