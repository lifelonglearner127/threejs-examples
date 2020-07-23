<template>
  <div id="container"></div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data: function() {
    return {
      renderer: null,
      scene: null,
      camera: null
    }
  },
  mounted () {
    let container = document.getElementById( 'container' );

    this.camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 1000)
    this.camera.position.set(1, 2, -3)
    this.camera.lookAt(0, 1, 0)

    this.scene = new THREE.Scene()
    this.scene.background = new THREE.Color(0xa0a0a0)
    this.scene.fog = new THREE.Fog(0xa0a0a0, 10, 50)

    let hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444)
    hemiLight.position.set(0, 20, 0)
    this.scene.add(hemiLight)

    let dirLight = new THREE.DirectionalLight(0xffffff)
    dirLight.position.set(-3, 10, -10)
    dirLight.castShadow = true
    dirLight.shadow.camera.top = 2
    dirLight.shadow.camera.bottom = - 2
    dirLight.shadow.camera.left = - 2
    dirLight.shadow.camera.right = 2
    dirLight.shadow.camera.near = 0.1
    dirLight.shadow.camera.far = 40
    this.scene.add(dirLight)

    var mesh = new THREE.Mesh(
      new THREE.PlaneBufferGeometry( 100, 100 ),
      new THREE.MeshPhongMaterial( { color: 0x999999, depthWrite: false } )
    )
    mesh.rotation.x = - Math.PI / 2
    mesh.receiveShadow = true
    this.scene.add( mesh )

    let loader = new GLTFLoader()
    let vm = this
		loader.load('Soldier.glb', function (gltf) {
      let model = gltf.scene
			vm.scene.add(model)
      model.traverse( function ( object ) {
        if ( object.isMesh ) {
          object.castShadow = true
        }
      })
      vm.animate()
    })

    this.renderer = new THREE.WebGLRenderer( { antialias: true } )
    this.renderer.setPixelRatio( window.devicePixelRatio )
    this.renderer.setSize( window.innerWidth, window.innerHeight )
    this.renderer.outputEncoding = THREE.sRGBEncoding
    this.renderer.shadowMap.enabled = true
    container.appendChild( this.renderer.domElement )
  },
  methods: {
    animate() {
      requestAnimationFrame(this.animate)
      this.renderer.render( this.scene, this.camera )
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
