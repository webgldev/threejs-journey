## 15 Lights

[1] setup

이번시간에는 지오메트리에 빛을 반사하는 머테리얼을 입히고 여러 조명을 테스트할 것입니다.

라이팅을 알아보기 전에 기본적인 내용을 간단히 설정해줍니다.

- 지오메트리

지오메트리는 4개를 배치해줍니다.

sphere, cube, torus, plane (바닥에 깔아줄 오브젝트)

- 머테리얼

머테리얼은 `MeshStandardMaterial` 로 적용해줍니다.

- 거칠기

지오메트리에 거칠기가 높으면 빛이 제대로 반사되는지 확인할 수 없기 때문에

거칠기는(roughness) 0.4로 낮춰서 적용해줍니다.

[2] ambientLight [(Docs)](https://threejs.org/docs/#api/en/lights/AmbientLight)

```js
// 첫번째 파라미터는 color, 두번째 파라미터는 intensity 입니다.
// ambientLight.color = new THREE.Color(0xffffff)
// ambientLight.intensity = 0.5
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
scene.add(ambientLight)

// intensity를 직접 조절 가능하도록 디버그를 추가해줍니다.
// 라이팅을 선택해주고, intensity 이름을 적고, 최소와 최대 그리고 범위 스탭을 적용
gui.add(ambientLight, 'intensity').min(0).max(1).step(0.01)

```

AmbientLight는 오브젝트가 굴절을 갖고있어도 표면을 전체적으로 반사시킵니다.

![image](https://user-images.githubusercontent.com/54713067/153725831-d8cc0947-1433-422c-9a0c-fd5b03da990a.png)

일반적으로 오브젝트에 라이팅을 설정해주면

오브젝트 표면이 빛과 가까울수록 더 빛나게 되고

오브젝트의 표면 아래는 바닥에서 빛이 반사되어 표면에 닿게됩니다.

오브젝트의 반대편은 반사된 빛이 오브젝트에 닿는 부분이 적기 때문에 어둡게 보입니다.

이런 라이팅 바운싱 방법은 리얼타임 렌더링에 사용되지만 3js에서는 지원하지 않습니다.

그래서 AmbientLight를 사용해서 빛을 오브젝트 표면에 골고루 적용해주어야 합니다.

![image](https://user-images.githubusercontent.com/54713067/153727350-2060d98d-7439-4c76-bad2-436157a9d60b.png)

[3] DiractionalLight [(Docs)](https://threejs.org/docs/index.html?q=DirectionalLight#api/en/lights/DirectionalLight)

이제 오브젝트에 빛을 직접적으로 적용해줍니다.

```js
// 첫번째 파라미터에 그린 컬러의 빛을 추가해주고, 빛의 강도도 추가해줍니다.
const directionalLight = new THREE.DirectionalLight(0x00fffc, 0.3)
// 라이팅 위치를 옮길수도 있습니다.
directionalLight.position.set(1, 0.25, 0)
scene.add(directionalLight)
```

DiractionalLight는 빛을 오브젝트에 직접 적용해주지만

오브젝트에 반사되지 않아서 오브젝트 반대편은 빛이 들어오지 않습니다.

![image](https://user-images.githubusercontent.com/54713067/153727565-dc51bb65-d774-4172-a464-b00ed723e000.png)

![2022-02-13 05;41;30](https://user-images.githubusercontent.com/54713067/153727687-054ceb53-ba05-473a-a137-65ddc5bad23b.gif)

[4] hemisphereLight [(Docs)](https://threejs.org/docs/index.html#api/en/lights/HemisphereLight)

hemisphereLight는 위쪽에서 비추는 빛과 아래쪽에서 반사되는 빛의 컬러를 각각 설정해줄수 있습니다.

```js
// 이번엔 첫번째 파라미터인 위쪽에 레드컬러 빛을 추가하고, 두번째 파라미터인 아래쪽에 블루컬러를 추가하고, 빛의 강도를 적용해줍니다.
const hemisphereLight = new THREE.HemisphereLight(0xff0000, 0x0000ff, 1)
scene.add(hemisphereLight)
```

![image](https://user-images.githubusercontent.com/54713067/153728133-b4704a1d-5970-4a8e-a382-08220ed3af40.png)

![2022-02-13 05;54;43](https://user-images.githubusercontent.com/54713067/153728063-c9ba5e48-44d2-4462-83d2-45d60dcc6076.gif)

[5] PointLight [(Docs)](https://threejs.org/docs/index.html?q=hemisphereLight#api/en/lights/PointLight)

포인트 라이트는 빛을 사방으로 밝혀줍니다.

![image](https://user-images.githubusercontent.com/54713067/153728246-1283ed81-9895-46d3-a7d8-19dab1bf1402.png)


```js
// 노란색 포인트 라이트를 추가해줍니다.
// 첫번째 파라미터는 옐로우 컬러, 두번째 파라미터는 강도
// 세번째 파라미터는 빛의 거리 (기본값은 0), 네번째 파라미터는 빛의 거리에따라 빛이 어두워지는 양 입니다.
// 네번째 파라미터의 기본값은 1이지만, 사실적인 빛 감소를 위해 2로 적용해줍니다.
const pointLight = new THREE.PointLight(0xff9000, 0.5, 10, 2)
// 라이트 포지션은 기본적으로 중앙으로 되어있는데
// 포지션을 오브젝트 위쪽으로 옮겨줍니다. 
pointLight.position.set(1, - 0.5, 1)
scene.add(pointLight)

```

[6] RectAreaLight [(Docs)](https://threejs.org/docs/index.html?q=RectAreaLight#api/en/lights/RectAreaLight)

rectAreaLight는 사각형 모양으로된 라이팅입니다.

```js
// 첫번째 파라미터는 바이올렛 컬러, 두번째는 빛의 강도
// 세번째는 가로(width), 네번째는 세로(height) 입니다.
const rectAreaLight = new THREE.RectAreaLight(0x4e00ff, 2, 3, 1)
scene.add(rectAreaLight)
```

![2022-02-13 06;14;32](https://user-images.githubusercontent.com/54713067/153728644-0a3e218c-ea47-4c58-9b7f-25575e0e4d6d.gif)

```js
// 라이팅을 중앙에서 한쪽 방향을 향하도록 위치를 옮기고
rectAreaLight.position.set(- 1.5, 0, 1.5)
// lookAt을 추가해서 포지션을 적용해줍니다.
rectAreaLight.lookAt(new THREE.Vector3())
```

![image](https://user-images.githubusercontent.com/54713067/153728800-719c351d-6434-4452-93ea-84886214401c.png)


[7] SpotLight

```js
// 스폿라이트의 파라미터는
// 컬러, 강도, 빛이 사라지는 거리, 각도, 빛의 가장자리 부드러움, 빛이 끝나는 부분에 페이드 입니다.
// color, intensity, distance, angle, penumbra, decay

const spotLight = new THREE.SpotLight(0x78ff00, 0.5, 10, Math.PI * 0.1, 0.25, 1)
spotLight.position.set(0, 2, 3)
spotLight.target.position.x = - 0.75
scene.add(spotLight.target)
scene.add(spotLight)
```

- penumbra

값이 높아질수록 (0.1) 빛의 가장자리가 샤프해지고  
값이 내려갈수록 (0.25) 가장자리가 부드러워집니다.

![image](https://user-images.githubusercontent.com/54713067/153729003-7a2c9c83-a4d6-4d11-ab52-63de48e367b6.png)

- decay

기본값은 1 입니다. 빛이 끝나는 부분이 자연스럽게 페이드 됩니다.  
값이 낮아질수록 (0.1) 빛이 끝나는 부분에 페이드가 보이지 않습니다.

![image](https://user-images.githubusercontent.com/54713067/153729034-1dbc18ea-b732-4b19-8509-0046f36615aa.png)


[8] Performance

조명을 많이 사용하게되면 GPU의 연산이 많아져서 성능이 떨어지게 됩니다.  
그래서 상황에 맞게 적절한 조명을 사용하는 것이 좋습니다.

- Minimal cost : AmbientLight, HemisphereLight
- Moderate cost : DirectionalLight, PointLight
- Height cost : SpotLight, RectAreaLight

- Baking
조금 더 사실적인 그림자를 표현하고자 한다면  
조명보다는 텍스처에 미리 조명에 따라 그림자를 자연스럽게 입혀주는 방법이 있습니다.  
이것을 베이킹이라고 부릅니다.

다만, 텍스처의 그림자는 고정이라서 조명이 움직여도 그림자가 조명 방향에 따라 움직이지 않습니다.

> 아래 이미지에서 게임기 옆판의 그림자를 보시면 됩니다.

![image](https://user-images.githubusercontent.com/54713067/153729376-26761e7d-38fc-40d9-9451-86c701f657e6.png)

![image](https://user-images.githubusercontent.com/54713067/153729381-7cc7b553-0721-4a56-acf4-96d5499a61da.png)


[9] Light Helpers

위에 알아본 각 조명에 헬퍼를 추가하면  
이름별로 각각의 조명이 어떻게 생겼는지 볼 수 있습니다.

```js
// HemisphereLightHelper
const hemisphereLightHelper = new THREE.HemisphereLightHelper(hemisphereLight, 0.2)
scene.add(hemisphereLightHelper)

// DirectionalLightHelper
const directionalLightHelper = new THREE.DirectionalLightHelper(directionalLight, 0.2)
scene.add(directionalLightHelper)

// PointLightHelper
const pointLightHelper = new THREE.PointLightHelper(pointLight, 0.2)
scene.add(pointLightHelper)

// RectAreaLightHelper
import { RectAreaLightHelper } from 'three/examples/jsm/helpers/RectAreaLightHelper.js'
const rectAreaLightHelper = new RectAreaLightHelper(rectAreaLight)
scene.add(rectAreaLightHelper)

// SpotLightHelper
const spotLightHelper = new THREE.SpotLightHelper(spotLight)
scene.add(spotLightHelper)
window.requestAnimationFrame(() =>
{
    spotLightHelper.update()
})

```

![image](https://user-images.githubusercontent.com/54713067/153729499-f1ec694e-4422-4d36-98c5-2da6eefe3ccc.png)
