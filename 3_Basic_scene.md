## 03 Basic scene 기본화면

[threejs](https://threejs.org/) 사이트에 접속하여 좌측 메뉴에 download로 코드 다운받기 Code > download
([여기서](https://github.com/webgldev/threejs-journey/tree/chapter/src) `three.min.js` 을 사용할 예정)

`index.html` 에 스크립트 추가
```js
<script src="./three.min.js"></script> // 라이브러리를 먼저 불러오고
<script src="./script.js"></script>    // 그다음 내가 작성할 스크립트를 불러옴
```

`script.js`에 콘솔 찍어보고 브라우저 열어서 콘솔 나오는거 확인

`console.log(THREE); 를 찍어보면 콘솔에서 속성을 확인 할 수 있습니다.`

여기까지는 html과 javascript를 연결하는 방법 입니다.

***
***

Mesh 에는`BoxGeometry` 와 `MeshBasicMaterial`이 필요합니다. [( Docs )](https://threejs.org/docs/index.html?q=mesh#api/en/objects/Mesh)

**씬 추가**  
`const scene = new THREE.Scene()`

오브젝트인 지오메트리 **박스 추가**  
`const cubeGeometry = new THREE.BoxGeometry(1, 1, 1)`

오브젝트를 추가하고 **재질 추가**  
`const cubeMaterial = new THREE.MeshBasicMaterial({ color: '#ff0000' })`
> 컬러는 3가지로 표현 가능합니다.  
> `0xff0000` `'#ff0000'` `'red'`

오브젝트를 추가했으니 오브젝트랑 재질을 **메쉬에** 넣고  
`const cubeMesh = new THREE.Mesh(cubeGeometry, cubeMaterial)`

그리고 씬에 추가해줍니다.  
`scene.add(mesh)`

*** 

카메라는 2D와 3D 두가지가 있습니다.  
`PerspectiveCamera`  `OrthographicCamera`

이제 **카메라를 추가**하려면  
`const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height)`
> 앵글을 75도 잡고 가로와 세로를 지정하면 되는데 여기서는 sizes 변수로 따로 빼서 지정합니다.
`const sizes = { width: 800, height: 600 }`

그다음 카메라 프로퍼티를 설정해줘야합니다.  
`position` `rotation` `scale`

positon property는 object의 x, y, z 를 설정할수 있습니다.  
> x는 위쪽, y는 오른쪽, z는 내쪽  

카메라를 현재 오브젝트에서 뒤쪽으로 빼고싶으면  
`camera.positon.z = 3` 으로 설정해서 3만큼 뒤로 물러나게 합니다.  
> 똑같이 x와 y 도 원하는 위치로 설정할수 있습니다.

카메라도 씬에 추가해줍니다.  
`scene.add(camera)`

***

- 렌더링은 `WebGLRenderer` 을 사용합니다. [( Docs )](https://threejs.org/docs/index.html?q=render#api/en/renderers/WebGLRenderer) 
> `SVGRenderer` `CSS3DRenderer` `CSS2DRenderer` 는 3js와 같이 사용 못합니다.

캔버스를 선택해서 렌더링할건데  
`const renderer = new THREE.WebGLRenderer({ canvas: canvas })`

HTML에서 캔버스를 먼저 불러와야합니다.  
`index.html`에 `<canvas class="webgl"></canvas>` 추가해주고  
`const canvas = document.querySelector('canvas.webgl')` 로 불러옵니다.

렌더링 사이즈를 위에 카메라에서 설정해줬던 사이즈로 적용해줍니다.  
`renderer.setSize(sizes.width, sizes.height)`

renderer에 씬과 카메라를 렌더해줍니다.  
`renderer.render(scene, camera)`

여기까지 완성된 화면 입니다.  
![image](https://user-images.githubusercontent.com/54713067/127890661-4db530fd-73db-4091-a59a-f4c3a09fb7d0.png)


