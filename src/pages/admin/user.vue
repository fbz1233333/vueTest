<template>
  <div>

    <br>
    <Page :total="pageTotal" :current="pageNum" :page-size="pageSize"  show-sizer show-total
          placement="top" @on-change="handlePage" @on-page-size-change='handlePageSize' ></Page>
    <br>

    <Modal v-model="ifShow" title="INFO" @on-ok="handleUpdate">
      <Row>
        <div v-for="(value,key,index) in entity" >
          <label>{{key}}</label> <Input v-model="entity[key]" size="small"/>
        </div>
      </Row>
    </Modal>

    <Table :data="users" :columns="userColumns">
    </Table>

    <!--    <Button type="primary" size="large" @click="exportData(1)"><Icon type="ios-download-outline"></Icon> Export source data</Button>-->
    <!--    <Button type="primary" size="large" @click="exportData(2)"><Icon type="ios-download-outline"></Icon> Export sorting and filtered data</Button>-->
    <!--    <Button type="primary" size="large" @click="exportData(3)"><Icon type="ios-download-outline"></Icon> Export custom data</Button>-->
  </div>

</template>
<script>
  export default {
    data(){
      return{
        ifShow:false,
        entity:{},

        pageTotal:0,
        pageNum:1,
        pageSize:10
      }
    },
    components:{


    },
    methods: {
      handlePage(value) {
        this.pageNum=value;
        console.log(value);
        this.$store.dispatch('ajaxGetUserByPage',{
          pageNum:this.pageNum,
          pageSize:this.pageSize
        })

      },
      handlePageSize(value) {
        this.pageSize = value;
        console.log(value);
        this.$store.dispatch('ajaxGetUserByPage',{
          pageNum:this.pageNum,
          pageSize:this.pageSize
        })

      },
      handleUpdate(){
        console.log("更新的entity为",this.entity)
        this.$store.dispatch('ajaxUpdateUser',this.entity)
      },

      handleDelete(index){
        this.entity=this.users[index];
        this.entity.isDel=1-this.entity.isDel;
        this.$store.dispatch('ajaxUpdateUser',this.entity)
      },
      //Modal相关
      update(index){
        this.entity=this.users[index]
        this.ifShow=true;

      }

    },
    created:function () {
      this.$store.dispatch('ajaxGetUserByPage',{
        pageNum:this.pageNum,
        pageSize:this.pageSize
      })

    },

    computed:{
      users:function () {
        this.pageTotal=this.$store.getters.getUserCount
        return this.$store.getters.getUserList;
      },
      userColumns:function () {
        return[
          {
            title:"id",
            key:"id"
          },
          {
            title:"用户名",
            key:"name"
          },
          {
            title:'密码',
            key:'password'
          },
          {
            title:"状态",
            key:'isDel',

          },
          {
            title:"创建时间",
            key:'createTime'
          },{
            title:'操作',
            key:'action',
            width:300,
            align:'center',
            render:(h,params)=>{
              return h('div', [
                h('Button', {
                  props: {
                    type: 'primary',
                    size: 'small'
                  },
                  style: {
                    marginRight: '0px'
                  },
                  on: {
                    click: () => {
                      //
                      this.handleDelete(params.index)
                    }
                  }
                }, '删除/复原'),
                h('Button',{
                  props:{
                    type: 'error',
                    size: 'small'
                  },
                  style: {

                  },
                  on:{
                    click:()=>{
                      console.log(params.index)
                      this.update(params.index)
                    }
                  }
                },'修改')
              ]);
            }
          }
        ]
      }
    },
  }
</script>
<style scoped>
</style>
