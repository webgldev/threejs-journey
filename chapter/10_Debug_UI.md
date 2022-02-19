## 10 Debug UI  UI 디버깅



[1] 디버그 할수있는 라이브러리들

dat.GUI, control-panel, ControlKit, Uil, Tweakpane, Guify, Oui

컨트롤 박스를 통해 카메라, 컬러, 쉐도우, 속도 등등 여러가지를 디버깅할 수 있습니다.

[2] 이 강의에서는 dat.GUI를 사용합니다.

영상에서는 컨트롤박스 중에 dat.gui 라이브러리를 사용할 예정입니다. (3js에서 많이 사용되는 GUI입니다.)
> [( Github )](https://github.com/dataarts/dat.gui), [( NPM )](https://www.npmjs.com/package/dat.gui)

***

### 영상에서는 dat.gui를 설명했지만.

*dat.gui는 오래동안 업데이트되지 않아서 취약성 경고가 뜨므로*
*lil-gui로 대체할것이라고 합니다.*

1. 설치 - npm i --save lil-gui

2. 추가 - import * as dat from 'lil-gui'

3. 작성 - const gui = new dat.GUI()

4. 요소 - Range, Color, Text, Checkbox, Select, Button, Folder

***

영상을 기준으로 정리하려합니다.

`npm install --save dat.gui` 설치하고  
`import * as dat from 'dat.gui'` 임포트해줍니다.

OrbitControls

`const gui = new dat.GUI()` 을 추가해서 화면에 패널을 만들어봅니다.
> 컨트롤이 생겼는데 옵션은 없는상태임  
옵션에 `Range` `Color` `Text` `Checkbox` `Select` `Button` `Folder` 이런것들을 추가할 수 있습니다.

그다음 오브젝트 밑에 gui 파라미터를 추가해줍니다.  
`gui.add(mesh.position, 'y', -3, 3, 0.01)`  
`-3`은 `Minimum` 이고 `3`은 `Maximum`이고 `0.01`은 `Step` 입니다.

똑같이 x, z 도 추가해줍니다.  
`gui.add(mesh.position, 'x', -3, 3, 0.01)`  
`gui.add(mesh.position, 'z', -3, 3, 0.01)` 

이렇게 바꿔도 동일합니다.  
`gui.add(mesh.position, 'y').min(-3).max(3).step(0.01)`

잘보이게 라인을 정리해주면
```js
gui
  .add(mesh.position, 'y')
  .min(-3)
  .max(3)
  .step(0.01)
  .name('elevation') // 이름도 적용할 수 있습니다.

// 오브젝트를 보여주거나 보여주지 않는 체크박스도 추가할 수 있습니다.
gui
  .add(mesh, 'visible')

// 와이어프레임 체크박스도 추가할 수 있습니다.
gui
  .add(material, 'wireframe')
```

[3] 컬러를 추가해봅시다.


먼저 컬러 변수를 만들어주고
`const parameters = { color: 0xff0000 } // 헥사값 입력 가능`

인스턴스를 추가해줍니다.
`gui.addColor(parameters, 'color')`

기본 컬러 외에 컬러를 사용하려면  
onChange로 색상 선택기를 추가해서 조절하면 됩니다.

```js
gui
  // 컬러 파라미터 추가하고
  .addColor(parameters, 'color')
  // 컬러 바꾸면 컬러가 변경되도록 onChange도 추가해줍니다.
  .onChange(() => { material.color.set(parameters.color) })
  // 머테리얼 컬러도 같이 변경되도록 수정해줍니다.
  const material = new THREE.MeshBasicMaterial({ color: parameters.color })
```


[4] 움직이는 기능을 추가하려면

변수에 spin 기능을 추가해주고
```js
const parameters = {

  color: 0xff0000,

  spin: () => { gsap.to(mesh.rotation, { duration: 1, y: mesh.rotation.y + Math.PI * 2 }) }

}
```

gui로 인스턴스화하여 객체로 만들어줍니다.  
`gui.add(parameters, 'spin')`

```js
const parameters = {
  color: '#ff0000',
  spin: () => {
    // gsap.to(mesh.rotation, { duration: 1, y: 10 })
    gsap.to(mesh.rotation, { duration: 1, y: mesh.rotation.y + 10 }) // 지속시간 1 만큼 mesh 오브젝트를 y 방향으로 10번 회전시킵니다.
    console.log('spin')
  }
}

gui
  .add(parameters, 'spin') // spin 버튼을 클릭하면 동작합니다.
```


[5] 기타 팁들

- 단축키 H 를 누르면 패널을 숨길 수 있습니다.

> `const gui = new dat.GUI({ closed: true, width: 400 })` GUI에 closed 를 true로 설정하면 열려있던 옵션이 닫혀있게 됩니다. (GUI 가로 사이즈도 지정해줄 수 있습니다.)

- gui.hide()를 호출하면 패널을 처음부터 안보이게 할 수 있습니다.

> `gui.hide()` 를 작성하면 GUI가 안보이게되는데 키보드 `H` 를 누르면 다시 보이게 됩니다.

- 패널을 처음부터 닫으려면 const gui = new dat.GUI({ closed: true }) 로 적용

- 패널의 기본 width를 설정하려면 const gui = new dat.GUI({ width: 400 }) 적용

- Dat.GUI에 대해 자세한 문서는 https://github.com/dataarts/dat.gui

***

위에있는것들 모두 종합하면 이렇게 컨트롤러가 만들어집니다.
(GUI 순서는 인터프리터 순서대로 정렬되는듯 합니다.)

![image](https://user-images.githubusercontent.com/54713067/128742828-7c5845c3-d14b-4474-8f8e-4232dd69cc25.png)

- bruno_simon 이 만든 컨트롤러는 [이렇게](https://bruno-simon.com/#debug) 생겼습니다.

- 기본적인 것들 외에 [**여기 MD에**](github.com/dataarts/dat.gui/blob/HEAD/API.md) 적용가능한 다양한 GUI가 있습니다.

- 여기있는 [예제](https://jsfiddle.net/ikatyang/182ztwao/)도 괜찮아서 참고하면서 만들면됩니다.
> 예제 URL이 사라질까봐 코드를 미리 작성해두려 합니다.

`index.html`  
`<script src="https://cdnjs.cloudflare.com/ajax/libs/dat-gui/0.5/dat.gui.min.js"></script>`

`style.css`  
`.folder .dg ul {margin-top: 0 !important;}`

`script.js`
```js
var gui = new dat.GUI({ load: getPresetJSON(), preset: 'Preset1' });

var object1 = {
	type1_boolean: false,
  type2_string: 'string',
  type3_number: 0,
  type4_function: function () {
  	alert('This is a function.');
  }
};

var object2 = {
	string1: 'string1',
  string2: 'string2'
};

var object3 = {
	color0: "#ffae23", // CSS string
  color1: [ 0, 128, 255 ], // RGB array
  color2: [ 0, 128, 255, 0.3 ], // RGB with alpha
  color3: { h: 350, s: 0.9, v: 0.3 } // Hue, saturation, value
};
// dat.GUI will modify colors in the format defined by their initial value.

// saveValues: gui.remember must be executed before gui.add
gui.remember(object1);
gui.remember(object2);

// setController: boolean, string, number, function
gui.add(object1, 'type1_boolean');
gui.add(object1, 'type2_string');

var folder1 = gui.addFolder('FolderNameA');
folder1.add(object1, 'type3_number', -5, 5);
folder1.add(object1, 'type4_function');

// collapse folder1
folder1.close();

var folder2 = gui.addFolder('FolderNameB');
folder2.add(object2, 'string1', {
	'key1': 'string_1',
  'key2': 'string_2',
  'key3': 'string_3'
});
folder2.add(object2, 'string2', [
	'string_1', 'string_2', 'string_3'
]);

var folder3 = gui.addFolder('FolderNameC');
folder3.addColor(object3, 'color0');
folder3.addColor(object3, 'color1');
folder3.addColor(object3, 'color2');
folder3.addColor(object3, 'color3');

// expand folder3
folder3.open();

// presetJSON: created from pressing the gear.
function getPresetJSON() {
	return {
    "preset": "Default",
    "closed": false,
    "remembered": {
      "Default": {
        "0": {
          "type1_boolean": false,
          "type2_string": "string",
          "type3_number": 0
        },
        "1": {
          "string1": "string1",
          "string2": "string2"
        }
      },
      "Preset1": {
        "0": {
          "type1_boolean": true,
          "type2_string": "string123",
          "type3_number": -2.2938689217758985
        },
        "1": {
          "string1": "string_2",
          "string2": "string_3"
        }
      }
    },
    "folders": {
      "FolderNameA": {
        "preset": "Default",
        "closed": false,
        "folders": {}
      },
      "FolderNameB": {
        "preset": "Default",
        "closed": false,
        "folders": {}
      },
      "FolderNameC": {
        "preset": "Default",
        "closed": false,
        "folders": {}
      }
    }
  };
}
```





