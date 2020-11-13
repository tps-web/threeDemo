<template>
  <div id="content">
     <div class="audioBtn" @click="audioClick">
       <img src="../assets/playAudio.png" alt="" height="20" width="20" v-show="audioBoool"/>
       <img src="../assets/closeAudio.png" alt="" height="20" width="20" v-show="!audioBoool"/>
     </div>
    
     <div class="com" style="position: absolute;left:5px;" :style="{top:height/2-40+'px'}" @click="upClick">
       <img src="../assets/left.png" alt="" height="20" width="20"/>
     </div>  
     <div class="com" style="position: absolute;right:5px;" :style="{top:height/2-40+'px'}" @click="nextClick">
       <img src="../assets/right.png" alt="" height="20" width="20"/>
     </div>  
  </div>
</template>

<script>
  import * as Three from 'three';
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
var audio,that,textureLoader,clock,update,timeS
  export default {
  name: 'App',
  data () {
    return {
      width: window.innerWidth,
      height: window.innerHeight,
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      audioBoool: true,  //是否播放音乐
      path:[{id:1,url:'0011.jpg'},{id:2,url:'00110.jpg'},{id:3,url:'00125.jpg'}],
      current:'',
      currentNum:0
    }
  },
  created(){
    that=this
    this.current=this.path[this.currentNum]
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

       
        textureLoader = new Three.TextureLoader();
        var listener = new Three.AudioListener();
         audio = new Three.Audio(listener);
        var texture = textureLoader.load(require(`../assets/${this.current.url}`), function(obj) {
            var audioLoader = new Three.AudioLoader();
            audioLoader.load('../assets/1.mp3', function(AudioBuffer) {
                audio.setBuffer(AudioBuffer);
                audio.setLoop(true);
                // audio.setVolume(0.3);
                // audio.play()
            });
        });
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


        clock = new THREE.Clock();
        var FPS = 30;
         update = 1 / FPS;
         timeS = 0;


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
         this.animate()

      },
      animate: function () {
        requestAnimationFrame(this.animate)

         var 渲染间隔 = clock.getDelta();
            timeS = timeS + 渲染间隔;
            if (timeS > update) {
                  this.renderer.render(this.scene, this.camera)
                  this.mesh.rotateY(0.002)
                  timeS = 0
            }
        // this.mesh.rotation.y += 0.005
        //  this.mesh.rotateY(0.002)
        // this.renderer.render(this.scene, this.camera)
      },
      audioClick(){
         if (this.audioBoool) {
              this.audioBoool = false;
                audio.pause()
            } else {
                this.audioBoool = true;
                audio.play()
            }
      },
      nextClick(){
        console.log('下一张')
        if(this.currentNum<this.path.length-1){ 
           this.currentNum++
           this.current=this.path[this.currentNum]

        }else{
          this.currentNum=0
          this.current=this.path[0]
        }
      },
      upClick(){
        console.log('上一张')
          if (this.currentNum > 0) {
                this.currentNum -= 1
                this.current=this.path[this.currentNum]
              } else {
                  this.currentNum = this.path.length-1
                  this.current=this.path[this.currentNum]
            }
      }
    },
    watch:{
        currentNum: function(value) {
         var texture = textureLoader.load(require(`../assets/${this.current.url}`), function(obj) {
              // that.animate()
         });
           that.mesh.material.map = texture;
          //  that.scene.add(that.mesh)
           that.animate()
      }
    },
    mounted () {
      this.init()
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
  right:20px;
  top:10px
}
.com{
  background:#DCDFE6;
  width: 35px;
  height: 35px;
  border-radius: 50%;
  line-height:45px;
}
</style>
