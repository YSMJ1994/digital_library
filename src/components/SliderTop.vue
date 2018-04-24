<template>
    <section class="slider-wrap">
        <ul class="slider-ul">
            <router-link :to="item.path" v-for="item in list" :key="item.path">
                <li :class="['slider-li', isCurrent(item) ? 'active-li' : '']" v-if="role <= item.role">
                    {{item.label}}
                </li>
            </router-link>
        </ul>
    </section>
</template>

<script>
    const list = [
        {
            label: '首页',
            path: '/Hello',
            role: 3,
            index: 0
        },
        {
            label: '图书',
            path: '/Book',
            role: 3,
            index: 1
        },
        {
            label: '下载',
            path: '/Download',
            role: 2,
            index: 2
        },
        {
            label: '用户管理',
            path: '/User',
            role: 1,
            index: 3
        }
    ]
    export default {
        data() {
            return {
                list
            }
        },
        computed: {
            role() {
                return this.$store.getters.role ? parseInt(this.$store.getters.role) : 1000
            }
        },
        methods: {
            isCurrent(item) {
                return this.$route.path === item.path || this.$route.meta.parentIndex === item.index
            }
        }
    }
</script>

<style lang="scss" scoped>
    .slider-wrap {
        height: 100%;
        display: flex;
        align-items: flex-end;

        .slider-ul {
            list-style: none;
            display: flex;
            height: 3rem;
        }
        .slider-li {
            list-style: none;
            width: 10rem;
            min-width: 5rem;
            line-height: 3rem;
            text-align: center;
            font-size: 1rem;
            cursor: pointer;
            color: #000;

            &:hover {
                background-color: rgba(255, 255, 255, 0.2);
            }
        }
        .active-li {
            background-color: rgba(255, 255, 255, 0.2) !important;
        }
    }
</style>
