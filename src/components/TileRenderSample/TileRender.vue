    <template>
        <div id="tileRenderContainer">
            <div id="view"></div>
        </div>
    </template>

    <script>
        import { TilesRenderer } from '3d-tiles-renderer';
        import { Scene, PerspectiveCamera, WebGLRenderer, PCFSoftShadowMap, sRGBEncoding, BoxGeometry, MeshLambertMaterial, Mesh, AmbientLight, SpotLight } from "three";
        export default {
            name: "TileRender",
            data() {
                return {
                }
            },
            mounted() {
                this.initMainScene();
                this.initViewScene();
            },
            methods: {
                initMainScene: function () {
                    const scene = new Scene();
                    // primary camera view
                    let renderer = new WebGLRenderer( { antialias: true } );
                    renderer.setPixelRatio( window.devicePixelRatio );
                    renderer.setSize( window.innerWidth, window.innerHeight );
                    renderer.setClearColor( 0x151c1f );
                    renderer.shadowMap.enabled = true;
                    renderer.shadowMap.type = PCFSoftShadowMap;
                    renderer.outputEncoding = sRGBEncoding;

                    document.querySelector('#tileRenderContainer').appendChild( renderer.domElement );
                    const tilesRenderer = new TilesRenderer('http://192.168.6.166:8101/3dtiles/fxdq/lab_b_0.json');
                    let camera = new PerspectiveCamera( 60, window.innerWidth / window.innerHeight, 0.1, 4000 );
                    camera.position.set( -200, 300, 400 );
                    camera.lookAt(scene.position);
                    tilesRenderer.setCamera( camera );
                    tilesRenderer.setResolutionFromRenderer( camera, renderer );
                    scene.add( tilesRenderer.group );
                    const renderLoop = () => {
                        requestAnimationFrame( renderLoop );

                        // The camera matrix is expected to be up to date
                        // before calling tilesRenderer.update
                        camera.updateMatrixWorld();
                        tilesRenderer.update();
                        renderer.render(scene, camera);
                    }
                    renderLoop();
                },
                initViewScene: function () {
                    const scene = new Scene();
                    // primary camera view
                    let renderer = new WebGLRenderer( { antialias: true } );
                    renderer.setPixelRatio( window.devicePixelRatio );
                    renderer.setSize( 200, 200 );
                    renderer.setClearColor( 0xeeeeee );
                    renderer.shadowMap.enabled = true;
                    renderer.shadowMap.type = PCFSoftShadowMap;
                    renderer.outputEncoding = sRGBEncoding;

                    document.querySelector('#view').appendChild( renderer.domElement );
                    let camera = new PerspectiveCamera( 45, 1, 0.1, 1000);
                    camera.position.set( -20, 30, 40 );
                    camera.lookAt(scene.position);
                    let cubeGeometry = new BoxGeometry(15, 15, 15);
                    let cubeMaterial = new MeshLambertMaterial({color: 0x7777ff});
                    let cube = new Mesh(cubeGeometry, cubeMaterial);

                    cube.position.x = 0;
                    cube.position.y = 3;
                    cube.position.z = 2;

                    scene.add(cube);

                    // let textOptions = {
                    //     size: 90,
                    //     height: 90,
                    //     bevelThickness: 2,
                    //     bevelSize: 0.5,
                    //     bevelEnabled: true,
                    //     bevelSegments: 3,
                    //     curveSegments: 12,
                    //     steps: 1,
                    //     font: "helvetiker",
                    //     weight: "normal"
                    // };
                    // let text1 = createMesh(new TextGeometry("上", textOptions));
                    // scene.add(text1);

                    let ambientLight = new AmbientLight(0x0c0c0c);
                    scene.add(ambientLight);

                    // add spotlight for the shadows
                    let spotLight = new SpotLight(0xffffff);
                    spotLight.position.set(-30, 60, 60);
                    spotLight.castShadow = true;
                    scene.add(spotLight);

                    const renderLoop = () => {
                        requestAnimationFrame( renderLoop );
                        camera.updateMatrixWorld();
                        renderer.render(scene, camera);
                    }
                    renderLoop();
                }
            }
        }
    </script>

    <style scoped>
        #tileRenderContainer {
            height: 100vh;
            overflow: hidden;
        }
        #view {
            position: fixed;
            left: 0;
            top: 0;
            width: 200px;
            height: 200px;
        }
    </style>