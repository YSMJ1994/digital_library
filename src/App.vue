<template>
    <div id="app">
        <router-view/>
    </div>
</template>

<script>
    export default {
        name: 'App'
    }
</script>

<style lang="scss">
    html {
        width: 100%;
        height: 100%;
        padding: 0;
        margin: 0;
        overflow: hidden;
    }
    body {
        width: 100%;
        height: 100%;
        padding: 0;
        margin: 0;
        overflow: hidden;
    }
    #app {
        @import "./styles/variable";
        background-color: $bg;
        width: 100%;
        height: 100%;
        padding: 0;
        margin: 0;
        overflow: hidden;

        .iview-input {

            .ivu-input-group-prepend {
                background-color: transparent;
                border-color: #222222;
            }
            input {
                background-color: transparent;
                border-color: #222222;

                &::placeholder {
                    color: #a3a3a3;
                }
            }
        }
    }
</style>
