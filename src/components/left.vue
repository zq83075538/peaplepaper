<template>
  
    <el-menu
      :default-active="active"
      class="el-menu-vertical-demo"
      :default-openeds="[openeds]" >
      <el-submenu v-for="(o, i) in menus" :index="''+i" :key="i">
        <template slot="title">{{o.name}}</template>
        <el-menu-item v-for="(data,index) in o.datas" :key="index" :index="i+'-'+index" @click="menuclick(data, [i,index])">
          {{data.value}}
        </el-menu-item>
      </el-submenu>
    </el-menu>
</template>
<script>
export default {
  data() {
    return {
      openeds: '',
      menus: [
        {
          name: "用户画像",
          datas: [
            {
              api: "catlist?type=portrait_media",
              value: "用户画像日报列表"
            },
            {
              api: "maplist?type=sex",
              value: "性别"
            },
            {
              api: "maplist?type=age",
              value: "年龄"
            },
            {
              api: "maplist?type=hobby",
              value: "偏好"
            },
            {
              api: "maplist?type=province",
              value: "地域"
            },
            {
              api: "maplist?type=dev",
              value: "终端"
            },
            {
              api: "maplist?type=os",
              value: "操作系统"
            },
          ]
        },
        {
          name: "党报追踪",
          datas: [
            {
              api: "newslist?type=news_paper",
              value: "报纸"
            },
            {
              api: "maplist?type=word",
              value: "词云"
            },
            {
              api: "maplist?type=paper_level",
              value: "媒体分类"
            },
            {
              api: "newslist?type=news_top",
              value: "头条"
            },
            {
              api: "newslist?type=track_news",
              value: "头版热榜"
            }
          ]
        },
        {
          name: "新闻动态",
          datas: [
            {
              api: "maplist?type=web_article",
              value: "网站"
            },
            {
              api: "maplist?type=weibo_article",
              value: "微博"
            },
            {
              api: "maplist?type=wechat_article",
              value: "微信"
            },
            {
              api: "maplist?type=app_article",
              value: "app"
            },
            {
              api: "maplist?type=content_reach_monitor",
              value: "内容推荐"
            },
            {
              api: "maplist?type=content_input_monitor",
              value: "内容入库"
            },
            {
              api: "newslist?type=content_real_time",
              value: "实时内容"
            }
          ]
        },
        {
          name: "入驻党媒",
          datas: [
            {
              api: "catlist?type=party_media_data",
              value: "入驻媒体"
            },
            {
              api: "catlist?type=media_position",
              value: "媒体地图"
            }
          ]
        },
        {
          name: "内容推荐",
          datas: [
            {
              api: "maplist?type=recommend",
              value: "推荐量-曲线"
            },
            {
              api: "maplist?type=read",
              value: "阅读量-曲线"
            },
            {
              api: "catlist?type=read_recommend_number",
              value: "推荐量和阅读量总数"
            },
            {
              api: "newslist?type=recommend_rank",
              value: "推荐风云榜"
            },
            {
              api: "newslist?type=read_rank",
              value: "阅读风云榜"
            },
            {
              api: "catlist?type=media_30",
              value: "近30天媒体列表"
            },
            {
              api: "maplist?type=day_30",
              value: "近30天-曲线"
            },
          ]
        },
        {
          name: "落地渠道",
          datas: [
            {
              api: "maplist?type=recommend_panel",
              value: "推荐量-曲线"
            },
            {
              api: "maplist?type=read_panel",
              value: "阅读量-曲线"
            },
            {
              api: "catlist?type=recommend_read_number",
              value: "推荐量和阅读量总数"
            },
            {
              api: "maplist?type=wx_rank",
              value: "微信影响力"
            },
            {
              api: "maplist?type=wb_rank",
              value: "微博影响力"
            },
            {
              api: "catlist?type=media_panel",
              value: "媒体列表"
            }
          ]
        },
      ],
      updateState: 0 //1暂停状态，0开启状态
    };
  },
  methods:{
    menuclick(data, indArr){
      this.$router.push({name: 'appmain', params:{api: data.api, page: 1}});
      let [ind0, ind1] = indArr;
      this.$emit('menuClick', data.api, [this.menus[ind0]['name'], data])
    }
  },
  created(){
    let {api} = this.$route.params;
    this.menus.forEach((o,i) => {
      o.datas.forEach((oo, ii) => {
        if(oo.api === api){
          this.openeds = '' + i;
          this.active = i + '-' + ii;
        }
      })
    })
  }
};
</script>
<style>
.el-submenu__title i{
  color: #f3f3f3;
}
.el-aside{
  box-shadow: 0 0 2px #999;
}
.el-menu.el-menu-vertical-demo{
  width: 98%;
}
.el-submenu{
  margin-top: 2px;
  margin-left: 1%;
}
.el-submenu.is-active{
  box-shadow: 0 0 10px #409EFF;
}
.el-submenu__title{
  background: #333;
  color: #fff;
}
.el-submenu__title:hover{
  background: #333;
  color: #fff;
}
.el-menu-item.is-active{
  background: #eee;
}
.el-submenu .el-menu-item.is-active {
  color: #409EFF;
  border-color: #409EFF;
}
.el-submenu .el-menu-item {
  border-right: 3px solid #fff;
  border-left: 3px solid #fff;
  color: #828282;
  text-decoration: none;
  height: 34px;
  line-height: 34px;
  width: 100%;
  min-width: 10px;
}
</style>
