<template>
  <div class="map-box">
    <div class="init" v-if="showInit">
      <button class="init-button" @click="initDemo">{{Msg}}</button>
    </div>
    <div id="cesiumContainer"></div>
    <div class="tool-box" v-if="!showInit">
      <div class="tool-item" @click="cameraAnimate">
        <div>相机动画1</div>
      </div>
      <div class="tool-item" @click="cameraAnimate2">
        <div>相机动画2</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "",
  data() {
    return {
      showInit: true,
      Msg: "First Cesium Learning Demo",
      stopRotate: false,
      nowI: Date.now(),
      isPlayAnimation: false,
    }
  },
  mounted() {
    window.viewer = new Cesium.Viewer('cesiumContainer', {
      shadows: false,
      animation: false,                   // [ Bool, 是否显示动画控件 ]
      shouldAnimate: true,                // [ Bool, 是否开启动画 ]
      homeButton: false,                  // [ Bool, 是否显示Home按钮 ]
      vrButton: false,                    // [ Bool, 是否显示vr按钮 ]
      fullscreenButton: false,            // [ Bool, 是否显示全屏按钮 ]
      baseLayerPicker: false,             // [ Bool, 是否显示图层选择控件 ]
      geocoder: false,                    // [ Bool, 是否显示地名查找控件 ]
      timeline: false,                    // [ Bool, 是否显示时间线控件 ]
      navigationHelpButton: false,        // [ Bool, 是否显示帮助信息控件 ]
      infoBox: false,                     // [ Bool, 是否显示点击要素之后显示的信息 ]
      requestRenderMode: true,            // [ Bool, 启用请求渲染模式 ]
      scene3DOnly: false,                 // [ Bool, 每个几何实例将只能以3D渲染以节省GPU内存 ]
      sceneModePicker: false,             // [ Bool, 是否显示场景切换控件 ]
      sceneMode: 3,                       // [ Number,初始场景模式 1 2D模式 2 2D循环模式 3 3D模式  Cesium.SceneMode ]
      fullscreenElement: document.body,   // [ Object, 全屏时渲染的HTML元素 ]
    })

    window.viewer.imageryLayers.remove(window.viewer.imageryLayers.get(0))
    let img = window.viewer.imageryLayers.addImageryProvider(
            new Cesium.ArcGisMapServerImageryProvider({
              url: 'https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer',
              baseLayerPicker: false
            })
    )
    img.brightness = 0.8

    window.viewer.scene.undergroundMode = false                                  // [ Bool , 设置开启地下场景 ]
    window.viewer.scene.terrainProvider.isCreateSkirt = false                    // [ Bool , 关闭裙边 ]
    window.viewer.scene.globe.enableLighting = true                              // [ Bool , 是否添加全球光照，scene(场景)中的光照将会随着每天时间的变化而变化 ]
    window.viewer.scene.globe.showGroundAtmosphere = true                        // [ Bool , 是否关闭大气效果 ]
    // viewer.scene.globe.depthTestAgainstTerrain = true                  // [ Bool , 地面以下不可见（高程遮挡） ]
    window.viewer._cesiumWidget._creditContainer.style.display = "none"          // [ String , 隐藏logo ]

    // 地球自转开启
    this.earthRotate();
    // this.preload();
  },
  methods: {
    earthRotate: function () {
      window.viewer.clock.onTick.addEventListener(this.rotate)
    },
    stopEarthRotate: function () {
      window.viewer.clock.onTick.removeEventListener(this.rotate)
    },
    rotate: function () {
      let a = .1
      let t = Date.now()
      let n = (t - this.nowI) / 1e3
      this.nowI = t
      window.viewer.scene.camera.rotate(Cesium.Cartesian3.UNIT_Z, -a * n)
    },
    initDemo: function() {
      this.showInit = false;
      this.stopEarthRotate();
      window.viewer.clock.shouldAnimate = true;
      let tileset = window.viewer.scene.primitives.add(
              new Cesium.Cesium3DTileset({
                url: 'http://192.168.6.166:8101/3dtiles/fxdq/fxdq1.json',
                maximumMemoryUsage: 2048
              })
      );
      tileset.readyPromise.then(() => {
        window.viewer.flyTo(tileset);
      }).otherwise( error => {
        console.log(error);
      });
    },
    cameraAnimate: function () {
      if (this.isPlayAnimation) {
        return;
      }
      this.isPlayAnimation = true;
      let startTime = Date.now(), cameraMoveAnimation;
      let startPosition = {
        destination: new Cesium.Cartesian3(-2177201.4790705508, 4389091.085317371, 4070016.141447827),
        orientation: {
          heading: 6.230264015136325,
          pitch: 0.03943820405688947,
          roll: 0.00007987714552726288,
        }
      }
      window.viewer.camera.flyTo({
        ...startPosition,
        complete: () => {
          this.nowI = Date.now();
          window.viewer.clock.onTick.addEventListener(cameraMoveAnimation = () => {
            let a = 30
            let t = Date.now()
            let n = (t - this.nowI) / 1e3
            this.nowI = t
            window.viewer.scene.camera.moveRight(a * n);
            window.viewer.scene.camera.moveBackward(a * n * 0.1);
            if (t - startTime > 45000) {
              this.isPlayAnimation = false;
              window.viewer.clock.onTick.removeEventListener(cameraMoveAnimation)
            }
          })
        }
      });
    },
    cameraAnimate2: function () {
      if (this.isPlayAnimation) {
        return;
      }
      this.isPlayAnimation = true;
      let startTime = Date.now(), cameraMoveAnimation;
      let startPosition = {
        destination: new Cesium.Cartesian3(-2178452.460839313, 4388456.767053101, 4070082.220391774),
        orientation: {
          heading: 4.731120743519466,
          pitch: -0.221113242361066,
          roll: 6.27979986919941,
        }
      }
      window.viewer.camera.flyTo({
        ...startPosition,
        complete: () => {
          this.nowI = Date.now();
          window.viewer.clock.onTick.addEventListener(cameraMoveAnimation = () => {
            if (!this.isPlayAnimation) {
              return;
            }
            requestAnimationFrame(cameraMoveAnimation)
            let a = 30
            let t = Date.now()
            let n = (t - this.nowI) / 1e3
            this.nowI = t
            window.viewer.scene.camera.moveForward(a * n);
            window.viewer.scene.camera.moveUp(a * n * 0.25);
            if (t - startTime > 35000) {
              this.isPlayAnimation = false;
              window.viewer.clock.onTick.removeEventListener(cameraMoveAnimation);
            }
          });
        }
      })
    }
  }
};
</script>
<style scoped>
  .init {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 1000000;
    width: 100%;
    height: 100%;
    background-color: rgba(0,0,0,.6);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .init-button {
    background-color: rgba(0,0,0,.5);
    border: 2px solid #eeefff;
    color: aliceblue;
    border-radius: 4px;
  }
  .map-box {
    width: 100%;
    height: 100%;
  }
  #cesiumContainer {
    width: 100%;
    height: 100%;
  }
  .tool-box {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 100000;
    width: 48%;
    height: 50px;
    display: flex;
    align-items: center;
  }
  .tool-item {
    width: 120px;
    height: 40px;
    background: url(/static/nav_bg.png) no-repeat;
    background-size: 100% 100%;
    cursor: pointer;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: #5CBBF0;
  }
</style>
