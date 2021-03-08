<template>
  <div id="app"></div>
</template>


<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { FBXLoader } from "three/examples/jsm/loaders/FBXLoader.js";
import { TGALoader } from "three/examples/jsm/loaders/TGALoader.js";
export default {
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
    };
  },
  mounted() {
    this.init();
    this.animate();
    window.addEventListener("resize", this.onWindowSize());
  },
  methods: {
    //初始化
    init() {
      //  创建场景对象Scene
      this.scene = new THREE.Scene();
      this.fbxLoader = new FBXLoader();
      var axes = new THREE.AxisHelper(100);
      //网格模型添加到场景中
      // let geometry = new THREE.BoxGeometry(10, 11, 11);
      var material = new THREE.MeshLambertMaterial({
        color: 0xffffff,
        side: THREE.DoubleSide,
      }); //创建材质
      // var mesh = new THREE.Mesh(geometry, material);
      //添加光线
      var light = new THREE.DirectionalLight(0xffffff);
      light.position.set(20, 50, 60);
      this.scene.add(light);
      this.fbxLoader.load("/static/QTZSB.FBX", (object) => {
        object.position.set(0,-20,0)
        object.scale.multiplyScalar(0.35);
        console.log(object.children);

        // this._ChangeMaterialEmissive(object);
        this.scene.add(axes);
        this.scene.add(object);
        var axis = new THREE.Vector3(1, 0, 0); //向量axis
        object.children[1].rotateOnWorldAxis(axis, 3*Math.PI /2); //绕axis轴旋转π/8
        object.children[4].rotateOnWorldAxis(axis, 3*Math.PI /2); //绕axis轴旋转π/8
        object.children[1].translateY(-25); 
        object.children[4].translateY(-25); 
        object.children[1].translateZ(-25); 
        object.children[4].translateZ(-25);
        setInterval(() => {
          //   console.log("删除组件");
          var axis = new THREE.Vector3(1, 0, 0); //向量axis
          // object.children[1].rotateOnWorldAxis (axis, Math.PI / 8); //绕axis轴旋转π/8
          // object.children[4].rotateOnWorldAxis (axis, Math.PI / 8); //绕axis轴旋转π/8
          // object.children[10].rotateOnWorldAxis (axis, Math.PI / 8); //绕axis轴旋转π/8
          // object.children[11].rotateOnWorldAxis (axis, Math.PI / 8); //绕axis轴旋转π/8
          // object.children[8].rotateOnWorldAxis (axis, Math.PI / 8); //绕axis轴旋转π/8
          object.rotateY(Math.PI / 8);
        }, 260);
        // var mesh = new THREE.Mesh(object.children[11].geometry, material);
        // mesh.scale.set(0.4, 0.4, 0.4);
        // mesh.rotateX(Math.PI / 2);
        // mesh.position.set(50, 0, 0);
        // this.scene.add(mesh);
        // object.children.forEach((item, index) => {
        //   console.log(item);
        //   var mesh = new THREE.Mesh(item.geometry, material);
        //   mesh.rotateX(Math.PI / 2);
        //   // mesh.scale.set(0.4, 0.4, 0.4)
        //   this.scene.add(mesh);
        // });
      });
      /**
       * 相机设置
       */

      let container = document.getElementById("app");
      this.camera = new THREE.PerspectiveCamera(
        60,
        container.clientWidth / container.clientHeight,
        0.01,
        1000
      );
      this.camera.position.set(20, 40, 80);
      // this.camera.lookAt(this.scene.position);
      /**
       * 创建渲染器对象
       */
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setClearColor("#fff", 1); //设置背景颜色
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      container.appendChild(this.renderer.domElement);

      //创建控件对象
      new OrbitControls(this.camera, this.renderer.domElement);
    },
    _ChangeMaterialEmissive(parent) {
      parent.traverse(function (obj) {
        if (obj instanceof THREE.Mesh) {
          obj.material.emissive = new THREE.Color(1, 1, 1);
          obj.material.emissiveIntensity = 1;
          obj.material.emissiveMap = obj.material.map;
        }
      });
    },
    // 窗口变化
    onWindowSize() {
      let container = document.getElementById("app");
      (this.camera.aspect = container.clientWidth / container.clientHeight),
        this.camera.updateProjectionMatrix();
      this.renderer.setSize(container.clientWidth, container.clientHeight);
    },
    // 动画
    animate() {
      requestAnimationFrame(this.animate);
      //   this.mesh.rotation.x += 0.01;
      //   this.mesh.rotation.y += 0.02;
      this.renderer.render(this.scene, this.camera);
    },
  },
};
</script>

<style>
#app {
  position: absolute;
  width: 100%;
  height: 100%;
}
</style>