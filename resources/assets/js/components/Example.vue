<template>

    <div class="container">
        <div class="row">
            <div class="col-md-10  col-md-offset-1">
                <div class="col-xs-2" style="padding: 0px">

                    <div class="panel panel-default">
                        <div class="panel-heading">
                           结果提示
                        </div>
                        <div class="panel-body">
                           <div class="btn-group">

                               <button class="btn btn-default btn-xs" :class="{active: capion=='hide'}" @click="capion = 'hide'" >隐藏</button>
                                <button class="btn btn-default btn-xs" :class="{active: capion=='show'}" @click="capion = 'show'" >显示</button>
                                <button class="btn btn-default btn-xs"  :class="{active: capion=='delay'}" @click="capion = 'delay'" >延迟</button>
                            </div>
                        </div>
                    </div>

                    <div class="panel panel-default">
                        <div class="panel-heading">
                           按键说明
                        </div>
                        <div class="panel-body">
                           ↵ - 回车
                        </div>
                    </div>
                </div>
                <div class="col-xs-8">

                    <div class="panel panel-default">
                        <div class="panel-heading">
                            <p class="text-center h3">
                           <!--  <small>{{ cur_index+1 }}.</small> -->{{ cur.name }}</p>
                            <input id="keyTextArea" class="input-lg form-control" rows="3" v-model.trim="command" @keyup.enter="onEnter" :placeholder="getPlaceHolder()"/>
                        </div>
                        <div class="panel-body">
                            <div class="resultAlert" :class="['alert',item.status?'alert-success':'alert-danger']" v-for="item in items">
                                <h4>{{ item.input }}</h4>
                                <strong>{{ item.name }}</strong>
                                <h4 style="color: #999;"><i>{{ item.key }}</i></h4>
                            </div>
                        </div>
                    </div>

                </div>
                <div class="col-xs-2"  style="padding: 0px">
                    <div class="panel panel-default">
                        <div class="panel-heading">
                           统计
                        </div>
                        <div class="panel-body">
                           <p>新增：{{ new_cnt }}</p>
                           <p>回顾：{{ review_cnt }}</p>
                           <p>完成：{{ completed_cnt }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted: function() {
            console.log('mounted')
            this.$http.jsonp('http://shortcut.com/api/1/keys', {}, {
                headers: {

                },
                emulateJSON: true
            }).then(function(response) {
              // 这里是处理正确的回调
                this.keys       = response.data.keys;
                this.new_cnt    = response.data.new_cnt;
                this.review_cnt = response.data.review_cnt;

                this.cur        = this.keys[this.cur_index]

            }, function(response) {
                // 这里是处理错误的回调
                console.log(response)
            });
        },
        data() {
            return {
                    command : '',
                    capion: 'show',
                    cur_index : 0,
                    new_cnt : 0,
                    review_cnt : 0,
                    completed_cnt : 0,
                    cur : [],
                    items : []
            }
        },
        methods:{
            onEnter(){

                this.items.unshift({
                    key: this.cur.key,
                    name: this.cur.name,
                    input: this.command + '↵',
                    status: this.command == this.cur.key
                })

                this.command = ''
                this.cur_index +=1

                if(!this.keys[this.cur_index]){
                    this.cur_index = 0
                }

                this.cur = this.keys[this.cur_index]

                if(this.completed_cnt < this.keys.length)
                    this.completed_cnt +=1;
            },
            getPlaceHolder(){
                if(this.capion == 'hide')
                    return '';
                else if(this.capion == 'show')
                    return this.cur.key;
                else{
                    setTimeout(function(){
                        // return this.cur.key;
                    },1000);
                }
            }
        },
        created()
        {
            console.log('created')

        }
    }



</script>
<style>
    #keyTextArea{
        font-size: 30px;
        font-family: 'Inconsolata', 'courier';
        height: 100px;

    }

/*    input:placeholder {
        color: red;

    }

    input::-webkit-input-placeholder {

        transition: opacity 4s ease-in-out;
        opacity: 0;
    }*/

    .resultAlert{
        font-family: 'Inconsolata', 'courier';
    }
</style>
