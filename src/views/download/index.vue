<template>
    <section class="download-wrap">
        <div class="header">
            我的下载
        </div>
        <div class="main">
            <Table :width="tableWidth" :loading="loading" :row-class-name="rowClassName" :columns="columns" :data="data"></Table>
        </div>
        <Spin size="large" fix v-if="loading"></Spin>
    </section>
</template>

<script>
    import { getMyDownload, setScore } from "../../api/download";
    import ScoreTool from '../../components/ScoreTool'

    export default {
        data() {
            return {
                columns: [
                    {
                        title: '书名',
                        key: 'bookName',
                        // width: 200,
                        align: 'center',
                        render: (h, params) => {
                            return (
                                <span class="bookName" onClick={() => this.gotoDetail(params.row)}>
                                    {params.row.bookName}
                                </span>
                            )
                        }
                    },
                    {
                        title: '用户名',
                        key: 'username',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '我的评分',
                        key: 'score',
                        // width: 200,
                        align: 'center',
                        render: (h, params) => {
                            if(!params.row.score) {
                                return (
                                    <span>请评分</span>
                                )
                            }
                            return (
                                <ScoreTool value={params.row.score} noSet/>
                            )
                        }
                    },
                    {
                        title: '评分',
                        key: 'action',
                        // width: 300,
                        align: 'center',
                        render: (h, params) => {
                            console.log('33333', params.row.score)
                            if(params.row.score) {
                                return (
                                    <span>感谢评分</span>
                                )
                            }
                            return (
                                <ScoreTool value={params.row.score} onChange={score => this.setScore(params.row.id, score, params.row.book_id)}/>
                            )
                        }
                    }
                ],
                data: [],
                tableWidth: window.innerWidth * 0.92,
                loading: false
            }
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
            setScore(id, score, bookId) {
                setScore({id, score, bookId}).then(res => {
                    if(res === 'success') {
                        this.getList()
                        this.$Message.success({
                            content: '感谢您的评分',
                            duration: 3
                        })
                    }
                    if(res === 'failed') {
                        this.$Message.warning({
                            content: '评分失败',
                            duration: 3
                        })
                    }
                }).catch(() => {
                    this.$Message.warning({
                        content: '评分失败',
                        duration: 3
                    })
                })
            },
            getList() {
                let para = {
                    userId: this.$store.getters.id
                }
                this.loading = true
                getMyDownload(para).then(res => {
                    this.data = res
                    console.log('res', res)
                    this.loading = false
                }).catch(() => {
                    this.loading = false
                })
            },
            rowClassName (row, index) {
                if(index % 2 === 0) {
                    return 'table-row-first'
                }else {
                    return 'table-row-second'
                }
            },
        },
        mounted() {
            this.getList()
        }
    }
</script>

<style lang="scss">
    .download-wrap {
        @import "../../styles/variable";

        .ivu-table-wrapper {
            border: 0;
            width: 100%;
            height: 100%;
        }
        .ivu-spin-fix {
            background-color: rgba(255, 255, 255, .5);
            border: 0;
        }
        .ivu-table{
            background-color: transparent;
            border: 0;

            th, td {
                background-color: transparent;
                border: 0;
            }

            thead {
                background-color: $bg;
            }
            &:before {
                background-color: transparent;
            }
            &:after {
                background-color: transparent;
            }
        }
        .table-row-first {
            background-color: transparent;
            transition: background-color .3s;

            &:hover {
                background-color: rgba(45, 140, 240, 0.11);
            }
        }
        .table-row-second {
            background-color: $bg;
            transition: background-color .2s;

            &:hover {
                background-color: rgba(45, 140, 240, 0.11);
            }
        }
        .button-box {
            font-size: 0;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .button-item {
            font-size: 0.75rem;
            padding: 0.1rem 0.5rem;
            color: #20a0ff;
            margin: 0.75rem;
            border: 1px solid #20a0ff;
            border-radius: 2px;
            cursor: pointer;
            transition: all .1s;

            &:hover {
                background-color: #20a0ff;
                border-color: #20a0ff;
                color: #fff;
            }
            &:first-child {
                margin-left: 0;
            }
            &:last-child {
                margin-right: 0;
            }
        }
        .bookName {
            cursor: pointer;

            &:hover {
                color: #2d8cf0;
                text-decoration: underline;
            }
        }
    }
</style>

<style lang="scss" scoped>
    .download-wrap {
        width: 100%;
        height: 100%;
        padding: 2rem;
        position: relative;

        .header {
            font-size: 1.2rem;
            text-align: center;
        }
        .main {
            margin-top: 1rem;
            /*background-color: rgba(0, 0, 0, .1);*/
        }
    }
</style>
