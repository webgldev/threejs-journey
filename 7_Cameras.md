## 07 Cameras 카메라

- 3js 에서 Camera [( Docs )](https://threejs.org/docs/index.html?q=camera#api/en/cameras/Camera)
- ArrayCamera [( Docs )](https://threejs.org/docs/index.html?q=camera#api/en/cameras/ArrayCamera)
- StereoCamera [( Docs )](https://threejs.org/docs/index.html?q=camera#api/en/cameras/StereoCamera)
- CubeCamera [( Docs )](https://threejs.org/docs/index.html?q=camera#api/en/cameras/CubeCamera)

<hr/>

- PerspectiveCamera 는 등각투영도를 보는 방법중에 하나 입니다. [( Docs )](https://threejs.org/docs/index.html?q=camera#api/en/cameras/PerspectiveCamera)

독스에 있는 예제처럼  
`const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 1, 1000)`  
이렇게 모든 매개변수를 사용하지 않고 Lear과 Far는 빼도됩니다.

> 매개변수에서 `75`는  
앞에서 알아본것처럼 앵글이라서 (Field of view)  
140으로 올려서 `숫자가 커지면` 오브젝트가 더 멀리 보이게됩니다. (`축소`)  
45로 내려서 `숫자가 작아지면` 오브젝트가 가까이 보이게됩니다. (`확대`)

> 매개변수에서 `sizes.width / sizes.height`는  
캔버스 사이즈 가로와 세로를 sizes변수에 미리 정의해주고 나눈것 입니다. [(이미지는 Adobe에서 설명한 Aspect ratio)](https://www.adobe.com/express/discover/sizes/photo-aspect-ratio)
![image](https://user-images.githubusercontent.com/54713067/128593632-cd63fe79-c904-4b2f-98e2-ea0c9b59bc24.png)

> 매개변수에서 `1, 1000`은  
Lear and Far 파라미터인데 극단적인 값을 적용해도 기본화면으로 보입니다.  
>  
> `console.log(camera.position.length())` 를 찍어보면 `1000`과 동일한 값이 출력됩니다.  
출력된 값을 `1000` 자리에 넣으면 똑같은 화면이 보입니다.

- OrthographicCamera 는 등각투영도를 보는 방법중에 하나 입니다. [( Docs )](https://threejs.org/docs/index.html?q=camera#api/en/cameras/OrthographicCamera)

`OrthographicCamera`는 아이소(isometric) 라서  
`left` `right` `top` `bottom` `near` `far` 를 파라미터로 지정해서 볼 수 있습니다.

`const camera = new THREE.OrthographicCamera(-1, 1, 1, -1, 0.1, 100)` 에서  
파라미터에 작성한 (-1, 1, 1, -1, 0.1, 100) 을 자세히 살펴보자면

중심에서 `-1` 만큼 왼쪽(left)으로 시야를 넓히고  
중심에서 `1` 만큼 오른쪽(right)으로 시야를 넓히고  
중심에서 `1` 만큼 위쪽(top)으로 시야를 넓히고  
중심에서 `-1`만큼 아래쪽(bottom)으로 시야를 넓히면  
정사각형을 만들수있고  

중심에서 `0.1`만큼 시야를 가까이(near) 좁히고  
중심에서 `100`만큼 시야를 멀리(far) 넓히면  

아이소 카메라가 적용된 오브젝트가 보이게 됩니다.

![image](https://user-images.githubusercontent.com/54713067/128595054-0555b04f-3679-472e-9333-509ad8977dbe.png)

근데 지금은 오브젝트가 눌려서 찌그러져보이기 때문에  
미리 설정해준 캔버스 사이즈 종횡비를 적용해서 오브젝트를 정사각형으로 보이게 해줍니다.

`const aspectRatio = sizes.width / sizes.height`  
`const camera = new THREE.OrthographicCamera(-1 * aspectRatio, 1 * aspectRatio, 1, -1, 0.1, 100)`

![image](https://user-images.githubusercontent.com/54713067/128595048-878120d5-706e-450e-b931-ce9fa6ca7180.png)

<hr/>

이제 마우스를 따라서 움직이는 카메라를 만들어줄 것입니다.  
앞에서 만들었던 move 함수에 mesh rotation은 지워주고, 카메라에 적용한 포지션도 지워줍니다.

```
camera.position.y = 2
camera.position.x = 2
```

<hr/>

- Cursor

이제 PerspectiveCamera로 다시 설정하고 `mousemove`를 알아볼것입니다.

```
const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height)
camera.position.z = 3
```

`addEventListener()` 이벤트 메서드를 추가하여 `mousemove` 이벤트를 호출 [( Docs )](https://developer.mozilla.org/ko/docs/Web/API/EventTarget/addEventListener)

```
window.addEventListener('mousemove', (e) => {
  console.log(e.clientX, e.clientY)
})
```

`const cursor = { x: 0, y: 0 }` cursor 변수에 x와 y를 넣어서  
mousemove 이벤트안에 추가하고 move에 카메라 포지션을 넣어줄것입니다.

```
window.addEventListener('mousemove', (e) => {
  cursor.x = e.clientX / sizes.width - 0.5 // -0.5를 준 이유는 좌측으로 가면 마이너스 우측으로 가면 플러스로 표시해주기위해서임
  cursor.y = - (e.clientY / sizes.height - 0.5) // 상하에서 마이너스는 마우스를 위로올리면 마이너스, 아래로 내리면 플러스로 나옴
  console.log(cursor.x, cursor.y)
})

const move = () => {
  const elapsedTime = clock.getElapsedTime()
  // Update camera
  camera.position.x = cursor.x * 3 // 커서 반대편으로 3 만큼 좌우로 움직임 (숫자가 커지면 더 멀리감)
  camera.position.y = cursor.y * 3 // 커서 반대편으로 3 만큼 상하로 움직임 (숫자가 커지면 더 멀리감)
  // Render
  renderer.render(scene, camera)
  window.requestAnimationFrame(move)
}
```

이렇게 마우스를 움직이면, 오브젝트가 마우스 반대방향으로 상하좌우로 움직입니다.

https://user-images.githubusercontent.com/54713067/128612122-608cd703-a68b-4a35-81a6-ff6fd670561f.mp4


이제 `mousemove`를 적용했을때 오브젝트가 날아다니지않고 고정시킬것입니다.  
`move`안에 `camera.lookAt` 을 적용해서 공간에 한점을 보게할 것입니다.  
> `camera.lookAt(new THREE.Vector3())` Vector는 기본적으로 0, 0, 0 으로 설정되어 있습니다.

`camera.lookAt(mesh.position)` 을 적용하면 오브젝트가 고정됩니다.

https://user-images.githubusercontent.com/54713067/128613660-4bc188f9-4b3f-4de1-b830-9abd0727590f.mp4

이제 `오브젝트`를 날아다니지않고 가운데 고정시켰지만  
지금은 오브젝트가 `90˚` 밖에 회전안해서 `위아래, 양옆, 앞면`만 보입니다.  
오브젝트의 반대편인 `뒷면`을 보려면 오브젝트가 `360˚` 돌아가야합니다.

먼저 오브젝트를 부드럽게 돌려야하니까 `Math.sin()`과 `Math.cos()` 움직임이 필요하고  
오브젝트를 반대편으로 돌려야하니까 `Math.PI`도 필요합니다.

`y축`  
오브젝트가 가운데 고정된 채로 `360˚` 돌아야하기 때문에 `y축을 고정`시키고  
커서를 위로 올리면 `5`만큼 오브젝트가 아래로 돌아갑니다.  
(숫자가 커질수록 아래쪽으로 더 많이 돌아가고 `1`로 설정하면 그대로 고정되어 있습니다.)

`x축` `z축`  
x축과 z축을 돌아가게할껀데 z축은 카메라쪽(내쪽)을 향해야 합니다.  
절반만 돌리면 90도랑 다를게 없으니까 `Math.PI`에 2를 곱해서 절반을 한번 더 돌려줍니다.  
`3`은 나랑 떨어져있는 거리입니다.  

`camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 3`  
`camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 3`  
`camera.position.y = cursor.y * 5`

https://user-images.githubusercontent.com/54713067/128623664-dc8ed5be-4aaf-407d-a4d1-6309a02155be.mp4

<hr/>

이제 오브젝트 돌아가는건 해봤으니  
마우스로 오브젝트를 드래그해서 움직이게하는 `controls` 에 대해 알아보겠습니다.

> - DeviceOrientationControls [( Docs )](https://threejs.org/docs/index.html?q=controls#examples/en/controls/DeviceOrientationControls)
> - FlyControls [( Docs )](https://threejs.org/docs/index.html?q=controls#examples/en/controls/FlyControls)
> - FirstPersonControls [( Docs )](https://threejs.org/docs/index.html?q=controls#examples/en/controls/FirstPersonControls)
> - OrbitControls [( Docs )](https://threejs.org/docs/index.html?q=controls#examples/en/controls/OrbitControls)
> - TrackballControls [( Docs )](https://threejs.org/docs/index.html?q=controls#examples/en/controls/TrackballControls)
> - TransformControls [( Docs )](https://threejs.org/docs/index.html?q=controls#examples/en/controls/TransformControls)
> - DragControls [( Docs)](https://threejs.org/docs/index.html?q=controls#examples/en/controls/DragControls)


- OrbitControls

위에 여러 컨트롤중에서 `OrbitControls` 를 알아보겠습니다.  
OrbitControls는 임포트해서 사용해야합니다.

`import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'`
> `console.log(OrbitControls)` 콘솔에 찍으면 속성을 볼 수 있습니다.

`const controls = new OrbitControls(camera, canvas)` 을 추가하면  
오브젝트를 클릭해서 드래그할 수 있습니다.

https://user-images.githubusercontent.com/54713067/128626379-9568fe14-53c1-4448-bfdb-aff446049608.mp4

x, y, z 축을 중심에 고정시키려면 `controls.target`을 적용하면 됩니다.

`controls.target.x = 1`  
`controls.update()`

https://user-images.githubusercontent.com/54713067/128626520-8be9bb31-4c3f-402e-93b3-c7708aec167f.mp4

위에는 오브젝트를 클릭하고 드래그(Drag and drop)해서 회전시키면  
오브젝트가 마우스 클릭에 따라서 회전되다가 마우스 클릭을 떼면 회전이 즉시 멈추게됩니다.

- enableDamping

마우스 클릭을 떼었을 때 회전이 바로 멈추지 않고  
천천히 부드럽게 멈추려면 Damping (가속과 마찰)을 추가해줍니다.
`enableDamping` 프로퍼티를 `true`로 설정 해주면 됩니다.

```
const controls = new OrbitControls(camera, canvas)
controls.enableDamping = true
```

그리고 `move` 함수 안에있는 애니메이션에 컨트롤을 업데이트 해줍니다.  
`controls.update()`

https://user-images.githubusercontent.com/54713067/128640813-83b1cb11-df45-44b5-8a68-2adcf9f7241c.mp4


`OrbitControls` 는  
마우스 `좌측 클릭`으로 화면을 `좌(left)/우(right)/상(up)/하(down)` 를 움직일 수 있고  
마우스 `휠`로 `확대(zoom in)/축소(zoom out)` 을 할 수 있고  
마우스 `우측 클릭`으로 `카메라를 드래그(Drag)` 할 수 있습니다.

마우스 좌측 클릭으로 좌/우/상/하 로 움직일때는  
`바닥 밑으로는 내려가지않고, 하늘도 sky view 까지만 볼 수 있습니다.`  
Docs에 있는 [examples](https://threejs.org/examples/#misc_controls_orbit) 를 보면 바로 알수있습니다.


Control은 편리한데 한계가 있으니 사용하기 전에 필요한 기능을 지원하는지 확인해봐야 합니다.


