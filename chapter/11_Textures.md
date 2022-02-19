## 11 Textures 텍스처


[1] 다양한 텍스처에 대해 알아봅시다.

이번 예제에서는 [여기](https://3d![image](https://user-images.githubusercontent.com/54713067/128763973-85626cc2-0df2-489d-98e0-6e821d2839f7.png)s.me/2019/04/16/door-wood-001/)에있는 텍스처로 연습해볼 것입니다.

아래에 텍스처 파일이 미리 준비되어 있습니다.  
> `base color (Diffuse)`, `alpha (bump)`, `ambientOcclusion (contrast)`, `height (Displacement)`, `metalness`, `normal`, `roughness`


- Alpha : 흰색만 보이는 이미지 (grayscale)

- Height : 뚜렷하게(relief) 보이게하는 회색 이미지 (grayscale)

- Normal : 디테일하게 보이도록하는 이미지

- Ambient occlusion : 흰색 바탕에 그림자를 만드는 이미지

- Metalness : 메탈을 흰색으로 지정해주는 이미지 (grayscale)

- Roughness : 거친 부분은 흰색으로, 부드러운건 검정색으로 나타내는 이미지 (grayscale)

- PBR : 텍스처는 물리기반렌더 원칙을 따릅니다. (Physically Based Rendering)

> - Adobe 에서 `PBR(Physical Based Rendering)` 에 대해 [**자세한 문서**](https://substance3d.adobe.com/tutorials/courses/the-pbr-guide-part-2)가 있으니 한번 읽어보는것이 
`metalness, roughness` 텍스처를 이해하는데 도움될것입니다.

> - PBR 물리기반 렌더링 기본이론을 보려면 [여기서](https://marmoset.co/posts/basic-theory-of-physically-based-rendering/)

> - PBR 물리기반 렌더링 적용방법을 보려면 [여기서](https://marmoset.co/posts/physically-based-rendering-and-you-can-too/)

![image](https://user-images.githubusercontent.com/54713067/128759141-4c5283e9-fec4-4ee9-a122-0c0c42ea0b5b.png)


> - `Bump Map` 이랑 `Displacement Map` 은 이런 차이가 있습니다. [여기서 보면됩니다.](https://www.rendernode.com/creating-bump-maps-in-photoshop/)
![image](https://user-images.githubusercontent.com/54713067/128757814-3ef78493-df7f-4aab-a645-50ad62e7482e.png)


> - `Nomal Map` 은 [wiki](https://ko.wikipedia.org/wiki/%EB%B2%95%EC%84%A0_%EB%A7%A4%ED%95%91) 에 간단히 설명이 나와있고, [여기서](https://www.creativebloq.com/features/model-3d-textures-quickly-with-normal-maps) 읽어봐도 도움됩니다.

![image](https://user-images.githubusercontent.com/54713067/128761441-8cda215a-6315-41d8-a234-f83e5b75860a.png)

<hr/>

몇가지 텍스처를 알아봤으니 이제 적용해봅시다!

3js가 제공하는 [( Docs )](https://threejs.org/docs/index.html?q=tex#api/en/constants/Textures) 에  
Mapping Modes, Wrapping Modes, Magnification Filters, Types, Formats 등등   여러 텍스처가 많이 있으니 나중에 필요한 텍스처를 적용하면 됩니다.

이미지 소스를 임포트해줍니다.  
`import imageSource from './image.png'`

- webpack으로 경로를 미리 지정해준 경우, static/ 하위 경로에 이미지 추가해줍니다.

웹팩에 이미지 Path를 미리 설정해두었기 때문에 src에서 이미지 경로 지정해서 사용해도 됩니다.  
`webpack.common.js`
```js
// Images
{
  test: /\.(jpg|png|gif|svg)$/,
  use:
  [
      {
          loader: 'file-loader',
          options:
          {
              outputPath: 'assets/images/'
          }
      }
  ]
},
```

- JS로 만들면 인스턴스에 패스를 직접 설정해줍니다.

`script.js`
```js
// Textures
const image = new Image()  // 이미지 하나 만들고
const texture = new THREE.Texture(image) // 이미지를 텍스처에 넣어줍니다.

image.onload = () => {  // 이미지 불러옴
  texture.needsUpdate = true // 이미지 변경되면 콜백으로 텍스처를 업데이트 해줍니다.
  console.log('image loaded')
}
image.src = '/textures/door/color.jpg' // 이미지 경로도 적어줍니다.
```

이미지 불러왔으니 그다음 material에 texture를 적용해줍니다.
```js
const material = new THREE.MeshBasicMaterial({
  map: texture,
  // color: parameters.color,
  // wireframe: true
})
```

이제 기본 텍스처가 적용되었습니다.

![image](https://user-images.githubusercontent.com/54713067/128763981-f65918c5-3aca-4a66-89c8-6c8063cde3eb.png)

근데 위에꺼 다 지우고 3js가 제공해주는 Loaders를 사용해도됩니다. [( Docs )](https://threejs.org/docs/index.html?q=tex#api/en/loaders/TextureLoader)

```js
const textureLoader = new THREE.TextureLoader()  // 텍스처 로더로 불러오고
const texture = textureLoader.load('/textures/door/color.jpg') // 이미지 경로 지정하면 됩니다.
```

콜백으로 이미지가 잘 불러와지는지 `load, progress, error` 로 알아볼 수도 있습니다.
```js
const texture = textureLoader.load(
  '/textures/door/color.jpg',
  () => {console.log('load')},
  () => {console.log('progress')},
  () => {console.log('error')},
)
```

- 이벤트를 추가하려면 LoadingManager에 인스턴스를 만들고 TextureLoader에 전달해줍니다.

```js
const loadingManager = new THREE.LoadingManager()
loadingManager.onStart  = () => {console.log('onStart')}
loadingManager.onLoaded = () => {console.log('onLoaded')}
loadingManager.onError  = () => {console.log('onError')}
const textureLoader = new THREE.TextureLoader(loadingManager)
```

- 모든 텍스처 종류 불러오기

```js
const colorTexture = textureLoader.load('/textures/door/color.jpg')
const alphaTexture = textureLoader.load('/textures/door/alpha.jpg')
const heightTexture = textureLoader.load('/textures/door/height.jpg')
const normalTexture = textureLoader.load('/textures/door/normal.jpg')
const ambientOcclusionTexture = textureLoader.load('/textures/door/ambientOcclusion.jpg')
const metalnessTexture = textureLoader.load('/textures/door/metalness.jpg')
const roughnessTexture = textureLoader.load('/textures/door/roughness.jpg')
```

[3] UV unwrapping

UV 2D 좌표를 보려면 console.log(geometry.attributes.uv)

geometry 밑에서 `console.log(geometry.attributes)` 콘솔을 찍어보면 브라우저에 uv가 나오는걸 볼수있습니다.

![image](https://user-images.githubusercontent.com/54713067/128813617-f25bbc84-f535-4fa9-b823-fba78b2ebf69.png)


<hr/>


[4] Transforming the texture

- 반복 : .repeat.x , .repeat.y , THREE.RepeatWrapping

- 방향 : THREE.MirroredRepeatWrapping

- 옵셋 : .offset.x , .offest.y 속성으로 UV 좌표 옵셋

- 회전 : .rotation (Math.PI * 0.1 숫자로 회전), 회전 기준 좌표는 0, 0

- 피봇 : pivot을 중심으로 변경하려면 .center.x , .center.y



- 이미지를 `repeat` `wrapS` `wrapT` `offset` `rotation` `center` 프로퍼티로 적용하는 방법

> - `repeat`  
> 프로퍼티를 적용하면, 이미지가 끝나는 지점에 있는 컬러가 오브젝트 끝까지 계속 반복됩니다.
> `colorTexture.repeat.x` x축으로 계속 반복됩니다.
> 
> - `wrapS`  
프로퍼티를 적용하면, 수평으로 래핑됩니다. (UV에서 `U`에 해당함) [( Docs )](https://threejs.org/docs/#api/en/textures/Texture.wrapS)
> - `wrapT`  
프로퍼티를 적용하면, 수평으로 래핑됩니다. (UV에서 `V`에 해당함) [( Docs )](https://threejs.org/docs/#api/en/textures/Texture.wrapT)
> (근데 S랑 T가 무슨 약자인지는 모르겠음)
> 
> - `THREE.RepeatWrapping` 프로퍼티는  
이미지가 끝나는 지점에서 동일한 이미지가 끝까지 계속 반복됩니다.

> - `THREE.MirroredRepeatWrapping`프로퍼티를  
이미지가 끝나는 지점에서 동일한 이미지가 반대로 미러링된채로 끝까지 계속 반복됩니다.
> 
> - `offset.x = 0.5` `offset.y = 0.5` x축과 y 축으로 옵셋이고
> 
> - `rotation` 은 radian 각도에 따라 `Math.PI / 4`하고 `Math.PI * 25` 같은 숫자를 사용해서 텍스처를 회전 할 수 있습니다.
![image](https://user-images.githubusercontent.com/54713067/128970018-c1d07ab0-4799-4332-a7ca-e8736e332555.png)

> - `center` 는 pivot point를 중앙으로 옮겨서 다른 프로퍼티를 적용할 수 있습니다.
![image](https://user-images.githubusercontent.com/54713067/128971347-3e401c12-aaf7-47ea-9b7e-18159cc20d39.png)

> [threejs fundamentals 한글자료](https://threejsfundamentals.org/threejs/lessons/kr/threejs-textures.html)에서 텍스처도 한번 읽어보는 것도 도움됩니다.  
> ( `repeat` `offset` `rotation` Controls box가 있어서 이미지가 각각 어떻게 나오는지 직접 확인할 수 있습니다.)



[5] filtering, mipmapping

- .minFilter 의 여섯가지 속성으로 텍스처의 필터를 변경할수 있습니다.

THREE.LinearMipmapLinearFilter // 기본값

THREE.NearestFilter // 오브젝트가 축소될수록 이미지가 샤프해짐
THREE.LinearFilter, THREE.NearestMipmapNearestFilter
THREE.NearestMipmapLinearFilter, THREE.LinearMipmapNearestFilter
나머지 텍스처는 독스에 설명이 있습니다.

https://threejs.org/docs/#api/en/constants/Textures

- Moiré pattern (https://en.wikipedia.org/wiki/Moir%C3%A9_pattern)

- 작은 텍스처를 오브젝트에 적용하면 확대되서 흐릿해지는데

.magFilter에 THREE.NearestFilter 를 적용하면 작은 이미지는 확대되지않고 원본이 유지됩니다.

- .minFilter에 THREE.NearestFilter를 적용하면 mipmap이 필요하지 않아서 

colorTexture.generateMipmaps = false 로 mipmap을 비활성해서 GPU의 부하를 덜어줍니다.



***

`colorTexture.minFilter = THREE.NearestFilter` 이미지가 픽셀단위로 적용되서  
이미지 사이즈가 작고 오브젝트가 크면 이미지가 확대되면서 뭉개지는데 [( Docs )](https://threejs.org/docs/#api/en/textures/Texture.minFilter)

`colorTexture.magFilter = THREE.NearestFilter` 를 적용하면  
이미지가 벡터처럼 선명하게 보입니다. (기본값은 THREE.LinearFilter 입니다.) [( Docs )](https://threejs.org/docs/#api/en/textures/Texture.magFilter)

[이미지 출처엔 한글로 설명이 적혀있습니다.](https://threejsfundamentals.org/threejs/lessons/kr/threejs-textures.html)

![image](https://user-images.githubusercontent.com/54713067/129071283-ba401cba-30bd-4ba6-8538-3fa1afba7c7d.png)

- minFilter 프로퍼티를 사용해서 텍스처를 축소하거나 필터를 변경할수 있습니다.
```js
THREE.NearestFilter
THREE.LinearFilter
THREE.NearestMipmapNearestFilter
THREE.NearestMipmapLinearFilter
THREE.LinearMipmapNearestFilter
THREE.LinearMipmapLinearFilter (default)
```

`minFilter`에 `THREE.NearestFilter` 를 사용하면 mpmaps가 필요없어서  
`colorTexture.generateMipmaps = false` 를 적용해줍니다.

```js
colorTexture.generateMipmaps = false
colorTexture.minFilter = THREE.NearestFilter
```


***


[6] optimisation

- weight, size(resolution), data 세가지를 고려해서 텍스처 준비

- weight : jpg는 png보다 가벼움, png는 TinyPNG 에서 압축해서 사용

- size : 이미지 크기는 줄이는것을 추천, width와 height는 2픽셀에서 거듭제곱으로 지정

- data : jpg 파일은 alpha map을 사용하지 못해서 png를 사용

***


- 텍스처 최적화 (Texture format and optimisation)  
텍스처를 준비할때는  
1. `The weight`  
2. `The size(or the resolution)`  
3. `The data` 가 중요합니다.

- 1. `The weight`
> - `jpg` lossy compression but usually lighter
> - `png` lossless compression but usually heavier
> [TinyPNG](https://tinypng.com/) 여기에서 png 사이즈를 압축하면 사이즈를 더 줄일수 있음

-  2. `The size(or the resolution)`

> 사용하는 텍스처의 각 픽셀은 이미지의 weight에 관계없이 GPU에 저장되어야 합니다.  
GPU에는 스토리지 제한이 있으니 가능한 한 이미지의 크기를 줄여야 합니다.

> Three.js는 1x1 텍스처를 얻을 때까지 반복적으로 절반의 작은 버전의 텍스처를 생성할 것입니다.  
Three.js가 텍스처 크기를 2로 나눌 수 있도록 하기 위해 텍스처 너비와 높이는 2의 거듭제곱이어야 합니다.

```
512/2 = 256
256/2 = 128
128/2 = 64
64/2 = 32
32/2 = 16
16/2 = 8
8/2 = 4
4/2 = 2
2/1 = 1

512x512
1024x1024
512x2048
```

3. `The data`

jpg에는 알파채널이 없어서 png를 사용하는 것을 추천합니다.

- 텍스처 참고 사이트들 
> [poliigon.com](poliigon.com)  
[3dtextures.me](3dtextures.me)  
[arroway-textures.ch](arroway-textures.ch)  

