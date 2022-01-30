## 08 Fullscreen and resizing 전체화면, 리사이징


- Fit in the viewport

이전까지는 뷰포트 사이즈를 `const sizes = { width: 800, height: 600 }` 로 설정했었는데  
이젠  `const sizes = { width: window.innerWidth, height: window.innerHeight }` 윈도우에 맞게 수정해 줍니다.

적용하고나면 스크롤이 생기는데 이건 style에서 수정해줍니다.  
`style.css`
```
// css에서 브라우저 유저에이전트 box model 초기화해주고
* { margin: 0; padding: 0; } 

// script.js에서 컨트롤을 `controls.enabled = false` 적용하면
// 오브젝트가 drop down 안되고 브라우저가 drop down이 되어버려서
// 맥(mac)에서 canvas가 위로 살짝 올라가는데, 이건 `html, body {overflow: hidden;}` 적용해주면됩니다.
html, body {overflow: hidden;}

// 포지션 고정해놓고, canvas 위치 잡아주고,
// 오브젝트를 마우스로 드래그할때 아웃라인 생기는것을 없애줍니다.
.webgl { position: fixed; top: 0; left: 0; outline: none; }
```

> 위에 옵션들은 css reset 파일이 따로 없을 경우에 이렇게 따로 설정해주어야 합니다.

지금은 전체화면을 적용해주었지만, 브라우저 사이즈가 바뀔때마다 리사이징은 안돼서  
handle resize 를 적용해줍니다.

```
window.addEventListener('resize', () => {
  // Update sizes
  sizes.width = window.innerWidth
  sizes.height = window.innerHeight
  // Update camera
  camera.aspect = sizes.width / sizes.height
  camera.updateProjectionMatrix()
  // Update renderer
  renderer.setSize(sizes.width, sizes.height)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
  console.log('window has been resized')
})
```

- Pixel ratio 에 대해 알아봅시다.

몇 년 전만 해도 모든 화면은 픽셀 비율이 1이었고 자세히 보면 그 픽셀들을 볼 수 있었습니다.
애플과 같은 제작자들은 2 픽셀 비율의 화면을 만들기 시작했습니다.

픽셀 비율이 2이면 렌더링할 픽셀이 4배 더 많음을 의미합니다.  
픽셀 비율이 3이면 렌더링할 픽셀이 9배 더 많습니다.  
가장 높은 픽셀 비율은 일반적으로 가장 약한 장치인 모바일에서 나타납니다.

현재 픽셀 비율을 얻기 위해 '윈도우'를 사용할 수 있습니다.devicePixelRatio'  
그에 따라 렌더러를 업데이트하려면 'renderer.setPixelRatio()'를 사용할 수 있습니다.)`

![image](https://user-images.githubusercontent.com/54713067/128642671-ca59041f-f2db-4c7e-82d3-9683c068df7a.png)

// 리사이즈 이벤트 콜백 update renderer 부분에 추가

// 디바이스 픽셀에 맞게 비율 조절
.setPixelRatio(Math.min(window.devicePixelRatio, 2))


[3] Handle fullscreen

```
// .fullscreenElement는 사파리에서 프리픽스가 필요해서 else if에 웹킷 추가

window.addEventListener('dblclick', () => {

    const fullscreenElement = document.fullscreenElement || document.webkitFullscreenElement
    if(!fullscreenElement) {

        if(canvas.requestFullscreen() {

          canvas.requestFullscreen()

         } else if(canvas.webkitRequestFullscreen) {

          canvas.webkitRequestFullscreen()

         }
    } else {

        if(document.exitFullscreen) {

          document.exitFullscreen()

         } else if(document.webkitExitFullscreen) {

          document.webkitExitFullscreen()

         }

    }
})
```




