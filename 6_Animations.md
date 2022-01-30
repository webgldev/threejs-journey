## 06 Animations 애니메이션

화면은 60초 프레임으로 실행되지만 모든 브라우저 화면이 그렇지 않습니다.  
그래서 프레임속도와 상관없이 애니메이션은 동일하게 보여야합니다.

- requestAnimationFrame

`window.requestAnimationFrame(...)` 함수를 사용해서 객체를 업데이트하고 프레임을 업데이트를 할것입니다.

간단히 애니메이션으로 오브젝트를 이동시키려면  

```js
  // 애니메이션 함수를 만들어주고, 화살표함수로 콜백에서 동작하게 하고
  const move = () => {

  // [1] Update objects (오브젝트가 움직일 방향을 정해주고)
  mesh.position.x += 0.01  // 0.01만큼 연속해서 우측으로 이동
  mesh.position.y += 0.01  // 0.01만큼 연속해서 위로 이동
  mesh.rotation.y += 0.01  // 0.01만큼 연속해서 우측으로 회전

  // [2] Render (렌더할 카메라를 작성하고)
  renderer.render(scene, camera)

  // [3] 애니메이션 프레임을 적용해줌
  window.requestAnimationFrame(move)

  // requestAnimationFrame을 적용했기 때문에 콘솔을 찍어보면 메시지가 계속해서 찍히게됨
  // console.log('move')
}
move()  // 실행
```

- Date.now()

애니메이션을 적용하는 방법은 알았으니 이제 프레임속도 조절하는 방법을 알아볼것입니다.
> adaptation to the framerate  
Date.now()를 사용해서 timestamp를 얻을수 있습니다.

`const time = Date.now()` 을 추가해서 `console.log(time)` 콘솔을 찍어보면  
이렇게 `1628278794261` 시간이 매우 길게 찍히는걸 볼수있습니다.

이걸 단축해서 줄이려면

```js
let으로 선언한 time 변수와 콜백안에있는 currentTime 변수는 같은 Date.now()라도 다르게 실행됨

// 애니메이션 밖에 time 변수를 선언해서 한번만 실행되게 해두고
let time = Date.now()

// 애니메이션 안에 
const move = () => {
  const currentTime = Date.now()       // 이건 애니메이션이 적용되어있어서 초단위로 계속 실행됨
  const deltaTime = currentTime - time // 계속실행되는 currentTime과 한번 실행되는 time을 빼서 deltaTime에 넣으면 초단위가 1628278794261 이런식으로 나오지 않고 두자리로 나오게됨
  time = currentTime                   // 계속 실행되는 currentTime을 time에 넣어줌
  console.log(deltaTime)
}
move()
```

초단위를 두자리수로 만들어주고나서 오브젝트에 시간을 적용해주면 됩니다.  
`mesh.rotation.y += 0.0002 * deltaTime` 으로 적용하면 초마다 y축인 우측으로 0.0002씩 회전합니다.

- Clock [( Clock는 3js에 built-in 솔루션 입니다. Docs를 보려면 여기를 클릭 )](https://threejs.org/docs/index.html?q=clock#api/en/core/Clock)

위에 Date.now()는 detaTime으로 바꿔줘야해서 번거로운데  
3js가 제공해주는 Clock 개체에 있는 `getElapsedTime()` 메서드를 사용하면 ms 로 출력해줍니다.  
그럼 `3.088599999904638` 이런식으로 콘솔에 나오게됩니다.

```js
// Clock
const clock = new THREE.Clock()

// Animation
const move = () => {
  // Clock
  const elapsedTime = clock.getElapsedTime()
  console.log(elapsedTime)

  // Update object
   mesh.rotation.y = elapsedTime
}
move()
```

update object에

> [1] 초당 회전을 점진적으로 빠르게  
`mesh.rotation.y += 0.0002 * elapsedTime` 로 회전 각도를 `+=`로 추가해서 clock 시간을 `*`곱하면 처음 각도에서 계속 `+` 플러스가 되면서 시간이 지날수록 값이 커져서 회전각도가 `점점 빠르게 바뀜`

> [2] 초당 회전을 빠르게  
`mesh.rotation.y = elapsedTime * Math.PI * 2` 로  
회전 각도를 `더 빠르게` 돌릴수도 있습니다.

> [3] 초당 일반적인 회전  
초당 점점 빠르게하거나 빠르게 하지 않고 그냥 `일반 회전`을 적용하려면  
`mesh.rotation.y = elapsedTime` 로 시간만 적용해주면됩니다.

마찬가지로 `position`에 시간을 적용해서 오브젝트 위치를 이동시킬수도 있습니다.  
`mesh.position.y = elapsedTime` 초당 y축으로 오브젝트가 `일반 이동`이 적용됨

- Math.sin(), Math.cos()

이번엔 Math.sin()과 Math.cos() 로 오브젝트를 초당 `위아래` `양옆` 으로 움직이게 할 수도 있습니다.  
`sin과 cos를 같이 적용`하면 오브젝트가 `원형을 그리면서` 움직이게 됩니다.

`mesh.position.y = Math.sin(elapsedTime)` sin은 y 포지션이고   
`mesh.position.x = Math.cos(elapsedTime)` cos는 x 포지션입니다.

`sin(x)`는 곡선의 중심에서 시작하고 (이미지는 sin(x)를 구글에 검색한 결과)  

![image](https://user-images.githubusercontent.com/54713067/128570787-f9c273ef-0983-4d40-bd29-6f19e5f0b000.png)

`cos(x)`는 곡선의 윗부분에서 시작합니다. (이미지는 cos(x)를 구글에 검색한 결과)

![image](https://user-images.githubusercontent.com/54713067/128570769-1f3d4810-545a-4c12-a444-508004500b6b.png)

- sin과 cos이 적용되어 원형을 그리면서 움직이는 화면

https://user-images.githubusercontent.com/54713067/128571810-1bf91775-1951-452d-80a9-07081eb29557.mp4

위에는 오브젝트에 포지션을 직접 적용했기 때문에 오브젝트가 이동되는데

```js
mesh.position.y = Math.sin(elapsedTime)
mesh.position.x = Math.cos(elapsedTime)
```

이번엔 카메라에 포지션을 직접 적용해서 오브젝트가 움직이지 않고  
카메라가 회전되도록 적용하고, lookAt을 사용해서 공간에 한점을 보도록 하려합니다.

```js
camera.position.y = Math.sin(elapsedTime)
camera.position.x = Math.cos(elapsedTime)
camera.lookAt(mesh.position)
```

- camera에 `position`만 적용해줬을때 움직임

https://user-images.githubusercontent.com/54713067/128572663-15ea8f97-1763-4d58-9652-4c225970cda0.mp4

- camera에 `lookAt`을 적용했을때 움직임

https://user-images.githubusercontent.com/54713067/128572677-5933d1db-05d6-486a-bba0-7244545a6eb3.mp4


- GSAP [ (JavaScript Animation Library) ](https://greensock.com/docs/)

움직이는걸 더 다양하고 자세히 적용하고싶다면  
GSAP 라이브러리를 사용해서 tweens, timelines 등등 다양한걸 매우 쉽게 적용할수 있습니다.

그래서 GSAP를 설치할것입니다.
`npm install --save gsap@3.5.1`

`script.js`에 gsap를 임포트해줍니다.  
`import gsap from 'gsap'`

Clock 대신에 gsap.to()를 작성해서  
아규먼트에 mesh.position을 지속시간, 딜레이, x축 프로퍼티를 적용해줍니다.  
`gsap.to(mesh.position, { duration: 1, delay: 1, x: 2 })`

다시 되돌아오게 하려면 딜레이 2를 주고 x축을 0으로 제자리로 이동시킵니다.  
`gsap.to(mesh.position, { duration: 1, delay: 2, x: 0 })`

https://user-images.githubusercontent.com/54713067/128574261-1a0f273d-f7a4-4114-9b3a-391731d5315b.mp4

그럼 이제 애니메이션은 gsap로 적용해주고 카메라만 애니메이션 프레임안에 남습니다.

```js
gsap.to(mesh.position, { duration: 1, delay: 1, x: 2 })
gsap.to(mesh.position, { duration: 1, delay: 2, x: 0 })

const move = () => {
  camera.lookAt(mesh.position) // 계속 나오지만 lookAt을 적용했을때랑 적용하지 않았을때 차이가 있습니다.
  renderer.render(scene, camera)
  window.requestAnimationFrame(move)
}
move()
```

