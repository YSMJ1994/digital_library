<template>
    <div class="index-wrap">
        <!--<canvas id="canvas" class="canvas"></canvas>-->
        <div class="logo">
            <span>数字图书馆</span>
        </div>
        <div class="main">
            <transition :name="$route.name === 'login' ? 'fade-back' : 'fade-forward'">
                <router-view/>
            </transition>
        </div>
        <div class="city"></div>
        <div class="moon"></div>
    </div>
</template>

<script>
    import Stars from '../../../static/js/Star'
    import Moon from '../../../static/js/Moon'
    import Meteor from '../../../static/js/Meteor'

    export default {
        data() {
            return {}
        },
        mounted() {
            /*let canvas = document.getElementById('canvas'),
                ctx = canvas.getContext('2d'),
                width = window.innerWidth,
                height = window.innerHeight,
                //实例化月亮和星星。流星是随机时间生成，所以只初始化数组
                moon = new Moon(ctx, width, height),
                stars = new Stars(ctx, width, height, 200),
                meteors = [],
                count = 0

            canvas.width = width;
            canvas.height = height;

            const meteorGenerator = () => {
                //x位置偏移，以免经过月亮
                let x = Math.random() * width + 800
                meteors.push(new Meteor(ctx, x, height))

                //每隔随机时间，生成新流星
                setTimeout(() => {
                    meteorGenerator()
                }, Math.random() * 2000)
            }

            const frame = () => {
                count++
                count % 10 == 0 && stars.blink()
                moon.draw()
                stars.draw()

                meteors.forEach((meteor, index, arr) => {
                    //如果流星离开视野之内，销毁流星实例，回收内存
                    if (meteor.flow()) {
                        meteor.draw()
                    } else {
                        arr.splice(index, 1)
                    }
                })
                requestAnimationFrame(frame)
            }
            meteorGenerator()
            frame()*/
        },
        methods: {}
    }
</script>

<style scoped lang="scss">
    .index-wrap {
        height: 100%;
        overflow: hidden;
        position: relative;

        .logo {
            width: 270px;
            height: 108px;
            margin: 20px auto;
            margin-top: 5.5rem;
            display: flex;
            justify-content: center;
            align-items: center;

            img {
                flex: none;
            }

            span {
                color: #000;
                font-size: 2.25rem;
                font-weight: bold;
                margin-bottom: 1rem;
            }

        }
        .main {
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
        }

        .canvas {
            position: fixed;
            z-index: -1;
        }

        .city {
            width: 100%;
            height: 170px;
            position: fixed;
            bottom: 0px;
            z-index: 100;
            background: url('../../assets/images/city.png') no-repeat;
            background-size: cover;
        }

        .moon {
            width: 100px;
            height: 100px;
            position: absolute;
            left: 100px;
            top: 100px;
            background: url('../../assets/images/moon.png') no-repeat;
            background-size: cover;
        }

        .fade-forward-enter-active, .fade-forward-leave-active {
            transition: all .5s
        }
        .fade-back-enter-active, .fade-back-leave-active {
            transition: all .5s
        }

        .fade-forward-enter {
            opacity: 0;
            transform: translateX(400px);
        }
        .fade-forward-leave-active {
            position: absolute !important;
            opacity: 0;
            transform: translateX(-400px);
        }
        .fade-back-enter {
            opacity: 0;
            transform: translateX(-400px);
        }
        .fade-back-leave-active {
            position: absolute !important;
            opacity: 0;
            transform: translateX(400px);
        }
    }
</style>
