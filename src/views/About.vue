<template>
    <div>
        <div id="Stats-output"></div>
        <div>
            <div class="left-trees">
            </div>
            <div>
                <div id="container"></div>
            </div>
        </div>
    </div>
</template>
 
<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import { STLLoader } from 'three/examples/jsm/loaders/STLLoader'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import Stats from 'stats-js'
export default {
    name: 'ThreeTest',
    data() {
        return {
            
            clock: undefined, // 时钟对象
            camera: undefined, // 相机对象
            renderer: undefined, // 渲染器
            gltfLoader: undefined, // glt文件
            stlLoader: undefined, // stl文件
            orbitControls: undefined, //相机控件
            animationMixer: undefined, // 动画
            stats: undefined, // 帧率
            spotLight: undefined, // 点光源
        }
    },
    methods: {
        init() {
            //创建一个场景（场景是一个容器，用于保存、跟踪所要渲染的物体和使用的光源）
            this.scene = new THREE.Scene()
            // 创建一个时钟对象Clock
            this.clock = new THREE.Clock()
            //创建一个摄像机对象（摄像机决定了能够在场景里看到什么）
            /* PerspectiveCamera(
                Fov– 相机的视锥体的垂直视野角, 
                Aspect– 相机视锥体的长宽比, 
                Near– 相机视锥体的近平面, 
                Far– 相机视锥体的远平面) */

            this.camera = new THREE.PerspectiveCamera(
                75,
                window.innerWidth / window.innerHeight,
                0.1,
                100
            )
            /*参数 WebGLRenderer({
                antialias: true, // true/false表示是否开启反锯齿
                alpha: true, // true/false 表示是否可以设置背景色透明
                precision: 'highp', // highp/mediump/lowp 表示着色精度选择
                premultipliedAlpha: false, // true/false 表示是否可以设置像素深度（用来度量图像的分辨率）
                preserveDrawingBuffer: true, // true/false 表示是否保存绘图缓冲
                maxLights: 3, // 最大灯光数
                stencil: false // false/true 表示是否使用模板字体或图案
            });*/

            // 渲染器将和Canvas元素进行绑定
            this.renderer = new THREE.WebGLRenderer({ antialias: true })
            // GLT STL 文件
            this.gltfLoader = new GLTFLoader()
            this.stlLoader = new STLLoader()
            
            /* this.stlLoader.load('../assets/180741_K6KK20190330014013_color.stl', geometry => {
                console.log(geometry)
            }) */
            // 渲染stl文件到场景中 (文件, )
            this.stlLoader.load('180741_K6KK20190330014013_color.stl', geometry => {
                // 返回对象的几何结构 查看顶点数 
                console.log('geometry', geometry);
                // 材质 暗淡不光亮表面
                var mat = new THREE.MeshLambertMaterial({ 
                    color: 'red', //{ color: 0x00ffff }color属性是漫反射的颜色
                    });
                // 
                var mesh = new THREE.Mesh(geometry, mat);//网格模型对象Mesh

                mesh.rotation.x = -0.5 * Math.PI; //将模型摆正
                mesh.scale.set(0.1, 0.1, 0.1); //缩放
                mesh.translateX(2)    //移动X轴位置
                // mesh.translateY(0.2)    //移动Y轴位置
                mesh.translateZ(0.08)    //移动Z轴位置
               // geometry.center(); //居中显示
                this.scene.add(mesh);//将网格模型对象渲染进场景
            });
            // 渲染stl文件到场景中 (文件, )
            this.stlLoader.load('80741_K6KK20190330014013_color.stl', geometry => {
                // 返回对象的几何结构 查看顶点数 
                console.log('geometry', geometry);
                // 材质 暗淡不光亮表面
                var mat = new THREE.MeshLambertMaterial({ 
                    color: 	'#FF0', //{ color: 0x00ffff }color属性是漫反射的颜色
                    });
                // 
                var mesh = new THREE.Mesh(geometry, mat);//网格模型对象Mesh

                mesh.rotation.x = -0.5 * Math.PI; //将模型摆正
                mesh.scale.set(0.05, 0.05, 0.05); //缩放
                mesh.position.set(-3.5,0.05,0)    //设置位置
                mesh.rotation.set(-90,0,0)

               // geometry.center(); //居中显示
                this.scene.add(mesh);//将网格模型对象渲染进场景
            });

               /*  光源大概有7种，
               其中基础光源有4种
                环境光（AmbientLight会它的颜色会添加到整个场景和所有对象的当前颜色上），
                点光源（PointLight空间中的一点，朝所有的方向发射光线），
                聚光灯光源（SpotLight这种光源有聚光的效果，类似于台灯，吊灯，手电筒），
                方向光（DirectionalLight也称无限光，从这种光源发出的光线可以看作是平行的，例如太阳光）
                特殊光源有3种(这三种我们后面再详述)
                半球光光源（HemisphereLight，可以为室内场景创建更加自然的光照效果，模拟反光面和光线微弱的天气）
                面光源（AreaLight使用这种光源可以指定散发光线的平面，而不是空间的一个点）
                镜头炫光（LensFlare这不是一种光源，但是通过该炫光可以为场景中的光源添加炫目的效果） */
            //创建点光源
            this.spotLight = new THREE.SpotLight(0xC0C0C0); // 设置光源
            this.spotLight.position.set(-40, 60, -10); // 设置光源位置
            this.spotLight.castShadow = true;
            this.scene.add(this.spotLight);

            // 相机控件 缩放、平移、旋转操作 通过this.renderer.domElement控制OrbitControls的作用范围 为画布范围
            this.orbitControls = new OrbitControls(
                this.camera,
                this.renderer.domElement
            )
            /* 
            orbitControls.enablePan =false; //禁止右键拖拽
            orbitControls.enableZoom =false; //禁止缩放
            contorbitControlsrols.enableRotate =false; //禁止旋转 
            orbitControls.minZoom =0.5;orbitControls.maxZoom =2; // 缩放范围
            orbitControls.minPolarAngle =0;orbitControls.maxPolarAngle =Math.PI; // 上下旋转范围
            orbitControls.minAzimuthAngle = -Math.PI * (100/180);orbitControls.maxAzimuthAngle =Math.PI * (100/180); // 左右旋转范围
            OrbitControls的变化事件change
            //监听鼠标事件，触发渲染函数，更新canvas画布渲染效果controls.addEventListener('change', render);
            */
            
            //AnimationMixer是场景中特定对象的动画播放器。当场景中的多个对象独立动画时，可以为每个对象使用一个AnimationMixer
            this.animationMixer = new THREE.AnimationMixer(this.scene)
            // 设置canvas 的大小 
            this.renderer.setSize(window.innerWidth, window.innerHeight)
            // 将three.js生成的画布放进页面
            document
                .getElementById('container')
                .appendChild(this.renderer.domElement)
            window.addEventListener('resize', () => this.onWindowResize())
            //相机位置
            this.camera.position.set(0, 5, 5)
            // 生成网格
            const planeBufferGeometry = new THREE.PlaneBufferGeometry(100, 100)
            const plane = new THREE.Mesh(
                planeBufferGeometry,
                new THREE.MeshBasicMaterial({
                    color: 0xf0f0f0,
                    side: THREE.DoubleSide
                })
            )
            plane.name = 'plane'
            plane.rotation.x = -Math.PI / 2
            // 生成xyz = 1、2、3的立方体
            /* const cube = new THREE.Mesh(new THREE.CubeGeometry(1, 2, 3),
            new THREE.MeshBasicMaterial({
                        color: 0xC0C0C0,
                        // wireframe: true //线框
                    })
            );
            this.scene.add(cube); */
            // 网格放入场景
            this.scene.add(plane)
            this.scene.add(new THREE.GridHelper(10, 10))

           // 渲染glt文件
            this.gltfLoader.load(
                'Soldier.glb',
                gltf => {
                   var mesh = gltf.scene;
                   mesh.name = 'Soldier'
                    mesh.rotation.y = Math.PI
                     this.scene.add(mesh); // 将模型引入three
                     this.treeData = [mesh]
    
                    this.scene.add(new THREE.AmbientLight(0xC0C0C0, 1))
                    
                    mesh.scale.set(2,2,2);
                    mesh.position.set(0, 0, 0 );

                    this.orbitControls.target.set(0, 1, 0)
                    // 动画
                    const animationClip = gltf.animations.find(
                        animationClip => animationClip.name === 'Walk'
                    )
                    this.walkAction = this.animationMixer.clipAction(
                        animationClip
                    )
                    this.walkAction.play()
                }
            )

            // 点击停止动画
            this.renderer.domElement.addEventListener('click', event => {
                const { offsetX, offsetY } = event
                const x = (offsetX / window.innerWidth) * 2 - 1
                const y = -(offsetY / window.innerHeight) * 2 + 1
                const mousePoint = new THREE.Vector2(x, y)
                const raycaster = new THREE.Raycaster()
                // 设置鼠标位置和参考相机
                raycaster.setFromCamera(mousePoint, this.camera)
                // 鼠标点击对应的物体（所有鼠标映射到的物体，包括被遮挡的）
                const intersects = raycaster.intersectObjects(
                    this.scene.children,
                    true
                )
                // console.log(intersects)

                // 过滤网格和地面
                const intersect = intersects.filter(
                    intersect =>
                        !(intersect.object instanceof THREE.GridHelper) &&
                        intersect.object.name !== 'plane'
                )[0]
                if (intersect && this.isClickSoldier(intersect.object)) {
                    // 停止动画
                    this.walkAction.stop();
                    // 暂停动画
                    this.walkAction.paused = !this.walkAction.paused
                    console.log(intersect)

                    intersect.object.material.color.set(0xff0000)
                }
            })
            this.initStats()
            // 渲染
            this.render()
            
        },
        isClickSoldier(object) {
            if (object.name === 'Soldier') {
                return object
            } else if (object.parent) {
                return this.isClickSoldier(object.parent)
            } else {
                return null
            }
        },

        render() {
            // 更新动画
            this.animationMixer.update(this.clock.getDelta())
            //执行渲染方法 渲染场景和摄像机 渲染一帧图像
            this.renderer.render(this.scene, this.camera)

            this.orbitControls.update()
            //周期性执行渲染函数
            window.requestAnimationFrame(() => this.render())
            //通知stats画面已被重新渲染了
            this.stats.update()

             /* //clock.getDelta()方法获得两帧的时间间隔
            var T = this.clock.getDelta();//返回时间单位：秒
            //打印查看你的渲染时间间隔
            console.log('两帧渲染时间间隔',T*1000+'毫秒');
            console.log('查看每秒渲染频率',1/T); */
        },

        onWindowResize() {
            this.renderer.setSize(window.innerWidth, window.innerHeight)
            this.camera.aspect = window.innerWidth / window.innerHeight
            this.camera.updateProjectionMatrix()
        },
            //初始化统计对象
         initStats() {
            this.stats = new Stats();
            //设置统计模式
            this.stats.setMode(0); // 0: fps, 1: ms
            //统计信息显示在左上角
            this.stats.domElement.style.position = 'absolute';
            this.stats.domElement.style.left = '10px';
            this.stats.domElement.style.top = '10px';
            
            //将统计对象添加到对应的<div>元素中
            document.getElementById("Stats-output").appendChild(this.stats.domElement);
            /* setInterval(function () {
                stats.begin();
                stats.end();
	        	stats.update();
                }, 1000 / 60) */
            return this.stats;
        }
    },
    mounted() {
        this.init()
    }
}
</script>
<style >
#container {
    height: 400px;
}
</style>