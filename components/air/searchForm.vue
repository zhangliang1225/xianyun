<template>
    <div class="search-form">

        <!-- 头部tab切换 -->
        <el-row type="flex" class="search-tab">
            <span v-for="(item, index) in tabs" :key="index"
            @click="handleSearchTab(item, index)"
            :class="{active: index === currentTab}">
                <i :class="item.icon"></i>{{item.name}}
            </span>
        </el-row>

        <el-form class="search-form-content" ref="form" label-width="80px">
            <el-form-item label="出发城市">
                <!-- fetch-suggestions 返回输入建议的方法 -->
                <!-- select 点击选中建议项时触发 -->
                <!-- 出发城市 -->
                <el-autocomplete
                v-model="form.goCity"
                @blur="goCityBlur"
                :fetch-suggestions="queryDepartSearch"
                placeholder="请搜索出发城市"
                @select="handleDepartSelect"
                class="el-autocomplete"
                ></el-autocomplete>
            </el-form-item>

            <!-- 到达城市 -->
            <el-form-item label="到达城市">
                <el-autocomplete
                v-model="form.getCity"
                @blur="getCityBlur"
                :fetch-suggestions="queryDestSearch"
                placeholder="请搜索到达城市"
                @select="handleDestSelect"
                class="el-autocomplete"
                ></el-autocomplete>
            </el-form-item>

            <!-- 出发时间 -->
            <el-form-item label="出发时间">
                <!-- change 用户确认选择日期时触发 -->
                <el-date-picker 
                v-model="form.goDate"
                type="date" 
                placeholder="请选择日期" 
                style="width: 100%;"
                @change="handleDate"
                  :picker-options="pickerOptions">>
                </el-date-picker>
            </el-form-item>
            <el-form-item label="">
                <el-button style="width:100%;" 
                type="primary" 
                icon="el-icon-search"
                @click="handleSubmit">
                    搜索
                </el-button>
            </el-form-item>
            <div class="reverse">
                <span @click="handleReverse">换</span>
            </div>
        </el-form>  
      </div>
</template>

<script>
import moment from "moment";
export default {
    data(){
        return {
            //  日期选择器限制选择
            pickerOptions: {
          disabledDate(time) {
            return time.getTime() + 3600 * 1000 * 24  < Date.now();
          },
            },
            tabs: [
                {icon: "iconfont icondancheng", name: "单程"},
                {icon: "iconfont iconshuangxiang", name: "往返"}
            ],
            // tab切换变量
            currentTab: 0,
            // 存储需要的数据
            form :{
                goCity : '',
                goCityNum : '',
                getCity : '',
                getCityNum : '',
                goDate : ''
            },
            // 储存出发城市的数组
            goData : '',
            // 储存到达城市的数组
            getData : ''
        }
    },
    methods: {
        // tab切换时触发
        handleSearchTab(item, index){
            if(index === 1){
                this.$message.warning('温馨提示，暂时不支持往返功能！')
                return;
            }
        },
        
        // 出发城市输入框获得焦点时触发
        // value 是选中的值，callback是回调函数，接收要展示的列表
        queryDepartSearch(value, callback){
            // cb([
            //     {value: 1},
            //     {value: 2},
            //     {value: 3},
            // ]);
            
            if(!value){
                this.goData = '';
                callback([]);
                return;
            };
            // 没封装的出发城市请求
            // this.$axios({
            //     url : 'airs/city',
            //     params : {
            //         name : value
            //     }
            // }).then(res =>{
            //     // console.log(res)
            //     const {data} = res.data;
            //     const newData =  data.map(v =>{
            //          v.value = v.name.replace('市','');
            //          return v;
            //      })
            //     //  console.log(newData)
            //     this.goData = newData;
            //     callback(newData)
            // })

            // vuex封装异步请求
            this.$store.dispatch('user/getAirData',value).then(newData => {
                this.goData = newData;
                callback(newData)
            })
        },
        //   出发城市失去焦点的函数
        goCityBlur(){
            if(!this.goData){
                return;
            }
             this.form.goCity = this.goData[0].value;
             this.form.goCityNum = this.goData[0].sort ;
        },
        // 到达城市失去焦点的函数
        getCityBlur(){
            if(!this.getData){
                return;
            }
              this.form.getCity = this.getData[0].value;
             this.form.getCityNum = this.getData[0].sort ;
        },
        // 目标城市输入框获得焦点时触发
        // value 是选中的值，cb是回调函数，接收要展示的列表
        queryDestSearch(value, callback){
              if(!value){
                  this.getData = '';
                  callback([]);
                  return;
              }

              this.$store.dispatch('user/getAirData',value).then(res=>{
                  this.getData = res;
                  callback(res)
              })
        },
       
        // 出发城市下拉选择时触发
        handleDepartSelect(item) {
            this.form.goCity = item.value;
            this.form.goCityNum = item.sort;
        },

        // 目标城市下拉选择时触发
        handleDestSelect(item) {
             this.form.getCity = item.value;
            this.form.getCityNum = item.sort;
        },

        // 确认选择日期时触发
        handleDate(value){
           this.form.goDate =  moment(value).format("YYYY-MM-DD");
        },

        // 触发和目标城市切换时触发
        handleReverse(){
            const {goCity , goCityNum , getCity , getCityNum} = this.form;
            this.form.goCity = getCity;
            this.form.goCityNum = getCityNum;
            this.form.getCity = goCity;
            this.form.getCityNum = goCityNum;
        },

        // 提交表单是触发
        handleSubmit(){
        //console.log(this.form)
        this.$router.push({
            path : '/air/flights',
            query : this.form
        })
        this.$message.success('获取机票列表成功！')
        }
    },
    mounted() {
       
    }
}
</script>

<style scoped lang="less">
.search-form{
    border:1px #ddd solid;
    border-top:none;
    width:360px;
    height:350px;
    box-sizing: border-box;
}

.search-tab{
  span{
    display: block;
    flex:1;
    text-align: center;
    height:48px;
    line-height: 42px;
    box-sizing: border-box;
    border-top:3px #eee solid;
    background:#eee;
  }

  .active{
    border-top-color: orange;
    background:#fff;
  }

  i{
    margin-right:5px;
    font-size: 18px;

    &:first-child{
      font-size:16px;
    }
  }
}

.search-form-content{
  padding:15px 50px 15px 15px;
  position: relative;

  .el-autocomplete{
    width: 100%;
  }
}

.reverse{
  position:absolute;
  top: 35px;
  right:15px;

  &:after,&:before{
      display: block;
      content: "";
      position: absolute;
      left:-35px;
      width:25px;
      height:1px;
      background:#ccc;
  }

  &:after{
      top:0;
    }

    &:before{
      top:60px;
    }

  span{
    display: block;
    position:absolute;
    top: 20px;
    right:0;
    font-size:12px;
    background: #999;
    color:#fff;
    width:20px;
    height:20px;
    line-height: 18px;
    text-align: center;
    border-radius: 2px;
    cursor: pointer;

    &:after,&:before{
      display: block;
      content: "";
      position: absolute;
      left:10px;
      width:1px;
      height:20px;
      background:#ccc;
    }

    &:after{
      top:-20px;
    }

    &:before{
      top:20px;
    }
  }
}
</style>