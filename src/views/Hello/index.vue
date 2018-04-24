<template>
    <section class="index-warp">
        <div class="channel-container">

            <div class="channel-item-container">
                <div class="channel-header">
                    <h2>下载排行</h2>
                </div>
                <div class="channel-body">
                    <div class="book-item-container" :key="down.id" v-for="(down,index) in downloadData" @click="gotoDetail(down)">
                        <em class="book-item-index">{{index + 1}}.</em>
                        <div class="book-item-image-container">
                            <img :src="down.cover_url" alt="" class="book-item-image">
                        </div>
                        <h3>{{down.name}}</h3>
                        <p>{{down.translator}}</p>
                        <ScoreTool :value="down.average_score" noSet/>
                    </div>
                </div>
            </div>

            <div class="channel-item-container">
                <div class="channel-header">
                    <h2>评分排行</h2>
                </div>
                <div class="channel-body">
                    <div class="book-item-container" :key="source.id" v-for="(source,index) in sourceData" @click="gotoDetail(source)">
                        <em class="book-item-index">{{index + 1}}.</em>
                        <div class="book-item-image-container">
                            <img :src="source.cover_url" alt="" class="book-item-image">
                        </div>
                        <h3>{{source.name}}</h3>
                        <p>{{source.translator}}</p>
                        <ScoreTool :value="source.average_score" noSet/>
                    </div>
                </div>
            </div>


            <div class="channel-item-container">
                <div class="channel-header">
                    <h2>推荐</h2>
                </div>
                <div class="channel-body">
                    <div class="book-item-container" :key="recommend.id" v-for="(recommend,index) in recommendData" @click="gotoDetail(recommend)">
                        <em class="book-item-index">{{index + 1}}.</em>
                        <div class="book-item-image-container">
                            <img :src="recommend.cover_url" alt="" class="book-item-image">
                        </div>
                        <h3>{{recommend.name}}</h3>
                        <p>{{recommend.translator}}</p>
                        <ScoreTool :value="recommend.average_score" noSet/>
                    </div>
                </div>
            </div>

        </div>
    </section>
</template>

<script>
    import {getDownloadTopFive, getScoreTopFive, getRecommendTop} from "../../api/book"
    import ScoreTool from '../../components/ScoreTool'

    export default {
        data() {
            return {
                downloadData: [],
                sourceData: [],
                recommendData:[]
            }
        },
        components: {
            ScoreTool
        },
        methods: {
            gotoDetail(book) {
                this.$router.push({
                    path: '/BookDetail',
                    query: {
                        id: book.id,
                        type: book.type
                    }
                })
            },
            getDownloadTopFive() {
                getDownloadTopFive().then(res => {

                    // console.log("getDownloadTopFive", res)

                    this.downloadData = res

                }).catch(err => {
                    console.log("err", err)
                })
            },
            getScoreTopFive() {
                getScoreTopFive().then(res => {

                    // console.log("getScoreTopFive", res)

                    this.sourceData = res

                }).catch(err => {
                    console.log("err", err)
                })
            },
            getRecommendTop() {
                getRecommendTop().then(res => {

                    // console.log("getRecommendTop", res)

                    this.recommendData = res

                }).catch(err => {
                    console.log("err", err)
                })
            }
        },
        mounted() {
            this.getDownloadTopFive()
            this.getScoreTopFive()
            this.getRecommendTop()
        }
    }
</script>

<style lang="scss" scoped>
    .index-warp {
        padding: 1rem 2rem;
        width: 100%;

        .channel-container {

            display: flex;
            justify-content: space-around;
            align-items: flex-start;

            .channel-item-container {
                width: 20rem;

                .channel-header {
                    position: relative;
                    margin-bottom: 16px;
                    height: 48px;
                    line-height: 48px;

                    h2 {
                        font-size: 18px;
                    }
                }

                .channel-header::after {
                    content: '';
                    position: absolute;
                    border: 1px solid #eb634b;
                    width: 36px;
                    left: 0;
                    bottom: 0;
                }

                .channel-body {

                    .book-item-container {
                        overflow: hidden;
                        margin-bottom: -1px;
                        padding: 16px 0;
                        /*border-bottom: 1px dashed #e2e2e2;*/

                        display: block;

                        cursor: pointer;

                        .book-item-index {
                            display: block;
                            float: left;
                            width: 32px;
                            line-height: 32px;
                            font-size: 24px;
                            font-family: fzfont;
                        }

                        .book-item-image-container {
                            width: 100px;
                            height: 120px;
                            box-shadow: 0 0 5px 2px rgba(45, 140, 240, 0.12);
                            float: left;
                            margin-right: 10px;

                            .book-item-image {
                                width: 100%;
                                height: 100%;
                                margin-right: 16px;
                            }

                        }

                        p {
                            margin: 10px 0;
                        }

                    }

                }

            }

        }
    }
</style>
