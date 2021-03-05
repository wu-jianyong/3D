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

      //网格模型添加到场景中
      //   let geometry = new THREE.BoxGeometry(40, 20, 20);
      let material = new THREE.MeshBasicMaterial({
        color: "#ff0000",
      });
      //   this.mesh = new THREE.Mesh(geometry, material);
      //   this.scene.add(this.mesh);

      this.fbxLoader.load("/static/QTZSB.FBX", (object) => {
        object.translateY(-10);
        object.rotateY(10);
        object.scale.multiplyScalar(0.25);
        console.log(object.children);

        this._ChangeMaterialEmissive(object);
        this.scene.add(object);
        setTimeout(() => {
          console.log("删除组件");
          // object.children[1].rotateZ(Math.PI);
          //   object.remove(object.children[0]);
          console.log(object.children);
        }, 1000);
        var mesh = new THREE.Mesh(object.children[0].geometry, material);
        mesh.scale.set(0.5, 0.5, 0.5);
        mesh.rotateZ(Math.PI / 2);

        // this.scene.add(mesh);
        // object.children.forEach((item, index) => {
        //   console.log(item);
        //   var mesh = new THREE.Mesh(item.geometry, material);
        //   this.scene.add(mesh);
        // });
        var light = new THREE.DirectionalLight(0xffffff);
        light.position.set(0, 0, 50).normalize();
        this.scene.add(light);
      });
      /**
       * 相机设置
       */

      let container = document.getElementById("app");
      this.camera = new THREE.PerspectiveCamera(
        70,
        container.clientWidth / container.clientHeight,
        0.01,
        1000
      );
      this.camera.position.set(0, 0, 50);
      this.camera.lookAt(this.scene.position);
      /**
       * 创建渲染器对象
       */
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setClearColor("pink", 1); //设置背景颜色
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