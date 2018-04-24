<template>
    <div class="bookCell" @click="clickBook">
        <div class="bookImgBox">
            <img :src="data.coverUrl" alt="" class="bookCover">
        </div>
        <div class="bookMsg">
            {{data.name}}
        </div>
        <ScoreTool class="book-score" :value="data.averageScore" noSet/>
    </div>
</template>

<script>
    import ScoreTool from './ScoreTool'
    export default {
        props: {
            data: {
                type: Object,
                default: () => {
                    return {
                        id: '',
                        coverUrl: '',
                        name: '',
                        averageScore: 0
                    }
                }
            }
        },
        data() {
            return {

            }
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
    .bookCell {
        width: 12rem;
        text-align: center;

        .bookImgBox {
            width: 6.3rem;
            height: 7rem;
            box-shadow: 0 0 5px 2px rgba(45, 140, 240, 0.12);
            border: 0;
            margin: 0 auto;
        }
        .bookCover {
            width: 100%;
            height: 100%;
            text-align: center;
        }
        .bookMsg {
            margin-top: 1rem;
            height: 2rem;
            overflow: hidden;
            text-overflow: ellipsis;
            word-break: break-all;
            white-space: inherit;
        }
        .book-score {
            margin-top: 0.5rem;
            height: 1rem;
        }
    }
</style>
