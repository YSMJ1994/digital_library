<template>
    <div class="channel-body">
        <div class="book-item-container">
            <em class="book-item-index">{{idx + 1}}.</em>
            <div class="book-item-image-container">
                <img :src="data.cover_url" alt="" class="book-item-image">
            </div>
            <h3 class="book-name">{{data.name}}</h3>
            <div class="author-box">作者：{{data.author}}</div>
            <div>语言：{{data.language}}</div>
            <ScoreTool :value="data.average_score" noSet/>
        </div>
    </div>
</template>

<script>
    import ScoreTool from './ScoreTool'

    export default {
        props: {
            idx: Number,
            data: {
                type: Object,
                default: () => {
                    return {
                        id: '',
                        cover_url: '',
                        name: '',
                        average_score: 0
                    }
                }
            }
        },
        data() {
            return {}
        },
        methods: {
            clickBook() {
                this.$emit('click', this.data.id)
            }
        },
        components: {
            ScoreTool
        }
    }
</script>

<style lang="scss" scoped>
    .channel-body {

        .book-item-container {
            overflow: hidden;
            margin-bottom: -1px;
            padding: 16px 0;
            /*border-bottom: 1px dashed rgba(32, 160, 255, 0.65);*/
            display: block;
            cursor: pointer;

            .book-item-index {
                display: block;
                float: left;
                width: 32px;
                line-height: 32px;
                font-size: 24px;
                /*font-family: fzfont;*/
            }
            .book-item-image-container {
                width: 120px;
                height: 120px;
                box-shadow: 0 0 5px 2px rgba(45, 140, 240, 0.12);
                float: left;
                margin-right: 10px;

                .book-item-image {

                    width: 120px;
                    margin-right: 16px;
                }
            }
            .book-name {
                height: 45px;
                overflow: hidden;
                word-break: break-all;
                text-overflow: ellipsis;

            }
            .author-box {
                height: 40px;
                overflow: hidden;
                word-break: break-all;
                text-overflow: ellipsis;
            }
            p {
                margin: 10px 0;
            }
        }
    }
</style>
