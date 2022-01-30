## 09 Geometries 다양한 지오메트리 도형들

3js에서 기본으로 제공해주는 지오메트리를 알아봅시다.

[1] built-in geometries

- 지오메트리

Position, UV, Normal

`vertices`  
포지션보다 더 많은 데이터를 저장할 수 있습니다.  
(UV coordinates, Normals, colors or anything we want)

- BufferGeometry의 속성들  
rotateX, rotateY, rotateZ, scale, translate 등등등

- Built in Geometry (지오메트리 예제를 보려면 [ Docs ](https://threejs.org/docs/index.html?q=geometry#api/en/geometries/BoxGeometry) 에서 직접 확인)

> - 기본으로 제공하는 Geometry  
BoxGeometry, PlaneGeometry, CircleGeometry, ConeGeometry, CylinderGeometry
RingGeometry, TorusGeometry, TorusKnotGeometry, DodecahedronGeometry
EdgesGeometry, ExtrudeGeometry, IcosahedronGeometry, LatheGeometry, OctahedronGeometry
ParametricGeometry, PolyhedronGeometry, ShapeGeometry, SphereGeometry
TetrahedronGeometry, TextGeometry, TubeGeometry, WireframeGeometry

[2] BoxGeometry

- 지오메트리에 적용가능한 파라미터는 6가지 Parameters가 있습니다. (axis & segments)  

`width` The size on the `x` axis  
`height` The size on the `y` axis  
`depth` The size on the `z` axis  
`widthSegments` how many subdivisions in the `x` axis  
`heightSegments` how many subdivisions in the `y` axis  
`depthSegments` how many subdivisions in the `z` axis

위에서 알아본것처럼 버텍스(vertices, vertex)는 세점을 연결해서 면(face)을 생성하기에  
기본 사각형 박스 지오메트리를 보면 `new THREE.BoxGeometry(1, 1, 1)` 삼각형 면  
두개가 붙어서 사각형 형태로 되어있는 것을 볼 수 있습니다.

https://user-images.githubusercontent.com/54713067/128644972-3251995c-d27b-4769-8f47-3bff3f2e4363.mp4


여기에서 `new THREE.BoxGeometry(1, 1, 1, 2, 2, 2),` 삼각면에 절반을 더 나누면 (subdivisions) 면이 더 나누어집니다. (숫자가 커질수록 면이 더 나눠집니다.)
fill로 칠해져있으면 나누어져있는게 안보이니 와이어프레임으로 보시면 확인됩니다.

```js
const mesh = new THREE.Mesh(
  new THREE.BoxGeometry(1, 1, 1, 2, 2, 2),
  new THREE.MeshBasicMaterial({
    color: '#ff0000',
    wireframe: true
  })
)
scene.add(mesh)
```

https://user-images.githubusercontent.com/54713067/128644942-c64797b4-9041-4307-8d99-17521d5e1d65.mp4

<hr/>

- 버퍼 지오메트리에 정점 추가하기

new Float32Array(9)

지오메트리를 만들기 전에 버퍼 지오메트리 데이터를 이해해야 합니다.

Float32Array를 그림으로 이해해도 되는데 그림만봐서는 이해불가능하니 코드로 보면됩니다.

![image](https://user-images.githubusercontent.com/54713067/128646192-e28d2909-8382-46c6-8402-6d392e04dce9.png)

```js
const positionArray = new Float32Array(9)

// Float32Array(9)에서 9는 x, y, z로 그린 삼각형 3개
// length 지정하고 array 채우기 (Specify a length and then fill the array)

// First vertex
// x는 positionArray[0] = 0
// y는 positionArray[1] = 0
// z는 positionArray[2] = 0

// Second vertex
// x는 positionArray[3] = 0
// y는 positionArray[4] = 1
// z는 positionArray[5] = 0

// x는 positionArray[6] = 1
// y는 positionArray[7] = 0
// z는 positionArray[8] = 0
```

위에 코드를 간단하게 작성하면 아래와 같습니다.

```js
const positionArray = new Float32Array([
  0, 0, 0,  // First vertex
  0, 1, 0,  // Second vertex
  1, 0, 0   // Third vertex
])
```

[3] BufferGeometry

버텍스 점 찍었으니까 이제 버퍼를 연결해서 삼각형을 만들어보겠습니다.

```js
const positionAttribute = new THREE.BufferAttribute(positionArray, 3) // 버텍스 연결해서
const geometry = new THREE.BufferGeometry() // 버퍼 지오메트리 만들고
geometry.setAttribute('position', positionAttribute) // 포지션 안넣어주면 화면에 안보입니다.
```

점 세개 연결해서 삼각형 만들면 아래 화면과 같습니다.

https://user-images.githubusercontent.com/54713067/128646635-e95ca30a-02a7-4a87-aa82-c9c24c8a6979.mp4

삼각형을 랜덤으로도 만들수있습니다.

```js
const geometry = new THREE.BufferGeometry()

const count = 100  // 100개를 랜덤으로 만들거임
const positionsArray = new Float32Array(count * 3 * 3)  // count는 랜덤숫자 * 3은 vertex이고 * 3은 value입니다.
for(let i = 0; i < count * 3 * 3; i++) { // 포문으로 랜덤하게 만들어줄것입니다.
  positionsArray[i] = (Math.random() - 0.5) * 4 // -0.5를 해야 xyz 중심에서부터 랜덤으로 뿌려짐 * 4는 숫자가 커질수록 카메라에 가까이 확대됩니다.
}

const positionsAttribute = new THREE.BufferAttribute(positionsArray, 3)  
geometry.setAttribute('position', positionsAttribute)

const material = new THREE.MeshBasicMaterial({
  color: '#ff0000',
  wireframe: true
})

const mesh = new THREE.Mesh(geometry, material)
scene.add(mesh)
```

삼각형 100개를 랜덤으로 만든 화면 입니다. (새로고침하면 랜덤으로 뿌려집니다.)

https://user-images.githubusercontent.com/54713067/128648467-3cf890bb-f11d-4062-83b2-6983c9421a6b.mp4


> - BufferGeometry [( Docs )](https://threejs.org/docs/index.html?q=geometry#api/en/core/BufferGeometry)
> 
> `BufferGeometry` 로 버텍스를 재사용해서 다른 삼각형과 연결할 수도 있다고 합니다.  
여기서는 다루지 않을거라서 나중에 쓸때 독스에서 읽어보면 됩니다.
> 




