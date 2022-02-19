### 20 Raycaster

광선 효과를 클릭이벤트로 만들어봅시다.

1. 벽이 앞에 있는지 먼저 감지하고
2. 레이저가 뭔가 맞았는지 테스트하고
3. 마우스이벤트를 시뮬레이션 하기위해 마우스 아래에 무언가 있는지 테스트하고
4. 우주선이 행성으로 향할 경우 경고메세지를 표시해줍니다.


[1] Setup

먼저 3개의 공을 만들고 레이저를 쏜다음 가운데를 통과하는지 테스트해줍니다.

![image](https://user-images.githubusercontent.com/54713067/154797033-005686d0-1a8c-4b28-bea8-bd3dc5dcc32e.png)

```js
const object1 = new THREE.Mesh(
  new THREE.SphereBufferGeometry(0.5, 16, 16),
  new THREE.MeshBasicMaterial({ color: '#ff0000' })
)
object1.position.x = -2
const object2 = new THREE.Mesh(
  new THREE.SphereBufferGeometry(0.5, 16, 16),
  new THREE.MeshBasicMaterial({ color: '#ff0000' })
)
const object3 = new THREE.Mesh(
  new THREE.SphereBufferGeometry(0.5, 16, 16),
  new THREE.MeshBasicMaterial({ color: '#ff0000' })
)
object3.position.x = 2
scene.add(object1, object2, object3)
```

[2] Create the Raycaster

이제 레이캐스터를 추가해봅시다.

```js
const raycaster = new THREE.Raycaster()
const rayOrigin = new THREE.Vector3(-3, 0, 0)
const rayDirection = new THREE.Vector3(10, 2, 0.75)
console.log(rayDirection.length()) // 10.488088481701515

// vector3 를 단위 벡터로 변환시켜줍니다. (unit vector) 길이를 1로 변환
rayDirection.normalize()
console.log(rayDirection.length()) // 1

// raycaster로 셋팅
raycaster.set(rayOrigin, rayDirection)
```

- normalize()[(Docs)](https://threejs.org/docs/index.html?q=vect#api/en/math/Vector3)

[3] Cast a ray

공을 한개 통과하는 광선과 공 세개를 전부 통과하는 광선을 만들어줍니다.

```js
const intersect = raycaster.intersectObject(object2)
console.log(intersect)

const intersects = raycaster.intersectObjects([object1, object2, object3])
console.log(intersects)
```

콘솔에 찍어보면 배열이 반환되어 있습니다.

[4] Result of an intersection

광선을 쏘고 반환된 배열에는 distance, face, faceIndex, object, point, uv 정보가 포함되어 있습니다.

> - distance: 광선점과 충돌점 사이 거리
> - face: 광선에 맞은 지오메트리의 면
> - faceIndex: 지오메트리 면의 인덱스
> - object: 충돌과 관련된 객체
> - point: xyz 3d공간에서 충돌한 Vector3의 위치
> - uv: 지오메트리의 UV 좌표

![image](https://user-images.githubusercontent.com/54713067/154799530-1d830a20-9e52-48d4-8275-2522f82dbcf7.png)

[5] Test on each frame

이제 애니메이션에 레이캐스터를 추가하고 공이 움직일동안 프레임에서 테스트해줍니다.

광선이 움직이는 공에 교차될때 공을 파란색으로 바꿔보겠습니다.

```js
// 레이캐스터 추가하고
const raycaster = new THREE.Raycaster()
// 애니메이션에 레이캐스터 추가
const clock = new THREE.Clock()
const tick = () => {
  const elapsedTime = clock.getElapsedTime()

  // 위아래 방향으로 움직이는 애니메이션 추가 ( sin(시간*스피드) )
  object1.position.y = Math.sin(elapsedTime * 0.3) * 1.5
  object2.position.y = Math.sin(elapsedTime * 0.8) * 1.5
  object3.position.y = Math.sin(elapsedTime * 1.4) * 1.5

  // 레이캐스터 추가
  const rayOrigin = new THREE.Vector3(-3, 0, 0)
  const rayDirection = new THREE.Vector3(1, 0, 0)
  rayDirection.normalize() // 길이를 1로 변환시킴
  raycaster.set(rayOrigin, rayDirection)

  // 테스트할 객체를 배열에 넣어서 테스트 추가
  const objectsToTest = [object1, object2, object3]
  const intersects = raycaster.intersectObjects(objectsToTest)
  // 공이 움직일때마다 숫자가 올라가서 몇번째 공이 얼만큼 움직이는지 알수있습니다.
  console.log(intersects.length)

  // 교차하는 순간에 레드공을 블루공으로 변경해줍니다.
  // 먼저 공을 모두 레드로 만들어주고
  for(const object of objectsToTest) {
    object.material.color.set('#ff0000')
  }
  // 교차되는 순간에 블루공으로 바꿔줍니다.
  for(const intersect of intersects) {
    intersect.object.material.color.set('#0000ff')
  }
}
```
![2022-02-19 21;21;49](https://user-images.githubusercontent.com/54713067/154800555-92918b3c-a350-466a-aead-8c174b3b989a.gif)


[6] Use the raycaster with the mouse & Hovering

이제 광선이 움직이는 공을 통과했을때와  
움직이는 공을 마우스를 호버했을때 컬러를 변경해보겠습니다.

마우스 좌표는 픽셀좌표 대신 방향에따라 +1 과 -1로 표시할겁니다.

예를들면 이렇게 표시됩니다.  
> 마우스가 페이지의 왼쪽 위에 있다면 -1 / 1  
> 마우스가 페이지의 왼쪽 아래에 있다면 -1 / -1  
> 마우스가 세로 방향으로 가운데에 있고, 가로 방향으로 우측에 있다면 1 / 0  
> 마우스가 페이지의 정 중앙에 있다면 0 / 0

```js
// vector2 로 마우스 변수를 하나 만들어주고
// xy 좌표를 알아야 해서 벡터2를 추가해줍니다.
const mouse = new THREE.Vector2()
// 마우스가 움직일 때마다 변수를 업데이트해줍니다.
// 근데 이 방식을 사용하면 브라우저가 이벤트 프레임 속도보다 더 많이 트리거되므로
// mousemove는 권장하지 않습니다.
window.addEventListener('mousemove', (event) => {
  // 마우스를 좌측으로 움직이면 -0이 나오고 우측은 0이 나옵니다.
  mouse.x = event.clientX / sizes.width * 2 - 1
  // 마우스를 위쪽으로 움직이면 0이 나오고 아래쪽은 -0이 나옵니다.
  mouse.y = - (event.clientY / sizes.height) * 2 + 1
  console.log(mouse)
})
// 대신에 애니메이션 함수에서 레이캐스터에 setFromCamera를 추가해줍니다.
const tick = () => {
  raycaster.setFromCamera(mouse, camera)

  const objectsToTest = [object1, object2, object3]
  const intersects = raycaster.intersectObjects(objectsToTest)

  for(const intersect of intersects) {
    intersect.object.material.color.set('#0000ff')
  }
  for(const object of objectsToTest) {
    if(!intersects.find(intersect => intersect.object === object)) {
      object.material.color.set('#ff0000')
    }
  }
}
```

이제 공에 호버를 했을 때 컬러가 변경되고
카메라의 방향을 바꿔도 공의 컬러가 변경되는 것을 볼수있습니다.

![2022-02-19 22;09;32](https://user-images.githubusercontent.com/54713067/154802152-e1929e61-bddd-4e78-bca6-c9ac8540caae.gif)

[7] Mouse enter and mouse leave events

mouseenter와 mouseleave 같은 마우스이벤트는 지원하지 않아서 알림을 직접 설정해줘야합니다.

현재 교차하는 객체를 저장해주기면 하면 됩니다.

```js
// cuttenrIntersect 변수를 만들고 테스트하고 업뎃해주면 됩니다.
let currentIntersect = null

const tick = () => {
  raycaster.setFromCamera(mouse, camera)
  const objectsToTest = [object1, object2, object3]
  const intersects = raycaster.intersectObjects(objectsToTest)
  // 마우스 호버시 mouse enter
  if(intersects.length) {
    if(!currentIntersect) { console.log('mouse enter') }
    currentIntersect = intersects[0]
  }
  // 마우스를 떼면 mouse leave
  else {
    if(currentIntersect) { console.log('mouse leave') }
    currentIntersect = null
  }
}
```

[8] Mouse click event

마우스를 움직이는 공에 올렸다가 내렸을 때 이벤트를 주었으니  
이젠 마우스를 클릭했을 때 이벤트를 주겠습니다.

먼저 호버링 한다음 클릭해야하니 위에 호버링 변수에 click 이벤트를 추가해주면 됩니다.

클릭이벤트는 위치랑 상관없이 윈도우로 추가해줍니다.

```js
window.addEventListener('click', () => {
  // case문으로 공 123을 클릭했을 때
  // 콘솔에 클릭되었다는 메세지를 출력해줍니다.
  if(currentIntersect) {
    switch(currentIntersect.object) {
      case object1:
        console.log('click on object 1')
        break

      case object2:
        console.log('click on object 2')
        break

      case object3:
        console.log('click on object 3')
        break
  }
}
})
```