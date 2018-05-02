<template>
    <div class="home-wrap">
        <canvas id="canvas" class="canvas"></canvas>
        <div class="header">
            <div class="header-title">
                <span class="title">数字图书馆</span>
                <span class="remark">v1.0</span>
            </div>
            <SliderTop/>
            <div class="login-tool">
                <span class="tool-text" style="margin-right: 10px;">Hello</span>
                <span class="tool-text">{{$store.getters.name}}</span>
                <div @click="exit" class="tool-button">
                    <Icon type="power" class="tool-icon"></Icon>
                    <span class="tool-text">退出登录</span>
                </div>
            </div>
        </div>
        <div class="content">
            <transition :name="fadeName">
                <router-view/>
            </transition>
        </div>
    </div>
</template>

<script>
    import SiderBar from '../components/slider'
    import SliderTop from '../components/SliderTop'

    export default {
        data() {
            return {
                fadeName: 'fade-forward'
            }
        },
        components: {
            SiderBar,
            SliderTop
        },
        methods: {
            exit() {
                this.$store.dispatch('logout').then(() => {
                    this.$router.push('/login')
                }).catch(err => {
                    console.log(err)
                })
            },
            setCanvas() {
                var canvas = document.querySelector('canvas'),
                    ctx = canvas.getContext('2d')
                canvas.width = window.innerWidth;
                canvas.height = window.innerHeight;
                ctx.lineWidth = .3;
                ctx.strokeStyle = (new Color(150)).style;

                // var mousePosition = {
                // 	x: 30 * canvas.width / 100,
                // 	y: 30 * canvas.height / 100
                // };
                var mousePosition = {
                    x:  canvas.width - 100,
                    y:  canvas.height - 60
                };

                var dots = {
                    nb: 250,
                    distance: 100,
                    d_radius: 150,
                    array: []
                };

                function colorValue(min) {
                    return Math.floor(Math.random() * 255 + min);
                }

                function createColorStyle(r,g,b) {
                    return 'rgba(' + r + ',' + g + ',' + b + ', 0.8)';
                }

                function mixComponents(comp1, weight1, comp2, weight2) {
                    return (comp1 * weight1 + comp2 * weight2) / (weight1 + weight2);
                }

                function averageColorStyles(dot1, dot2) {
                    var color1 = dot1.color,
                        color2 = dot2.color;

                    var r = mixComponents(color1.r, dot1.radius, color2.r, dot2.radius),
                        g = mixComponents(color1.g, dot1.radius, color2.g, dot2.radius),
                        b = mixComponents(color1.b, dot1.radius, color2.b, dot2.radius);
                    return createColorStyle(Math.floor(r), Math.floor(g), Math.floor(b));
                }

                function Color(min) {
                    min = min || 0;
                    this.r = colorValue(min);
                    this.g = colorValue(min);
                    this.b = colorValue(min);
                    this.style = createColorStyle(this.r, this.g, this.b);
                }

                function Dot(){
                    this.x = Math.random() * canvas.width;
                    this.y = Math.random() * canvas.height;

                    this.vx = -.5 + Math.random();
                    this.vy = -.5 + Math.random();

                    this.radius = Math.random() * 2;

                    this.color = new Color();
                }

                Dot.prototype = {
                    draw: function(){
                        ctx.beginPath();
                        ctx.fillStyle = this.color.style;
                        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, false);
                        ctx.fill();
                    }
                };

                function createDots(){
                    for(var i = 0; i < dots.nb; i++){
                        dots.array.push(new Dot());
                    }
                }

                function moveDots() {
                    for(var i = 0; i < dots.nb; i++){

                        var dot = dots.array[i];

                        if(dot.y < 0 || dot.y > canvas.height){
                            dot.vx = dot.vx;
                            dot.vy = - dot.vy;
                        }
                        else if(dot.x < 0 || dot.x > canvas.width){
                            dot.vx = - dot.vx;
                            dot.vy = dot.vy;
                        }
                        dot.x += dot.vx;
                        dot.y += dot.vy;
                    }
                }

                function connectDots() {
                    for(var i = 0; i < dots.nb; i++){
                        for(var j = 0; j < dots.nb; j++){
                            var i_dot = dots.array[i];
                            var j_dot = dots.array[j];

                            if((i_dot.x - j_dot.x) < dots.distance && (i_dot.y - j_dot.y) < dots.distance && (i_dot.x - j_dot.x) > - dots.distance && (i_dot.y - j_dot.y) > - dots.distance){
                                if((i_dot.x - mousePosition.x) < dots.d_radius && (i_dot.y - mousePosition.y) < dots.d_radius && (i_dot.x - mousePosition.x) > - dots.d_radius && (i_dot.y - mousePosition.y) > - dots.d_radius){
                                    ctx.beginPath();
                                    ctx.strokeStyle = averageColorStyles(i_dot, j_dot);
                                    ctx.moveTo(i_dot.x, i_dot.y);
                                    ctx.lineTo(j_dot.x, j_dot.y);
                                    ctx.stroke();
                                    ctx.closePath();
                                }
                            }
                        }
                    }
                }

                function drawDots() {
                    for(var i = 0; i < dots.nb; i++){
                        var dot = dots.array[i];
                        dot.draw();
                    }
                }

                function animateDots() {
                    ctx.clearRect(0, 0, canvas.width, canvas.height);
                    moveDots();
                    connectDots();
                    drawDots();

                    requestAnimationFrame(animateDots);
                }

                //----------------------跟着鼠标动--------------------
                this.$el.addEventListener('mousemove', function(e){
                    mousePosition.x = e.pageX;
                    mousePosition.y = e.pageY;
                });

                this.$el.addEventListener('mouseleave', function(e){
                    mousePosition.x = canvas.width / 2;
                    mousePosition.y = canvas.height / 2;
                });
                //----------------------跟着鼠标动--------------------

                createDots();
                requestAnimationFrame(animateDots);
            }
        },
        watch: {
            '$route'(to, from) {
                let isBack = this.$router.isBack
                if(isBack) {
                    this.fadeName = 'fade-back'
                }else {
                    this.fadeName = 'fade-forward'
                }
            }
        },
        mounted() {
            this.setCanvas()
            console.log(this.$store.getters)
        }
    }
</script>

<style lang="scss" scoped>
    .home-wrap {
        $sideWidth: 12.5rem;
        $headerHeight: 5rem;
        width: 100%;
        height: 100%;
        padding: 0;
        margin: 0;
        display: flex;
        overflow: hidden;
        align-items: center;
        flex-direction: column;

        #canvas {
            position: absolute;
            z-index: 1;
            opacity: 0.5;
        }
        .header {
            width: 100%;
            height: $headerHeight;
            display: flex;
            justify-content: space-between;
            padding: 0 1.5rem;
            z-index: 2;
        }
        .header-title {
            line-height: $headerHeight;

            .title {
                font-size: 2rem;
            }
            .remark {
                font-size: 1rem;
                margin-left: 1.5rem;
            }
        }
        .content {
            width: 97%;
            height: calc(98.5% - #{$headerHeight});
            background-color: rgba(255, 255, 255, 0.2);
            display: flex;
            /*padding: 0 2rem;*/
            justify-content: center;
            z-index: 2;
            overflow: auto;

            &::-webkit-scrollbar {
                width: 5px;
                height: 5px;
            }
            &::-webkit-scrollbar-button {
                display: none;
            }
            &::-webkit-scrollbar-track {
                background-color: transparent;
            }
            &::-webkit-scrollbar-thumb {
                border-radius: 5px;
                background-color: rgba(45, 140, 240, 0.25);
            }
        }
        .slider {
            width: $sideWidth;
            height: 100%;
        }
        .main {
            width: calc(100% - #{$sideWidth});
            height: 100%;
            overflow: auto;
            padding: 1rem;
        }
        .login-tool {
            line-height: $headerHeight;
            display: flex;
            font-size: 1rem;

            .tool-icon {
                transition: color .1s;
                margin-right: 0.3rem;
            }
            .tool-text {
                transition: color .3s;
            }
            .tool-button {
                padding: 0 1rem;
                cursor: pointer;

                &:hover  {
                    .tool-text, .tool-icon {
                        color: aqua;
                    }
                }
            }
        }
        .exit {
            text-align: center;
        }
        .fade-forward-enter-active, .fade-forward-leave-active {
            transition: all .5s
        }
        .fade-back-enter-active, .fade-back-leave-active {
            transition: all .5s
        }

        .fade-forward-enter {
            opacity: 0;
            transform: translateX(100%);
        }
        .fade-forward-leave-active {
            position: absolute !important;
            opacity: 0;
            transform: translateX(-100%);
        }
        .fade-back-enter {
            opacity: 0;
            transform: translateX(-100%);
        }
        .fade-back-leave-active {
            position: absolute !important;
            opacity: 0;
            transform: translateX(100%);
        }
    }
</style>
