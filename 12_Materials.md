## 12 Materials


픽셀의 색상은 shader로 작성합니다. 3js는 shader가 포함된 빌트인 머테리얼이 있습니다.



[1] Prepare our scene

// MeshBasicMaterial 기본 메쉬 추가하고 3개 오브젝트에 같은 머테리얼 적용
const material = new THREE.MeshBasicMaterial()
// 1번 공
const sphere = new THREE.Mesh(
    new THREE.SphereGeometry(0.5, 16, 16),
    material
)
sphere.position.x = - 1.5
// 2번 플랜
const plane = new THREE.Mesh(
    new THREE.PlaneGeometry(1, 1),
    material
)
// 3번 도넛
const torus = new THREE.Mesh(
    new THREE.TorusGeometry(0.3, 0.2, 16, 32),
    material
)
torus.position.x = 1.5

// .add() 메서드에 멀티플 오브젝트 추가
scene.add(sphere, plane, torus)


// 앞장에서 배웠던대로 오브젝트 회전시켜보기
const clock = new THREE.Clock()
const tick = () => {
    const elapsedTime = clock.getElapsedTime()
    // Update objects
    sphere.rotation.y = 0.1 * elapsedTime
    plane.rotation.y = 0.1 * elapsedTime
    torus.rotation.y = 0.1 * elapsedTime
    sphere.rotation.x = 0.15 * elapsedTime
    plane.rotation.x = 0.15 * elapsedTime
    torus.rotation.x = 0.15 * elapsedTime
}
tick()

// 텍스처 폴더에 matcaps 와 gradients 폴더를 추가하고 텍스처 로드
const matcapTexture = textureLoader.load('/textures/matcaps/1.png')
const gradientTexture = textureLoader.load('/textures/gradients/3.jpg')



// 기타 더 많은 머테리얼을 보려면 독스에서 직접 확인

https://threejs.org/docs/?q=mate#api/en/constants/Materials



[2] MeshBasicMaterial (기본)


// 머테리얼을 파라미터로 받아서 프로퍼티를 설정할수도 있지만
const material = new THREE.MeshBasicMaterial({
    map: doorColorTexture
})

// 인스턴스에서 해당 프로퍼티를 직접 변경할수도 있습니다.
// 프로퍼티는 .map으로 텍스처를 적용합니다.
const material = new THREE.MeshBasicMaterial()
material.map = doorColorTexture

// .color의 프로퍼티를 직접 변경하려면 new THREE.Color('#f00') 로 인스턴스화 해야합니다.
// 형식은 #ff0000, #f00, red, rgb(255, 0, 0), 0xff0000 모두 사용 가능합니다.
material.color = new THREE.Color('#ff0000')

// 와이프레임을 보려면
material.wireframe = true

// 투명값을 적용하려면
material.transparent = true
material.opacity = 0.5

// alphaMap으로 투명값을 조절하려면
material.transparent = true
material.alphaMap = doorAlphaTexture

// .side에 보고싶은 면을 표시
// 앞면 THREE.FrontSide , 뒷면 THREE.BackSide , 앞뒤 둘다 표시 THREE.DoubleSide
material.side = THREE.DoubleSide
// DoubleSide의 경우는 렌더링할 면이 두배로 많아지기 때문에 주의



[3] MeshNormalMaterial

// 노말은 각 버텍스가 면에서 밖을 향하는 방향이 encoded 된 정보입니다.
// 지오메트리에 environment reflect 와 refract (반사와 굴절) 를 표현합니다
const material = new THREE.MeshNormalMaterial()

// 사용 가능한 속성 : .wireframe, .transparent, .opacity, .side, .flatShading
.flatShading은 각진 면을 보여줍니다.



[4] MeshMatcapMaterial


// MeshMatcapMaterial은 성능이 좋습니다.
// .matcap 에 matcapTexture 텍스처를 적용해서 사용


// 텍스처를 2~8개를 로드해서 matcaps 를 사용해봅시다.
const matcapTexture = textureLoader.load('/textures/matcaps/2.png')


// 포토샵에서도 matcaps를 만들수 있습니다.

// 다양한 목록을 더 보려면 https://github.com/nidorx/matcaps



[5] MeshDepthMaterial



// 카메라에서 멀리 떨어져있는 원근감을 갖기위해 첫번째로 MeshDepthMaterial를 적용

// 오브젝트가 카메라에 가까우면 지오메트리를 흰색으로, 멀면 블랙으로 표시

const material = new THREE.MeshDepthMaterial()



[6] Adding a few lights

// 라이팅 두개 추가
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
scene.add(ambientLight)

const pointLight = new THREE.PointLight(0xffffff, 0.5)
pointLight.position.x = 2
pointLight.position.y = 3
pointLight.position.z = 4
scene.add(pointLight)



[7] MeshLambertMaterial

// 빛에 반사되는 휘도를 보려면
// MeshBasicMaterial과 같은 속성을 지원합니다.
const material = new THREE.MeshLambertMaterial()



[8] MeshPhongMaterial

// MeshLambertMaterial보다는 성능이 떨어지지만 빛 반사를 제어할수 있습니다.

// .shininess 는 높을수록 반짝이고 .specular 는 반사 컬러를 지정할 수 있습니다.

material.shininess = 100
material.specular = new THREE.Color(0x1188ff)


[9] MeshToonMaterial

// 만화 스타일 적용하려면

const material = new THREE.MeshToonMaterial()

// 그림자, 조명 두가지 컬러만 적용됩니다.

// 컬러를 더 추가하려면 .gradientMap에 gradientTexture 를 사용하면 되지만 그럼 만화효과가 줄어듭니다.

material.gradientMap = gradientTexture

// 기본적으로 mipmap을 사용하고있기 때문에 mipmaps를 false로 설정하면 다시 만화효과르 적용할수 있습니다.

gradientTexture.minFilter = THREE.NearestFilter
gradientTexture.magFilter = THREE.NearestFilter
gradientTexture.generateMipmaps = false



[10] MeshStandardMaterial

// MeshLambertMaterial 및 MeshPhongMaterial 보다 matalness와 roughness 처럼 현실적인 알고리즘 파라미터를 적용합니다.

const material = new THREE.MeshStandardMaterial()

material.metalness = 0.45
material.roughness = 0.65



// 디버그 UI를 추가해서 컨트롤로 직접 조정할수도 있습니다.
const gui = new dat.GUI()
gui.add(material, 'metalness').min(0).max(1).step(0.0001)
gui.add(material, 'roughness').min(0).max(1).step(0.0001)



// 어두운 부분에 그림자를 추가하는 aoMap(ambient occlusion map)은 uv 속성을 추가해서 사용하면 됩니다.

sphere.geometry.setAttribute('uv2', new THREE.BufferAttribute(sphere.geometry.attributes.uv.array, 2))
plane.geometry.setAttribute('uv2', new THREE.BufferAttribute(plane.geometry.attributes.uv.array, 2))
torus.geometry.setAttribute('uv2', new THREE.BufferAttribute(torus.geometry.attributes.uv.array, 2))



// 강도를 조절하려면

material.aoMap = doorAmbientOcclusionTexture
material.aoMapIntensity = 1



// 갈라진 틈새를 더 어둡게 만들려면

material.displacementMap = doorHeightTexture


// normalScale 속성으로 강도를 조절할수 있습니다.
material.normalScale.set(0.5, 0.5)

// 투명값 적용해서 알파맵 사용
material.transparent = true
material.alphaMap = doorAlphaTexture



[11] Environment map

Environment map으로 오브젝트에 반사와 굴절을 추가할수 있습니다.

// 디버그 UI에 MeshStandardMaterial 을 설정해서 사용

const material = new THREE.MeshStandardMaterial()
material.metalness = 0.7
material.roughness = 0.2
gui.add(material, 'metalness').min(0).max(1).step(0.0001)
gui.add(material, 'roughness').min(0).max(1).step(0.0001)



// .envMap으로 환경맵 추가

// 3js는 큐브 환경 맵만 지원합니다. 큐브 텍스처는 CubeTextureLoader를 사용합니다.

// metalness와 roughness 조절로 환경맵이 반영되었는지 체크하면 됩니다. 

const cubeTextureLoader = new THREE.CubeTextureLoader()
const environmentMapTexture = cubeTextureLoader.load([
    '/textures/environmentMaps/0/px.jpg',
    '/textures/environmentMaps/0/nx.jpg',
    '/textures/environmentMaps/0/py.jpg',
    '/textures/environmentMaps/0/ny.jpg',
    '/textures/environmentMaps/0/pz.jpg',
    '/textures/environmentMaps/0/nz.jpg'
])

material.envMap = environmentMapTexture



