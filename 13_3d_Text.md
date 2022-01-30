## 13 3D Text

3js를 배우면 ilithya의 [포트폴리오](https://www.ilithya.rocks/ )처럼 만들 수 있습니다.

3js는 [TextGeometry](https://threejs.org/docs/?q=textge#examples/en/geometries/TextGeometry) 클래스로 3d텍스트를 만들 수 있습니다.  
텍스트에 적용되는 폰트는 json 형식이어야 적용됩니다.

폰트는 [여기에서](http://gero3.github.io/facetype.js/) 변환할 수 있습니다.

`/node_modules/three/examples/fonts/` 에 있는 폰트를 임포트해서 사용해도 됩니다.

파일 형식은 json과 js가 있습니다.

`import typefaceFont from 'three/examples/fonts/helvetiker_regular.typeface.json'`

우리는 `/fonts/helvetiker_regular.typeface.json` 를 적용할 것입니다.

[1] Load the font

폰트는 FontLoader 로 임포트해서 사용합니다.  
```
import { FontLoader } from 'three/examples/jsm/loaders/FontLoader.js'

const fontLoader = new FontLoader()

fontLoader.load(
    '/fonts/helvetiker_regular.typeface.json',
    (font) =>
    {
        console.log('loaded')
    }
)
```


[2] Create the geometry

지오메트리는 TextGeometry 를 사용합니다.

```
import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry.js'

fontLoader.load(
  '/fonts/helvetiker_regular.typeface.json', // 폰트를 불러오고
  (font) =>
  {
    const textGeometry = new TextGeometry( // 지오메트리 추가해줍니다.
      'Hello Three.js',
      {
        font: font,
        size: 0.5,
        height: 0.2,
        curveSegments: 12,
        bevelEnabled: true,
        bevelThickness: 0.03,
        bevelSize: 0.02,
        bevelOffset: 0,
        bevelSegments: 5
      }
    )
    const textMaterial = new THREE.MeshBasicMaterial()
    const text = new THREE.Mesh(textGeometry, textMaterial)
    scene.add(text)
  }
)
```

와이어프레임으로 보면 수많은 삼각형이 보이는데,  

`const textMaterial = new THREE.MeshBasicMaterial({ wireframe: true })`

낮은 폴리로 유지하려면 bevelSegments와 curveSegments 속성을 줄여주면 됩니다.




[3] Center the text

텍스트를 중앙으로 맞추려면 바운딩을 사용하는 방법이 있습니다.  
바운딩 경계는 실제로 볼수는 없지만 box가 되거나 sphere 가 될수도 있습니다.

![image](https://user-images.githubusercontent.com/54713067/151691696-a4015198-bc3d-4082-ab11-65ec4ab5b578.png)

이 경계를 알게되면 지오메트리의 크기를 알수있고, 중앙으로 정렬할 수 있습니다.  
3js는 기본적으로  원형 경계를 사용합니다. 그치만 경계가 박스처럼 생겨야 더 정확하게 정렬이 가능해서  
computeBoundingBox() 을 호출해서 박스의 경계를 계산하려합니다.

`textGeometry.computeBoundingBox()`  
`console.log(textGeometry.boundingBox)`

bevel은 모서리의 경계를 등분하여 나누는 것입니다.  
이것의 사이즈를 0.02를 빼면 텍스트를 좀더 중앙에 배치시킬 수 있습니다.

```
textGeometry.translate(
    - (textGeometry.boundingBox.max.x - 0.02) * 0.5, // Subtract bevel size
    - (textGeometry.boundingBox.max.y - 0.02) * 0.5, // Subtract bevel size
    - (textGeometry.boundingBox.max.z - 0.03) * 0.5  // Subtract bevel thickness
)
```

위에 각 위치를 잡아주는것보다 `textGeometry.center()` center 메서드를 직접 호출하는것이 정렬이 더 빠릅니다.


[4] Add a matcap material

텍스트에 MeshMatcapMaterial 재질을 입혀봅시다.

텍스처는 [여기](https://github.com/nidorx/matcaps)에서 다운로드할 수 있습니다.

텍스처 사이즈는 256x256이면 충분합니다.

```
const matcapTexture = textureLoader.load('/textures/matcaps/1.png') // 텍스처를 불러와서

const textMaterial = new THREE.MeshMatcapMaterial({ matcap: matcapTexture }) // 직접 적용해줍니다.
```


[5] Add objects

텍스트 주변에 도넛을 추가해봅시다.

```
for(let i = 0; i < 100; i++)
{
    const donutGeometry = new THREE.TorusGeometry(0.3, 0.2, 20, 45)
    const donutMaterial = new THREE.MeshMatcapMaterial({ matcap: matcapTexture })
    const donut = new THREE.Mesh(donutGeometry, donutMaterial)
    scene.add(donut)
}
```

도넛 백개를 랜덤으로 화면에 뿌려줍니다.

```
donut.position.x = (Math.random() - 0.5) * 10
donut.position.y = (Math.random() - 0.5) * 10
donut.position.z = (Math.random() - 0.5) * 10
```

도넛의 방향을 조금 회전시킵니다.
```
donut.rotation.x = Math.random() * Math.PI
donut.rotation.y = Math.random() * Math.PI
```

도넛의 스케일도 랜덤으로 조절해봅시다.
```
const scale = Math.random()
donut.scale.set(scale, scale, scale)
```

최종화면 입니다.

![image](https://user-images.githubusercontent.com/54713067/151692832-80db19ce-429c-4a3b-87dd-5b36868099e1.png)


[6] Optimize

위에 코드는 최적화되어있지 않아서 코드를 최적화시켜줍니다.

donutGeometry와 donutMaterial을 for문 밖으로 꺼내줍니다.

```
const donutGeometry = new THREE.TorusGeometry(0.3, 0.2, 20, 45)
const donutMaterial = new THREE.MeshMatcapMaterial({ matcap: matcapTexture })

for(let i = 0; i < 100; i++)
{
    const donut = new THREE.Mesh(donutGeometry, donutMaterial)
    scene.add(donut)
}
```

[7] Go further

여기에 더 나아가서 더 많은 모양을 추가하거나 애니메이션을 적용할수도 있습니다.

