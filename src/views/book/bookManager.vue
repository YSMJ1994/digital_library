<template>
    <section class="book-manager-wrap">
        <div class="header">
            <div class="managerButton" @click="goBack">返回</div>
            <div class="managerButton" @click="addBook">上传图书</div>
            <Searchbar placeholder="按书名搜索" v-model="filter.name" @search="searchByName" class="searchTool"/>
            <Searchbar placeholder="按作者搜索" v-model="filter.author" @search="searchByAuthor" class="searchTool"/>
        </div>
        <div class="main">
            <!--<div class="book-list">-->
                <!--<BookCell2 v-for="(book, i) in data" :idx="i" :data="book" :key="book.id"/>-->
            <!--</div>-->
            <Table :width="tableWidth" :loading="loading" :row-class-name="rowClassName" :columns="columns" :data="data"></Table>
        </div>
        <Modal
            v-model="modal"
            :title="isEdit ? '修改图书' : '上传图书'">
            <Form ref="form" :model="form" :rules="formRules" :label-width="60" style="width: 95%;">
                <FormItem label="名称" prop="name">
                    <Input v-model="form.name" placeholder="输入名称"/>
                </FormItem>
                <FormItem label="选择类型">
                    <Select v-model="form.type" placeholder="选择类型">
                        <Option v-for="type in typeList" :value="`${type.id}`" :key="type.id">{{ type.name }}</Option>
                    </Select>
                </FormItem>
                <FormItem label="作者">
                    <Input v-model="form.author" placeholder="输入作者"/>
                </FormItem>
                <FormItem label="译者">
                    <Input v-model="form.translator" placeholder="输入译者"/>
                </FormItem>
                <FormItem label="版本">
                    <Input v-model="form.version" placeholder="输入版本"/>
                </FormItem>
                <FormItem label="语言">
                    <Input v-model="form.language" placeholder="输入语言"/>
                </FormItem>
                <FormItem label="上传封面" v-if="!isEdit">
                    <Upload v-if="modal" @success="res => form.coverUrl = res"/>
                </FormItem>
                <FormItem label="上传图书" v-if="!isEdit">
                    <Upload v-if="modal" @fileChange="size => form.byteSize = size" @success="res => form.fileUrl = res"/>
                </FormItem>
            </Form>
            <span slot="footer">
                <Button @click="modal = false">取消</Button>
                <Button type="primary" @click="submit">确定</Button>
            </span>
        </Modal>
        <!--<div class="footer"></div>-->
    </section>
</template>

<script>
    import Searchbar from '../../components/Searchbar'
    import BookCell2 from '../../components/BookCell2'
    import ScoreTool from '../../components/ScoreTool'
    import Upload from '../../components/Upload'
    import {getBookByType, deleteBook, getAllTypes, uploadBook, updateBook} from '../../api/book'

    export default {
        data() {
            return {
                columns: [
                    {
                        title: '书名',
                        key: 'name',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '类型',
                        key: 'typeName',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '作者',
                        key: 'author',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '译者',
                        key: 'translator',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '语言',
                        key: 'language',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '版本',
                        key: 'version',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '大小',
                        key: 'version',
                        // width: 200,
                        align: 'center'
                    },
                    {
                        title: '评分',
                        key: 'version',
                        // width: 200,
                        align: 'center',
                        render: (h, params) => {
                            return (
                                <ScoreTool value={params.row.average_score} noSet/>
                            )
                        }
                    },
                    {
                        title: '操作',
                        key: 'action',
                        // width: 300,
                        align: 'center',
                        render: (h, params) => {
                            return (
                                <div class="button-box">
                                    <div class="button-item" onClick={() => this.editBook(params.row)}>
                                        编辑
                                    </div>
                                    <div class="button-item" onClick={() => this.deleteBook(params.row.id)}>
                                        删除
                                    </div>
                                </div>
                            )
                        }
                    }
                ],
                data: [],
                filter: {
                    type: `${this.$route.query.type}` || '',
                    name: '',
                    author: ''
                },
                tableWidth: window.innerWidth * 0.92,
                loading: false,
                isEdit: false,
                modal: false,
                form: {
                    name: '',
                    author: '',
                    translator: '',
                    version: '',
                    language: '',
                    coverUrl: '',
                    type: '',
                    fileUrl: '',
                    byteSize: ''
                },
                formRules: {
                    name: [
                        { required: true, message: '类别名称不能为空', trigger: 'blur' }
                    ]
                },
                typeList: []
            }
        },
        computed: {
            role() {
                return this.$store.getters.role
            }
        },
        methods: {
            submit() {
                if(this.isEdit) {
                    updateBook(this.form).then(res => {
                        this.$Message.success({
                            content: '更新成功',
                            duration: 3
                        })
                        this.modal = false
                        this.getData()
                    }).catch(() => {
                        this.$Message.warning({
                            content: '更新失败',
                            duration: 3
                        })
                        // this.modal = false
                    })
                }else {
                    if(!this.form.fileUrl) {
                        this.$Message.warning({
                            content: '请上传书籍',
                            duration: 3
                        })
                        return
                    }
                    if(!this.form.coverUrl) {
                        this.$Message.warning({
                            content: '请上传封面',
                            duration: 3
                        })
                        return
                    }
                    uploadBook(this.form).then(res => {
                        this.$Message.success({
                            content: '添加成功',
                            duration: 3
                        })
                        this.modal = false
                        this.getData()
                    }).catch(() => {
                        this.$Message.warning({
                            content: '添加失败',
                            duration: 3
                        })
                    })
                }
            },
            editBook(book) {
                console.log(book)
                this.isEdit = true
                this.form = {
                    ...this.form,
                    ...{
                        id: book.id,
                        name: book.name,
                        author: book.author,
                        translator: book.translator,
                        version: book.version,
                        language: book.language,
                        coverUrl: book.coverUrl,
                        type: parseInt(book.type)
                    }
                }
                this.modal = true
            },
            addBook() {
                this.isEdit = false
                this.form = {
                    name: '',
                    author: '',
                    translator: '',
                    version: '',
                    language: '',
                    coverUrl: '',
                    type: this.filter.type
                }
                this.modal = true
            },
            deleteBook(id) {
                this.$Modal.confirm({
                    title: '删除',
                    content: '确认删除该书籍？',
                    onOk: () => {
                        deleteBook({id}).then(() => {
                            this.$Message.success({
                                content: '删除成功',
                                duration: 3
                            })
                            this.getData()
                        }).catch(() => {
                            this.$Message.warning({
                                content: '删除失败',
                                duration: 3
                            })
                        })
                    },
                    onCancel: () => {
                    },
                    okText: '删除',
                    cancelText: '取消'
                });
            },
            rowClassName (row, index) {
                if(index % 2 === 0) {
                    return 'table-row-first'
                }else {
                    return 'table-row-second'
                }
            },
            searchByName(name) {
                this.filter.name = name
                this.getData()
            },
            searchByAuthor(author) {
                this.filter.author = author
                this.getData()
            },
            goBack() {
                this.$router.go(-1)
            },
            doPost(cb) {
                let para = this.filter
                getBookByType(para).then(res => {
                    console.log(res)
                    cb && cb(res)
                }).catch(() => {
                    cb && cb([])
                })
            },
            getData() {
                if(this.loading) return
                this.loading = true
                this.doPost(data => {
                    this.data = data
                    this.loading = false
                })
            },
            getTypeList() {
                getAllTypes().then(res => {
                    console.log(res)
                    this.typeList = res
                }).catch(() => {

                })
            }
        },
        components: {
            Searchbar,
            BookCell2,
            ScoreTool,
            Upload
        },
        mounted() {
            this.getTypeList()
            this.getData()
        }
    }
</script>

<style lang="scss">
    .book-manager-wrap {
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
    }
</style>

<style lang="scss" scoped>
    .book-manager-wrap {
        padding: 2rem;

        .header {
            height: 4rem;
            display: flex;
            align-items: center;

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
            margin-top: 1rem;
            height: calc(100% - 5rem);
            /*background-color: rgba(0, 0, 0, .1);*/
        }
    }
</style>
