<template>
    <section class="book-list-wrap">
        <div class="header">
            <div class="managerButton" @click="goBack">返回</div>
            <Select v-model="filter.type" class="typeSelect" @on-change="getList">
                <Option v-for="item in typeList" :value="`${item.id}`" :key="item.id">{{ item.name }}</Option>
            </Select>
            <Searchbar placeholder="按书名搜索" v-model="filter.name" @search="getList" class="searchTool"/>
            <Searchbar placeholder="按作者搜索" v-model="filter.author" @search="getList" class="searchTool"/>
        </div>
        <div class="main">
            <Book class="book-cell" v-for="(book, i) in list" :key="book.id" :data="book" :idx="i" @click.native="gotoBookDetail(book.id, book.type)"/>
            <Spin size="large" fix v-if="loading"></Spin>
        </div>
    </section>
</template>

<script>
    import { getBookByType, getAllTypes } from '../../api/book'
    import Book from '../../components/BookCell2'
    import Searchbar from '../../components/Searchbar'

    export default {
        data() {
            return {
                list: [],
                typeList: [],
                filter: {
                    type: `${this.$route.query.type}` || '',
                    name: '',
                    author: ''
                },
                loading: false,
                hasInited: false
            }
        },
        methods: {
            goBack() {
                this.$router.go(-1)
            },
            gotoBookDetail(id, type) {
                this.$router.push({
                    path: '/BookDetail',
                    query: {
                        id: id,
                        type: type
                    }
                })
            },
            getList() {
                if(this.loading || !this.hasInited) return
                this.loading = true
                this.doPost()
            },
            getTypeList() {
                getAllTypes().then(res => {
                    this.typeList = res
                }).catch(() => {
                })
            },
            initList() {
                this.loading = true
                this.doPost(() => {
                    this.hasInited = true
                })
            },
            doPost(cb) {
                getBookByType(this.filter).then(res => {
                    this.loading = false
                    this.list = res
                    cb && cb()
                    console.log('books', res)
                }).catch(() => {
                    this.loading = false
                    cb && cb()
                    console.log('err')
                })
            }
        },
        mounted() {
            this.getTypeList()
            this.initList()
        },
        components: {
            Book,
            Searchbar
        }
    }
</script>

<style lang="scss">
    .book-list-wrap {
        @import "../../styles/variable";
        width: 100%;
        height: 100%;

        .typeSelect {

            .ivu-select-selection {
                background-color: transparent;
                border-color: #20a0ff;
            }
            .ivu-select-dropdown {
                /*background-color: #141d27;*/
                /*box-shadow: 0 1px 6px rgba(255,255,255,.2);*/

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
        }
        .ivu-spin-fix {
            background-color: rgba(255, 255, 255, .5);
        }
    }
</style>

<style lang="scss" scoped>
    .book-list-wrap {
        padding: 2rem;
        text-align: center;

        .header {
            height: 4rem;
            display: flex;
            align-items: center;

            .typeSelect {
                width: 12.5rem;
                margin-right: 2rem;
            }
            .searchTool {
                display: inline-block;
                margin-right: 2rem;
                flex: none;
            }
            .managerButton {
                flex: none;
                user-select: none;
                display: inline-block;
                color: #20a0ff;
                margin-right: 1.5rem;
                border: 1px solid #20a0ff;
                border-radius: 3px;
                padding: 0.5rem 1rem;
                cursor: pointer;
                transition: .2s;

                &:hover {
                    background-color: #20a0ff;
                    color: #fff;
                    border-color: #20a0ff;
                }
            }
        }
        .main {
            display: flex;
            flex-flow: row wrap;
        }
        .book-cell {
            width: 21rem;
            margin: 1rem 2rem;
            align-content: flex-start;
        }
    }
</style>
