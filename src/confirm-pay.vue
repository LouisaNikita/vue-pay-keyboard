<template lang="html">
    <div class="confirm-pay">
        <transition name="confirmPay">
            <div class="content" v-show="visible">
                <div class="pay-header">
                    <div class="title">输入交易密码</div>
                    <div class="iconfont icon-pop_ico_close" @click="close" v-show="!paying"></div>
                </div>
                <div class="confirm-box">
                    <trade-pwd-input :pwdIndex="pwdIndex"></trade-pwd-input>
                    <p class="forget-pwd ui-link" @click="forgetPwd">忘记交易密码</p>
                    <digital-keyboard @pwdCompleted="getPwd($event)" @inputIndex="getPwdIndex($event)" :reset="reset"></digital-keyboard>
                </div>
                <div class="pay-status" v-show="paying">
                    <spinner size="40" color="#cc9d3e"></spinner>
                    <p class="pay-status-desc">确认中</p>
                </div>
            </div>
        </transition>
        <transition name="mask">
            <div class="tra-mask" v-show="visible"></div>
        </transition>
    </div>
</template>

<script>
    import messageBox from 'components/message-box';
    import { verifyTradePwd, getProfile } from 'services/accounts.js';
    import router from 'route/router.js';

    export default {
        props:['visible'],
        data(){
            return {
                reset: false,
                paying: false,
                pwdIndex: 0,
            };
        },
        components:{
            'spinner': require('components/spinner.vue'),
            'digital-keyboard': require('./digital-keyboard.vue'),
            'trade-pwd-input': require('./trade-pwd-input.vue')
        },
        methods:{
            getPwd(pwd){
                this.paying = true;
                console.log(pwd);
                verifyTradePwd({
                    'passwd': pwd
                }).then( resp => {
                    if(resp.rstcode == '000000'){
                        this.$emit('confirmPay');
                    }else{
                        messageBox({
                            content: resp.friendMsg,
                            btns:[{
                                name: '确认',
                                action:()=>{
                                    this.pwdIndex = 0; //input-box
                                    this.reset = true; //keyboard
                                    this.paying = false;
                                }
                            }]
                        });
                    }
                });
            },
            getPwdIndex(index){
                this.pwdIndex = index;
                if(index>0){
                    // make reset to default state when key up
                    this.reset = false;
                }
            },
            close(){
                this.pwdIndex = 0;
                this.$emit('closeConfirmPay');
            },
            forgetPwd(){
                this.$store.commit('updateTradeLackView', this.$store.state.route);
                router.push({name: 'accounts:forget-trade-pwd'});
            }
        },
        watch:{
            visible(){
                if(!this.visible){
                    //init
                    this.reset = true;
                    this.pwdIndex = 0;
                    this.paying = false;
                }else{
                    this.reset = false;
                    getProfile().then( userProfile => {
                        if(userProfile.hastradepasswd == 0){
                            this.$store.commit('updateTradeLackView', this.$store.state.route);
                            messageBox({
                                title: '您还未设置交易密码',
                                content: '请先设置交易密码',
                                btns:[{
                                    name: '取消',
                                },{
                                    name: '去设置',
                                    action:function(){
                                        router.push({name: 'accounts:set-trade-pwd'});
                                    }
                                }]
                            });
                        }
                    });
                }
            }
        }
    };
</script>

<style lang="css"　scoped>
    @import 'styles/vars.css';
    @import 'styles/mixins.css';
    .confirm-pay{
        position: absolute;
        bottom: 0;
        right: 0;
        left: 0;
        .tra-mask{
            transition: opacity .3 linear;
            @mixin cover 0.5;
        }
        .content{
            position: fixed;
            bottom: 0;
            right: 0;
            left: 0;
            z-index: 99;
            background: $white-color;
            .pay-header {
                line-height: 55px;
                margin: 0 20px;
                .title {
                    font-size: $em-title-font;
                    text-align: center;
                }
                .iconfont {
                    position: absolute;
                    top: 0;
                    right: 10px;
                    font-size: 22px;
                }
            }
            .confirm-box {
                overflow: hidden;
                .forget-pwd {
                    line-height: 55px;
                    margin-right: 20px;
                    text-align: right;
                }
            }
            .pay-status {
                @mixin full-page;
                top: 55px;
                background: $white-color;
                display: flex;
                justify-content: center;
                flex-direction: column;
                .pay-status-desc{
                    text-align: center;
                    margin-top: 20px;
                }
            }
        }
    }
    .confirmPay-enter-active {
      transition: all .3s ease;
    }
    .confirmPay-leave-active {
      transition: all .3s ease;
    }
    .confirmPay-enter, .confirmPay-leave-active {
      transform: translate3d(0, 100%, 0);
    }
    .mask-enter,
    .mask-leave-active {
      opacity: 0;
    }
</style>
