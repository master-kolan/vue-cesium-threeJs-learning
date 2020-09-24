<template>
    <div id="view">
    </div>
</template>

<script>
    import {
        AmbientLight,
        BoxGeometry, Mesh,
        MeshLambertMaterial,
        PCFSoftShadowMap,
        PerspectiveCamera,
        Scene, SpotLight,
        sRGBEncoding,
        WebGLRenderer
    } from "three";
    import TWEEN from '@tweenjs/tween.js'
    export default {
        name: "ThreeJsSample",
        data() {
            return {
                boxList: [],
                render: null
            }
        },
        mounted() {
            this.initScene();
            this.rotate();
            this.animate();
        },
        methods: {
            initScene: function () {
                const scene = new Scene();
                // primary camera view
                let renderer = new WebGLRenderer({antialias: true});
                renderer.setPixelRatio(window.devicePixelRatio);
                renderer.setSize(window.innerWidth, window.innerHeight);
                renderer.setClearColor(0xeeeeee);
                renderer.shadowMap.enabled = true;
                renderer.shadowMap.type = PCFSoftShadowMap;
                renderer.outputEncoding = sRGBEncoding;
                document.querySelector('#view').appendChild(renderer.domElement);
                let camera = new PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 4000);
                camera.position.set(-40, 60, 80);
                camera.lookAt(scene.position);
                let cubeGeometry = new BoxGeometry(20, 1, 20);
                let cubeMaterial = new MeshLambertMaterial({color: 0xbdc3c7});
                for (let i = 0; i < 30; i++) {
                    let cube = new Mesh(cubeGeometry, cubeMaterial);
                    cube.position.x = -10;
                    cube.position.y = i;
                    cube.position.z = -10;
                    this.boxList.push(cube);
                    scene.add(cube);
                }
                let ambientLight = new AmbientLight(0x0c0c0c);
                scene.add(ambientLight);
                let spotLight = new SpotLight(0xffffff);
                spotLight.position.set(-30, 60, 60);
                spotLight.castShadow = true;
                scene.add(spotLight);
                this.render = () => {
                    renderer.render(scene, camera);
                }
                this.render();
            },
            animate: function () {
                requestAnimationFrame(this.animate);
                this.render();
                TWEEN.update();
            },
            rotate: function () {
                let rotation = {y: 0};
                this.boxList.forEach((cube, index) => {
                    let tween = new TWEEN.Tween(rotation).to({y: Math.PI}, 1500 - index * 10).delay(index * 10);
                    tween.easing(TWEEN.Easing.Sinusoidal.InOut);
                    let  tweenBack = new TWEEN.Tween(rotation).to({y: 0}, 1500 - index * 10).delay(index * 10);
                    tweenBack.easing(TWEEN.Easing.Sinusoidal.InOut);
                    tween.chain(tweenBack);
                    tweenBack.chain(tween)
                    let onUpdate = () => cube.rotation.y = rotation.y;
                    tween.onUpdate(onUpdate);
                    tweenBack.onUpdate(onUpdate);
                    tween.start();
                });
            }
        }
    }
</script>

<style scoped>

</style>