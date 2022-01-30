## 05 Transform objects 오브젝트 변환

Object3D의 프로퍼티는  
`position`  `scale`  `rotation`  `quaternion` 가 있습니다.  
이 프로퍼티들은 matrices에서 컴파일됩니다.

PerspectiveCamera, Mesh

- position
```
x, y, z 포지션을 따로 설정할 수도 있고
mesh.position.x = 0.7
mesh.position.y = - 0.6
mesh.position.z = 1

set으로 한번에 설정할 수도 있습니다.
mesh.position.set(0.7, - 0.6, 1)
```

`AxesHelper` 메서드는 WebGL 지오메트리 `convex geometry`

`Axes helper` 를 추가해서 화면에 축을 만들어서 오브젝트 각도를 조절할것입니다.  
`const axesHelper = new THREE.AxesHelper()`  
`scene.add(axesHelper)`

- scale
```
x, y, z 스케일 따로 설정할 수도 있고
mesh.scale.x = 2
mesh.scale.y = 0.5
mesh.scale.z = 0.5

set으로 한번에 설정할 수 있음
mesh.scale.set(2, 0.5, 0.5)
```

- rotation

`mesh.rotation.y = 3.14159`  
회전을 반만 하는건 3.14를 주면 되지만 JavaScript 내장객체인 Math 를 사용하여  
직접 숫자를 입력하지 않고 Math.PI 프로퍼티로 쉽게 적용할수 있습니다.  
[Math의 더 많은 프로퍼티와 메서드를 더 보려면 Docs로 이동](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Math)

`mesh.rotation.y = Math.PI / 2`  
`mesh.rotation.y = Math.PI * 0.5`  
2로 나누거나 0.5로 곱하거나 둘중에 하나만 계산해도 결과는 똑같이 나옵니다.

`Quaternion` 은 회전각도의 중심축이라서 회전하면 quaternion 의 좌표가 업데이트 됩니다.

![image](https://user-images.githubusercontent.com/54713067/128474335-23ec5795-9561-48f7-9f3b-2a86667ad0fb.png)

> [참고자료 : 3D Camera Movement in Three.js](https://blogs.perficient.com/2020/05/21/3d-camera-movement-in-three-js-i-learned-the-hard-way-so-you-dont-have-to/)

`reorder`는 쿼터니언(quaternion)을 만들고 각도를 설정한다음  
각도가 변경되면 업데이트해줍니다.  
`object.rotation.reorder('yxz')` [3js docs에 있는 reorder](https://threejs.org/docs/#api/en/math/Euler.reorder)

Rotation 에 쿼터니언을 추가하고 오브젝트를 회전시켜줍니다.
```
mesh.rotation.reorder('YXZ')
mesh.rotation.y = Math.PI * 0.25
mesh.rotation.x = Math.PI * 0.25
```

- lookAt [( Docs )](https://threejs.org/docs/#api/en/core/Object3D.lookAt)  
공간의 한 점을 향하도록 개체를 회전시킵니다. (부모가 균일하지 않은 크기면 지원하지 않습니다.)  
`camera.lookAt(new THREE.Vector3(0, 0, 0))`

lookAt의 Verctor3은 기본값인데  
위에서 포지션을 따로 설정해주었기 때문에 mesh에 있는 position을 따를것입니다.  
`camera.lookAt(mesh.position)`

`Object3D`의 프로퍼티인 `position, rotation, quaternion, scale`을  
적절히 결합해서 사용하면 됩니다.

- group  
위에 작성했던 Mesh 오브젝트를 `Group` 으로 묶어주면 아래와 같습니다.

```
그룹으로 묶기 전 코드
const Geometry = new THREE.BoxGeometry(1, 1, 1)
const Material = new THREE.MeshBasicMaterial({ color: '#ff0000' })
const mesh = new THREE.Mesh(Geometry, Material)
scene.add(mesh)
```
```
오브젝트를 그룹으로 묶음
const group = new THREE.Group()
scene.add(group)

레드 큐브를 하나 추가해주고
const cube1 = new THREE.Mesh(
  new THREE.BoxGeometry(1, 1, 1),
  new THREE.MeshBasicMaterial({ color: '#ff0000' })
)
group.add(cube1)

그린 큐브 하나를 더 추가해줌
const cube2 = new THREE.Mesh(
  new THREE.BoxGeometry(1, 1, 1),
  new THREE.MeshBasicMaterial({ color: '#00ff00' })
)
cube2.position.x = - 2
group.add(cube2)

블루 큐브도 하나 더 추가해줌
const cube3 = new THREE.Mesh(
  new THREE.BoxGeometry(1, 1, 1),
  new THREE.MeshBasicMaterial({ color: '#0000ff' })
)
cube3.position.x = 2
group.add(cube3)
```

위에 작성했던 Object3D 프로퍼티들을 연습하고 주석으로 바꿨기때문에  
현재 씬은 정면을 보고 있어서 cube2만 보입니다.  
사실은 cube1과 cube2와 cube3이 합쳐져있습니다.

`cube2.position.x = - 2` 으로 cube2를 x축으로 - 2 만큼 이동시켜서  
cube1은 그대로있고 cube2를 좌측으로 이동시켜서 큐브 두개가 같이 보이게됩니다.  

마찬가지로 cube3도 `cube3.position.x = 2` 우측으로 2 만큼 이동시키면  
큐브 세개가 같이 보이게 됩니다.

그럼 아래 화면처럼 그린, 레드, 블루 순서로 보이게 될 것입니다.

![image](https://user-images.githubusercontent.com/54713067/128490081-864ebeab-21d6-41d8-867d-c7fd2b16e510.png)

각 오브젝트들 포지션도 잡았으니 이제 전체 묶은 그룹에 포지션을 줘서 이동시켜볼것입니다.  
`group.position.y = - 1`  y축으로 - 1 만큼 group을 아래로 내리면 다음과 같은 화면이 보임

![image](https://user-images.githubusercontent.com/54713067/128490417-793e325d-765c-4afe-843f-cbb04a5112e3.png)


마찬가지로 그룹에 scale과 rotation을 그룹에 적용해주면 됩니다.

```
const group = new THREE.Group()
group.position.y = 0.5
group.position.x = 1
group.scale.x = 1.2
group.rotation.y = -10
group.rotation.x = 10
scene.add(group)
```
![image](https://user-images.githubusercontent.com/54713067/128491576-40311d47-b01f-465b-b474-4accb51df6cc.png)


