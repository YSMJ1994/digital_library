<template>
    <div class="register-wrap">
        <div class="register-box">
            <div style="width: 200px;" class="edit-box">
                <div class="edit-row">
                    <i-input v-model="username" size="large" placeholder="昵称" class="iview-input">
                        <span slot="prepend">
                            <Icon type="edit" slot="prepend"></Icon>
                        </span>
                    </i-input>
                </div>
                <br/>
                <div class="edit-row">
                    <i-input v-model="account" size="large" placeholder="用户名" class="iview-input" @on-change="checkAccount">
                        <span slot="prepend">
                            <Icon type="edit" slot="prepend"></Icon>
                        </span>
                    </i-input>
                    <span v-if="hasCheck" class="tip-box">
                        <Icon type="checkmark" v-if="accountAvailable" color="green"></Icon>
                        <Icon type="close-round" v-else color="red"></Icon>
                    </span>
                </div>
                <br/>
                <div class="edit-row">
                    <i-input v-model="password" size="large" placeholder="密码" class="iview-input">
                        <span slot="prepend">
                            <Icon type="locked" slot="prepend"></Icon>
                        </span>
                    </i-input>
                </div>
                <br/>
                <div class="edit-row">
                    <i-input v-model="password2" size="large" placeholder="确认密码" class="iview-input">
                        <span slot="prepend">
                            <Icon type="locked" slot="prepend"></Icon>
                        </span>
                    </i-input>
                    <span v-if="password && password2" class="tip-box">
                        <Icon type="checkmark" v-if="isSame" color="green"></Icon>
                        <Icon type="close-round" v-else color="red"></Icon>
                    </span>
                </div>
                <br/>
                <div class="button">
                    <a class="gv" href="javascript: void(0)" @click="register">注册</a>
                </div>
                <div class="to-register">
                    已有账号？
                    <a href="javascript: void(0)" @click="back()">去登录</a>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import {register, checkAccountAvailable} from '../../api/user'

    export default {
        data() {
            return {
                username: '',
                account: '',
                hasCheck: false,
                accountAvailable: true,
                password: '',
                password2: '',
                pending: false
            }
        },
        computed: {
            isSame() {
                return this.password === this.password2
            }
        },
        mounted() {
        },
        methods: {
            checkAccount() {
                checkAccountAvailable({
                    account: this.account
                }).then(res => {
                    this.hasCheck = true
                    this.accountAvailable = res
                }).catch(msg => {
                    this.hasCheck = true
                    this.accountAvailable = false
                    this.$Message.warning({
                        content: msg
                    })
                })
            },
            register() {
                if(this.pending) return
                if(!this.account) {
                    this.$Message.warning({
                        content: '请输入用户名',
                        duration: 3
                    })
                    return
                }
                if(!this.hasCheck || !this.accountAvailable) {
                    this.$Message.warning({
                        content: '用户名不可用',
                        duration: 3
                    })
                    return
                }
                if(!this.password) {
                    this.$Message.warning({
                        content: '请输入密码',
                        duration: 3
                    })
                    return
                }
                if(!this.isSame) {
                    this.$Message.warning({
                        content: '两次密码不一致',
                        duration: 3
                    })
                    return
                }
                let closeMsg = this.$Message.loading({
                    content: '注册中...',
                    duration: 0
                })
                this.pending = true
                let para = {
                    account: this.account,
                    password: this.password,
                    username: this.username || this.account
                }
                register(para).then(res => {
                    closeMsg()
                    console.log('register res', res)
                    if (res) {
                        //注册成功
                        this.pending = false
                        this.$Message.success({
                            content: '注册成功，马上去登录吧^_^',
                            duration: 3
                        })
                        this.back({
                            account: para.account
                        })
                    }else {
                        this.$Message.warning({
                            content: '注册失败，请检查信息',
                            duration: 3
                        })
                    }
                }).catch(() => {
                    closeMsg()
                    this.pending = false
                    this.$Message.error({
                        content: '注册失败'
                    })
                })
            },
            back(params) {
                this.$emit('back')
                this.$router.push({
                    path: '/login',
                    query: params || {}
                })
            }
        }
    }
</script>

<style scoped lang="scss">
    .register-wrap {
        height: 100%;
        overflow: hidden;
        position: relative;

        .edit-box {
            width: 15rem;
            margin-left: 1.5rem;
        }
        .edit-row {
            width: 100%;
            display: flex;
            align-items: center;
        }
        .iview-input {
            width: 90%;
        }
        .tip-box {
            width: 10%;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .back {
            position: fixed;
            left: 20px;
            top: 20px;
            width: 30px;
            height: 30px;
            background: url('../../assets/images/back.png') no-repeat;
            background-size: cover;
            cursor: pointer;
        }
        .gv {
            text-decoration: none;
            background: url('../../assets/images/nav_gv.png') repeat 0px 0px;
            width: 130px;
            height: 43px;
            display: block;
            text-align: center;
            line-height: 43px;
            cursor: pointer;
            float: left;
            margin: 10px 2px 10px 2px;
            font: 18px/43px 'microsoft yahei';
            color: #066197;
            transition: all .3s;

            &:hover {
                background: url('../../assets/images/nav_gv.png') repeat 0px -43px;
                color: #1d7eb8;
                -webkit-box-shadow: 0 0 6px #1d7eb8;
            }
        }

        .register-box {
            width: 600px;
            margin: 0 auto;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 100001;
        }
        .input-wrap {
            width: 270px;
        }
        .input-wrap label {
            display: inline-block;
            width: 25px;
            height: 25px;
            vertical-align: middle;
            margin-right: 10px;
            background-size: cover;
        }
        .input-wrap .un-same-password {
            border-color: red;
        }
        .icon-id {
            background: url('../../assets/images/id.png') no-repeat;
        }
        .icon-user {
            background: url('../../assets/images/user.png') no-repeat;
        }
        .icon-id {
            background: url('../../assets/images/id.png') no-repeat;
        }
        .icon-password {
            background: url('../../assets/images/password.png') no-repeat;
        }

        .input-wrap input {
            border: none;
            outline: none;
            background: none;
            border-bottom: 1px solid #000;
            margin-top: 30px;
            width: 200px;
            height: 30px;
            line-height: 30px;
            /* text-align: center; */
            font-size: 14px;
            padding: 0 5px;
        }
        .button {
            width: 100%;
            height: 80px;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .to-register {
            font-size: 12px;
            float: right;
            padding-top: 20px;
        }
        .to-register a {
            color: #066197;
            text-decoration: none;
        }
    }
</style>
