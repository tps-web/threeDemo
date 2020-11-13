<template>
  <div id="content">
     <div class="audioBtn">
       <img src="../assets/playAudio.png" alt="" height="20" width="20" v-show="audioBoool"/>
       <img src="../assets/closeAudio.png" alt="" height="20" width="20" v-show="!audioBoool"/>
     </div>
  </div>
</template>

<script>
  import * as Three from 'three';
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"

  export default {
  name: 'App',
  data () {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      audioBoool: true,  //是否播放音乐
    }
  },
    methods:{
      init: function () {
        var width = window.innerWidth;
        var height = window.innerHeight;

        this.scene = new Three.Scene()

        this.camera = new Three.PerspectiveCamera(60, width / height, 1, 1000)
        this.camera.zoom = 1;
        this.camera.updateProjectionMatrix();
        this.camera.position.set(-0.87, 0.03, 0.4);
        this.camera.lookAt(this.scene.position);
        
        // let geometry = new Three.BoxGeometry(0.2, 0.2, 0.2)
        var geometry = new Three.SphereGeometry(25, 50, 50); //半径 水平分段数 垂直分段数

        var texture = new Three.TextureLoader().load(require( '../assets/00125.jpg'));
        // var material = new Three.MeshBasicMaterial({map:texture});
        var material = new Three.MeshBasicMaterial({
            color: 0xffffff,
            side: Three.BackSide,
        });
        // let material = new Three.MeshNormalMaterial()
        this.mesh = new Three.Mesh(geometry, material)
        this.mesh.material.map = texture;
        this.scene.add(this.mesh)

        this.renderer = new Three.WebGLRenderer({antialias: true})
        this.renderer.setSize(window.innerWidth, window.innerHeight)
        document.body.appendChild(this.renderer.domElement)

        var controls = new OrbitControls(this.camera, this.renderer.domElement);
        controls.enablePan = false; //是否开启右键拖拽
        // 使动画循环使用时阻尼或自转 意思是否有惯性 
        controls.enableDamping = true; 
        //是否可以缩放 
        controls.enableZoom = false; 
        //是否自动旋转 
        controls.autoRotate = true; 
        //设置相机距离原点的最远距离 
        // controls.minDistance = 200; 
        //设置相机距离原点的最远距离 
        // controls.maxDistance = 600; 

      },
      animate: function () {
        requestAnimationFrame(this.animate)
        // this.mesh.rotation.y += 0.005
        this.renderer.render(this.scene, this.camera)
      },
      audioClick(){
         if (this.audioBoool) {
              this.audioBoool = false;
              // audio.pause()
            } else {
                this.audioBoool = true;
                // audio.play()
            }
      }
    },
    mounted () {
      this.init()
      this.animate()
    }
}
</script>

<style scoped>
.content{
  position:relative;
}
.audioBtn{
  width: 30px;
  height: 30px;
  line-height: 39px;
  border-radius: 50%;
  background:#DCDFE6;
  position:absolute;
  right:10px;
  top:10px
}
</style>
