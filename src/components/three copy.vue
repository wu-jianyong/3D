<template>
  <div>
    <div>
      <div class="left-trees"></div>
      <div>
        <div id="container"></div>
      </div>
    </div>
  </div>
</template>
 
<script>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import { FBXLoader } from "three/examples/jsm/loaders/FBXLoader.js";
import { STLLoader } from "three/examples/jsm/loaders/STLLoader";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

export default {
  name: "ThreeTest",
  data() {
    return {};
  },
  methods: {
    init() {
      this.scene = new THREE.Scene();
      this.clock = new THREE.Clock();
      this.camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        100
      );

      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.fbxLoader = new FBXLoader();
      this.stlLoader = new STLLoader();
      this.orbitControls = new OrbitControls(
        this.camera,
        this.renderer.domElement
      );
      this.animationMixer = new THREE.AnimationMixer(this.scene);

      this.renderer.setSize(window.innerWidth, window.innerHeight);

      // document.body.appendChild(this.renderer.domElement)
      document
        .getElementById("container")
        .appendChild(this.renderer.domElement);
      window.addEventListener("resize", () => this.onWindowResize());

      this.camera.position.set(-0.5, 2, 1);
      const planeBufferGeometry = new THREE.PlaneBufferGeometry(80, 80);
      const plane = new THREE.Mesh(
        planeBufferGeometry,
        new THREE.MeshBasicMaterial({
          color: 0xffffff,
          side: THREE.DoubleSide,
        })
      );
      plane.name = "plane";
      plane.rotation.x = -Math.PI / 2;
      this.scene.add(plane);
      this.scene.add(new THREE.GridHelper(10, 10));

      //   this.fbxLoader.load(
      //     `/static/QTZSB.FBX`,
      //     (gltf) => {
      //         console.log(gltf)
      //     //   gltf.scene.name = "Soldier";
      //     //   gltf.scene.rotation.y = Math.PI;
      //       this.scene.add(gltf);
      //     //   this.treeData = [gltf.scene];
      //     //   this.scene.add(new THREE.AmbientLight(0xffffff, 2));

      //     //   this.orbitControls.target.set(0, 1, 0);

      //     //   const animationClip = gltf.animations.find(
      //     //     (animationClip) => animationClip.name === "Walk"
      //     //   );
      //     //   this.walkAction = this.animationMixer.clipAction(animationClip);
      //     //   this.walkAction.play();
      //     }
      //   );

      //Samba Dancing idle_2
      this.fbxLoader.load("/static/QTZSB.FBX",(object)=>{
        object.scale.multiplyScalar(.1);
        console.log(object);
        // this.mixers = object.mixer = new THREE.AnimationMixer(object);
        // var action = object.mixer.clipAction(object.animations[0]);
        // action.play();

        // object.traverse(function (child) {
        //     // console.log(child)
        //   if (child.isMesh) {
        //     child.castShadow = true;
        //     child.receiveShadow = true;
        //   }
        // });
        // object.scale.set(0.5, 0.5, 0.5);
        // object.position.set(0, 0, 0);
        this.scene.add(object);
      });
      this.renderer.domElement.addEventListener("click", (event) => {
        const { offsetX, offsetY } = event;
        const x = (offsetX / window.innerWidth) * 2 - 1;
        const y = -(offsetY / window.innerHeight) * 2 + 1;
        const mousePoint = new THREE.Vector2(x, y);
        const raycaster = new THREE.Raycaster();
        // 设置鼠标位置和参考相机
        raycaster.setFromCamera(mousePoint, this.camera);
        // 鼠标点击对应的物体（所有鼠标映射到的物体，包括被遮挡的）
        const intersects = raycaster.intersectObjects(
          this.scene.children,
          true
        );
        // console.log(intersects)

        // 过滤网格和地面
        const intersect = intersects.filter(
          (intersect) =>
            !(intersect.object instanceof THREE.GridHelper) &&
            intersect.object.name !== "plane"
        )[0];
        if (intersect && this.isClickSoldier(intersect.object)) {
          // 停止动画
          // this.walkAction.stop();
          // 暂停动画
          this.walkAction.paused = !this.walkAction.paused;
          console.log(intersect);

          intersect.object.material.color.set(0xff0000);
        }
      });
      this.render();
    },
    isClickSoldier(object) {
      if (object.name === "Soldier") {
        return object;
      } else if (object.parent) {
        return this.isClickSoldier(object.parent);
      } else {
        return null;
      }
    },
    render() {
      // 更新动画
      this.animationMixer.update(this.clock.getDelta());

      this.renderer.render(this.scene, this.camera);

      this.orbitControls.update();
      window.requestAnimationFrame(() => this.render());
      // this.stats.update()
    },

    onWindowResize() {
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
    },
  },
  mounted() {
    this.init();
  },
};
</script>
<style >
#container {
  height: 400px;
}
</style>
