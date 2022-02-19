## 16 Shadows

[1] Setup

[MeshDepthMaterial](https://threejs.org/docs/index.html#api/en/materials/MeshDepthMaterial)은 Depth는 카메라 근거리 및 원거리 평면을 기반으로 합니다. 가까울수록 흰색이고, 멀어질수록 검정색으로 보여집니다.

Directional, Point, Spot 라이트 세개와 오브젝트 세개 화면에 있을 경우

카메라를 스폿라이트 와 같은 방향에 배치해두면

스폿라이트를 받는 지오메트리에 그림자가 형성되는것을 볼수있고,

그걸 카메라에 shadow map으로 보여지게 됩니다.

![image](https://user-images.githubusercontent.com/54713067/153741448-3cf9575c-8d5d-4212-aafd-97e86e4255a8.png)

쉐도우맵은 [여기서](https://threejs.org/examples/webgl_shadowmap_viewer.html) 직접 라이트 방향에 따라 쉐도우맵이 그려지는 방향을 볼 수 있습니다.

***

[2] How to activate shadows

쉐도우맵을 렌더러에 활성화 시켜줘야 그림자가 보입니다.

```js
const renderer = new THREE.WebGLRenderer({
  canvas: canvas
})
renderer.setSize(sizes.width, sizes.height)
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
// true로 쉐도우맵 활성화
renderer.shadowMap.enabled = true
```



그리고 지오메트리에도 그림자를 만들어주고, 비춰서 받을수 있게 설정해주고, 라이팅에도 쉐도우를 활성화시켜줍니다.
```js
// 원형에 그림자 만들어주기
const shpere = new THREE.Mesh(
  new THREE.SphereBufferGeometry(0.5, 32, 32),
  material
)
sphere.castShadow = true

// 바닥에 그림자 받을수 있게 설정해주기
const plane = new THREE.Mesh(
  new THREE.PlaneBufferGeometry(5, 5),
  material
)
plane.receiveShadow = true

// 라이팅에도 쉐도우 설정해주기
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5)
directionalLight.castShadow = true
console.log(directionalLight.shadow)
```

> 설정 전 : 쉐도우가 없음  
> 설정 후 : 쉐도우가 있음
![image](https://user-images.githubusercontent.com/54713067/153743590-706ec4dd-1ee0-4746-941d-e0c54485f862.png)


[3] Render size

- 쉐도우맵의 사이즈는 512x512 입니다. mipmap은 2의 거듭제곱으로 계산하게 됩니다.

> 설정 전 기본 그림자
![image](https://user-images.githubusercontent.com/54713067/153744500-f8bc628e-1506-497b-94ec-1d1b54f2bc42.png)

> 4로 나누었을 때 그림자
```js
directionalLight.shadow.mapSize.width  = 1024 / 4
directionalLight.shadow.mapSize.height = 1024 / 4
```
![image](https://user-images.githubusercontent.com/54713067/153744486-bdf166ed-4c8a-483d-bda6-2029c2595693.png)

> 2로 곱했을 때 그림자
```js
directionalLight.shadow.mapSize.width  = 1024 * 2
directionalLight.shadow.mapSize.height = 1024 * 2
```
![image](https://user-images.githubusercontent.com/54713067/153744472-d3da4f9e-4638-4aca-a4f3-6b8086a70ced.png)


[4] Near and far

3js는 카메라를 통해 쉐이더맵을 렌더링합니다.

CameraHelper를 씬에 추가해서 directionalLight의 방향을 보려합니다.

[DirectionalLightShadow(Docs)](https://threejs.org/docs/index.html?q=shadow#api/en/lights/shadows/DirectionalLightShadow)

```js
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5)
directionalLight.position.set(2, 2, -1)
scene.add(directionalLight)

// 쉐도우맵 키고
directionalLight.castShadow = true
// 라이팅박스 사이즈 지정하고
directionalLight.shadow.mapSize.width = 1024
directionalLight.shadow.mapSize.height = 1024

// 카메라헬퍼를 추가합니다.
const directionalLightCameraHelper = new THREE.CameraHelper(directionalLight.shadow.camera)
scene.add(directionalLightCameraHelper)
```

위에 헬퍼를 추가하고나면 이렇게 라이팅 헬퍼를 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/54713067/154716380-d0875bcd-d0ff-445e-b4fd-944dc2895402.png)


[5] Amplitude

아래처럼 카메라의 최소거리와 최대 거리를 지정할수도 있습니다.

```js
// 카메라의 가까운 거리를 설정
directionalLight.shadow.camera.near = 1
// 카메라의 먼 거리를 설정
directionalLight.shadow.camera.far = 6
```

이렇게 라이팅 거리를 지정하는 이유는 그림자가 갑자기 짤리는 경우를 대비해서 라이팅 박스를 만들어주는것입니다.

far 값이 작을 경우에 그림자가 일부분 잘리는 화면
![image](https://user-images.githubusercontent.com/54713067/154721336-b5f82840-2bf7-46cd-b9e2-c53c28bbc6c7.png)

far 값이 큰 경우에 그림자가 다 나오는 화면
![image](https://user-images.githubusercontent.com/54713067/154718640-aedcf44b-a20d-4140-af03-dae20b913dd7.png)

더 부드럽게 그림자가 나오기 위해서 라이팅의 위치를 옮기려면  
top, right, bottom, left 값으로 위치를 조절하면 됩니다.

```js
directionalLight.shadow.camera.top = 2
directionalLight.shadow.camera.right = 2
directionalLight.shadow.camera.bottom = - 2
directionalLight.shadow.camera.left = - 2
```

[6] Blur

이제 그림자가 좀 더 자연스럽게 나오도록 블러를 적용해봅시다.  
블러는 radius 프로퍼티에 원하는 값을주면 됩니다.

```js
directionalLight.shadow.radius = 10
```

[7] Shadow map algorithm

위에서 살펴본 DirectionalLight 외에 다른 쉐도우 맵도 알아봅시다.

```js
THREE.BasicShadowMap 퀄리티는 조금 떨어지지만 성능은 좋은 쉐도우맵 입니다.
THREE.PCFShadowMap 기본 쉐도우로 지정되어 있습니다.
THREE.PCFSoftShadowMap radius 속성이 적용되지 않을 수 있습니다.
THREE.VSMShadowMap Less performant, more constraints, can have unexpected results
```

쉐도우맵을 보려면 렌더러에 추가해주어야 합니다.

```js
// 렌더에 쉐도우맵을 보이게 해주고
renderer.shadowMap.enabled = true
// PCFSoftShadowMap을 적용해주면
// radius 속성이 적용되지 않는 화면을 볼 수 있습니다.
renderer.shadowMap.type = THREE.PCFSoftShadowMap
```

radius 속성이 적용되지 않는 화면

![image](https://user-images.githubusercontent.com/54713067/154726597-8104b32b-18b3-4514-a919-f24389c0181c.png)

다시 radius 속성을 주석처리하면 이렇게 나옵니다.

![image](https://user-images.githubusercontent.com/54713067/154726710-5ef32c17-85f9-4474-b694-aa55931694b1.png)


[8] SpotLight


```js
// 스폿라이트를 추가하고
const spotLight = new THREE.SpotLight(0xffffff, 0.4, 10, Math.PI * 0.3)
spotLight.castShadow = true
spotLight.getWorldPosition.set(0, 2, 2)
scene.add(spotLight)
scene.add(spotLight.target)

// 스폿라이트 헬퍼도 추가해봅시다.
const spotLightCameraHelper = new THREE.CameraHelper(spotLight.shadow.camera)
scene.add(spotLightCameraHelper)
```

![image](https://user-images.githubusercontent.com/54713067/154727224-53810b8d-cb80-4389-a897-11acc42d5803.png)

```js
// 스폿라이트의 사이즈를 조절해서 그림자를 조금 더 부드럽게 만들어줄수도 있습니다.
spotLight.shadow.mapSize.width = 1024
spotLight.shadow.mapSize.height = 1024
// 3js는 기본적으로 perspective 카메라를 사용하기 때문에 스폿라이트의 각도도 perspective로 보여지게 됩니다.
// fov 프로퍼티 값을 주면, 스폿라이트의 범위를 줄일 수 있습니다.
spotLight.shadow.camera.fov = 30
// 스폿라이트의 길이 범위도 지정해줄수 있습니다.
spotLight.shadow.camera.near = 1
spotLight.shadow.camera.far = 6
```

스폿라이트의 사이즈, 범위, 길이가 적용된 화면 입니다.

![image](https://user-images.githubusercontent.com/54713067/154729014-d2264714-9706-4c67-bfde-61fc5994a1f0.png)


[9] PointLight

```js
// 포인트 라이트를 추가하고
const pointLight = new THREE.PointLight(0xffffff, 0.3)
pointLight.castShadow = true
// 사이즈
pointLight.shadow.mapSize.width = 1024
pointLight.shadow.mapSize.height = 1024
// 범위
pointLight.shadow.camera.near = 1
pointLight.shadow.camera.far = 5
pointLight.position.set(-1, 1, 0)
scene.add(pointLight)

// 포인트라이트 헬퍼도 추가해봅시다.
const pointLightCameraHelper = new THREE.CameraHelper(pointLight.shadow.camera)
pointLightCameraHelper.visible = false // 헬퍼 안보이게하려면 visible false로 적용
scene.add(pointLightCameraHelper)
```

![image](https://user-images.githubusercontent.com/54713067/154729950-26fc9ffa-295d-4283-b3c9-67bce66119e8.png)

[9] Baking shadows

전체 쉐도우를 끄려면 렌더러에서 false로 설정하시면 됩니다.

```js
renderer.shadowMap.enabled = false
```

쉐도우를 끄고 [TextureLoader](https://threejs.org/docs/index.html#api/en/loaders/TextureLoader)로 텍스처 쉐도우를 입혀보겠습니다.

![image](https://user-images.githubusercontent.com/54713067/154730776-1c0955d9-0913-44c1-b746-83e2e971d3db.png)

텍스처는 위에 미리 준비해둔 jpg 파일로 입힐 예정입니다.

```js
// 텍스처를 불러와서
const textureLoader = new THREE.TextureLoader()
const bakedShadow = textureLoader.load('/textures/bakedShadow.jpg')

// 플랜 지오메트리에 입혀줍니다.
const plane = new THREE.Mesh(
  new THREE.PlaneGeometry(5, 5),
  new THREE.MeshBasicMaterial({
    map: bakedShadow
  })
)
```

텍스처 그림자가 입혀진 화면 입니다. 라이팅으로 쉐도우를 만들었을때보다 훨씬 자연스럽고 부드러워 보입니다.
대신에 빛에 따른 쉐도우가 아닌, 텍스처 이미지라서 지오메트리가 움직여도 그림자는 고정되어 있습니다.

![image](https://user-images.githubusercontent.com/54713067/154731193-7af87205-a3f4-440c-a601-4b20fb5ef137.png)


[10] Baking shadows alternative

애니메이션을 적용했을때에도 자연스러운 쉐도우가 나오기 위해서  
좀 더 쉬운 알파맵 쉐도우 텍스처를 적용해보겠습니다.

여기서 하얀색 부분은 그림자로 보여지고 검은색 부분은 보이지 않게 적용해줄 것입니다.

![image](https://user-images.githubusercontent.com/54713067/154732075-71625b91-5fd2-426f-8b4e-52cdefc1be5c.png)

```js
// 위에 쉐도우 텍스처를 불러와서
const simpleShadow = textureLoader.load('/textures/simpleShadow.jpg')

// 플랜 지오메트리를 하나 만들고
const plane = new THREE.Mesh(
    new THREE.PlaneGeometry(5, 5),
    material
)

// 알파맵 쉐도우 텍스처를 만들어줍니다.
const sphereShadow = new THREE.Mesh(
    new THREE.PlaneGeometry(1.5, 1.5), // 텍스처 그림자 사이즈
    new THREE.MeshBasicMaterial({
        color: 0x000000, // 쉐도우 컬러는 블랙으로 적용
        transparent: true, // 알파맵이라서 투명을 적용
        alphaMap: simpleShadow // 불러온 텍스처 적용
    })
)
sphereShadow.rotation.x = - Math.PI * 0.5
sphereShadow.position.y = plane.position.y + 0.01 // 텍스처를 바닥에서 살짝 띄워줍니다.

scene.add(sphere, sphereShadow, plane)
```

쉐도우가 공에 잘 붙어있는지 테스트하기위해서 애니메이션을 적용해봅시다.

```js
const clock = new THREE.Clock()
const tick = () => {
  const elapsedTime = clock.getElapsedTime()

  // 애니메이션 방향 추가
  sphere.position.x = Math.cos(elapsedTime) * 1.5
  sphere.position.z = Math.sin(elapsedTime) * 1.5
  sphere.position.y = Math.abs(Math.sin(elapsedTime * 3)) // abs로 바운싱 추가

  // 위에 추가한 알파맵 쉐도우 텍스처 추가
  sphereShadow.position.x = sphere.position.x
  sphereShadow.position.z = sphere.position.z
  // 지오메트리가 바닥에 가까울수록 진해지는데 조금 연하게 적용
  sphereShadow.material.opacity = (1 - sphere.position.y) * 0.3
}

tick()
```

애니메이션을 따라서 그림자가 지오메트리에 잘 적용된 화면입니다.

![2022-02-19 02;37;06](https://user-images.githubusercontent.com/54713067/154733901-3d773931-c840-4d9e-ba04-7f3f291f8271.gif)

여기까지 여러 라이팅에 따른 그림자와  
그림자를 베이킹하는 방법까지 알아보았습니다.
