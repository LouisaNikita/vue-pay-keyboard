<template lang="html">
    <div class="enter-box">
        <div class="main-box">
            <ul class="code-box">
                <li><span class="point" v-show="pwdIndex > 0"></span></li>
                <li><span class="point" v-show="pwdIndex > 1"></span></li>
                <li><span class="point" v-show="pwdIndex > 2"></span></li>
                <li><span class="point" v-show="pwdIndex > 3"></span></li>
                <li><span class="point" v-show="pwdIndex > 4"></span></li>
                <li><span class="point" v-show="pwdIndex > 5"></span></li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        props: ['pwdIndex']
    };
</script>

<style lang="css" scoped>
    @import 'styles/vars.css';
    @import 'styles/mixins.css';
    .enter-box {
        .main-box {
            .code-box {
                display: flex;
                margin: 0px 20px;
                border: 1px solid $disabled-color;
                li {
                    display: flex;
                    justify-content: center;
                    align-items: center;
                    flex: 1;
                    height: 50px;
                    border-right: 1px solid $disabled-color;
                    &:last-child{
                        border-right: none;
                    }
                    .point{
                        /*position: relative;*/
                        /*top: 45%;*/
                        /*left: 45%;*/
                        /*transform: translate(-50%,-50%);*/
                        content: '';
                        display: inline-block;
                        background: #333;
                        width: 12px;
                        height: 12px;
                        border-radius: 100%;
                    }
                }
            }
        }
    }
</style>
