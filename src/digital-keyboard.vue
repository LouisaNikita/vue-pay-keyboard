<template lang="html">
    <div class="key-box">
        <ul class="key-board" @click="getKey($event)">
            <li>
                <div>1</div>
                <div>2</div>
                <div>3</div>
            </li>
            <li>
                <div>4</div>
                <div>5</div>
                <div>6</div>
            </li>
            <li>
                <div>7</div>
                <div>8</div>
                <div>9</div>
            </li>
            <li>
                <div class="grey-key"></div>
                <div>0</div>
                <div class="grey-key iconfont icon-delete" @click="deleteKey()"></div>
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        data(){
            return {
                tradePwd: '',
                pwdIndex: 0
            };
        },
        props:{
            reset: Boolean,
        },
        watch:{
            reset(val){
                if(val){
                    console.log('reset',val);
                    this.tradePwd = '';
                    this.pwdIndex = 0;
                }
            }
        },
        methods:{
            getKey(e){
                let target = e.target;
                let n = target.innerText;

                target.className = 'active';
                setTimeout(()=>{
                    target.className = '';
                },100);
                let pwdLength = this.tradePwd.length;
                if(pwdLength < 6){
                    this.tradePwd += n;
                    this.pwdIndex ++;
                    this.$emit('inputIndex',this.pwdIndex);
                    if(this.tradePwd.length == 6){
                        setTimeout(()=>{
                            this.$emit('pwdCompleted',this.tradePwd);
                        },1000);
                    }
                }
            },
            deleteKey(){
                event.stopPropagation();
                let el = event.target;
                el.className += ' active';
                setTimeout(()=>{
                    el.className = el.className.replace(' active','');
                },100);
                let tradePwdlength = this.tradePwd.length;
                if(tradePwdlength > 0){
                    this.tradePwd = this.tradePwd.substring(0,tradePwdlength-1);
                    this.pwdIndex --;
                }
                this.$emit('inputIndex',this.pwdIndex);
            }
        }
    };
</script>

<style lang="css">
    @import 'styles/vars.css';
    @import 'styles/mixins.css';
    .key-box{
        background: $white-color;
        position: relative;;
        left: 0;
        right: 0;
        bottom: 0;
        .key-board{
            li{
                display: flex;
                border-top: 1px solid $border-color;
                div{
                    flex: 1;
                    text-align: center;
                    width: 33.33%;
                    line-height: 55px;
                    font-size: 35px;
                    &:nth-child(2){
                        width: 32%;
                        border-right: 1px solid $border-color;
                        border-left: 1px solid $border-color;
                    }
                    &.grey-key {
                        background: $bg-color;
                        color: $disabled-color;
                        font-size: 30px;
                        &.active{
                            background: $white-color;
                            color: $text-font-color;
                        }
                    }
                    &.active{
                        background: $disabled-color;
                        color: $white-color;
                    }
                }
            }
        }
    }
</style>
