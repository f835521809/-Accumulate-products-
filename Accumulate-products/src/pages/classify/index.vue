<template>
  <div class="wrap">
    <scroll-view scroll-x="true" class="chun">
       <li @click="btn">今日推荐</li>
       <li @click="clickType(index, item.cid)" v-for="(item, index) in list" :key="index" :class="active===index?'active':''">
        {{item.cname}}
      </li>
    </scroll-view>
    <div class="main">
      <div v-for="(items, indexs) in lists" :key="indexs" class="center" @click="clickSubType(items.cid)">
        <img :src="items.imgUrl" class="tu">
        <span class="txt">{{items.cname}}</span>
      </div>
      <!-- <div>
        没有该数据
      </div> -->
    </div>
    <div class="tbar">
      <span @click="clickSortType(1)">综合</span>
      <span @click="clickSortType(2)">最新</span>
      <span @click="clickSortType(3)">价格</span>
    </div>
    <div class="dls">
          <dl v-for='(item,index) in props' :key="index" class="dl-re" @click="clickItem(item.pid)">
            <dd>
             <img :src="item.mainImgUrl">
           </dd>
           <dt>
            <p>{{item.title}}</p>
                <span>包税</span>
                <span>满299减30</span>
                <span>满299减30</span>
            <b>￥{{item.earnMoney}}</b>
           <img src="/static/images/new.png">
          </dt>
          </dl>
    </div>
  </div>
</template>

<script>
  import {
    mapState,
    mapMutations,
    mapActions
  } from "vuex"
  export default {
    data() {
      return {
        page: 1,
        cid: '',
        sortType: 1,
        // hasMore: true,
        active: 0,
        listProps: []
      }
    },
    computed: {
      ...mapState({
        list: state => state.classify.list,
        props: state => state.classify.props
      }),
      // 子分类
      lists(){
        if (this.list && this.list.length){
          return this.list[this.active].childs;
        }
        return [];
      },
      // 判断是否还有更多
      hasMore(){
        return this.page*20 === this.props.length
      }
    },
    methods: {
      ...mapActions({
        getList: 'classify/getList',
        getProps: 'classify/getProps'
      }),
      // 获取数据接口
      async fetchData(){
        wx.showLoading({
          title: '数据加载中...', //提示的内容,
          mask: true, //显示透明蒙层，防止触摸穿透,
        });
        let result = await this.getProps({
          pageIndex: this.page,
          cid: this.cid,
          sortType: this.sortType
        });
         wx.hideLoading();
      },
      // 点击跳转详情
      clickItem(id) {
        wx.navigateTo({
          url: "/pages/go2detail/main?id="+id
        })
      },
      // 一级分类点击
      clickType(ind, cid) {
        this.active = ind
        this.cid = cid;
        this.page = 1;
        // 重新请求数据
        this.fetchData();
      },
      // 二级分类点击
      clickSubType(cid){
        this.cid = cid;
        this.page = 1;
        // 重新请求数据
        this.fetchData();
      },
      // 分类条件点击
      clickSortType(sort){
        this.sortType = sort;
        this.page = 1;
        // 重新请求数据
        this.fetchData();
      }
    },
    onReachBottom(){
      if (this.hasMore){
        this.page++;
        this.fetchData();
      }
    },
    onLoad(options) {
      this.active = options.index || 0;
    },
    async onShow(){
      await this.getList();
      this.cid = this.list[this.active].cid;
      this.fetchData();
    }
  }
</script>

<style lang="scss" scoped>
.wrap {
  width: 100%;
  background: #f6f6f6;
  .chun {
    width: 100%;
    display: flex;
    white-space: nowrap;
    height: 100rpx;
    background: #fff;
    li {
      font-size: 32rpx;
      display: inline-block;
      height: 100rpx;
      text-align: center;
      color: #484848;
      box-sizing: border-box;
      margin: 0 20rpx;
    }
    li.active {
      border-bottom: solid #56d2bf 6rpx;
      line-height: 94rpx;
      font-weight: 500;
      color: red;
    }
  }
  .tbar {
    width: 100%;
    height: 40px;
    line-height: 40px;
    display: flex;
    justify-content: space-around;
  }
  .main {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    background: rgba(255, 255, 255, 1);
    .center {
      width: 25%;
      display: inline-block;
      padding: 20rpx;
      box-sizing: border-box;
      text-align: center;
      .tu {
        width: 100rpx;
        height: 100rpx;
      }
      .txt {
        font-size: 30rpx;
        display: block;
      }
    }
  }

  .dls {
    width: 100%;
    height: 100%;
    display: flex;
    flex-wrap: wrap;
    dl {
      width: 48%;
      height: 280px;
      background: rgba(255, 255, 255, 1);
      border-radius: 5px;
      margin-left: 4px;
      margin-top: 4px;
      position: relative;
      dd {
        text-align: center;
        height: 200px;
        background: rgba(255, 255, 255, 1);
        border-radius: 5px 5px 0px 0px;
        img {
          width: 163px;
          height: 145px;
          margin: 30px 23px 25px 14px;
        }
      }
      dt {
        margin-top: 2px;
        width: 280px;
        p {
          width: 186px;
          height: 20px;
          font-size: 12px;
          font-family: PingFangSC-Regular;
          font-weight: 400;
          color: rgba(50, 58, 69, 1);
          line-height: 20px;
          overflow: hidden;
          white-space: nowrap;
          text-overflow: ellipsis;
        }
        span {
          border-radius: 2px;
          border: 1px solid rgba(252, 93, 123, 1);
          width: 50px;
          height: 14px;
          font-size: 10px;
          font-family: PingFangSC-Regular;
          font-weight: 400;
          color: rgba(252, 93, 123, 1);
          line-height: 14px;
          margin-left: 5px;
        }
        b {
          width: 19px;
          height: 32px;
          font-size: 18px;
          font-family: DINAlternate-Bold;
          font-weight: bold;
          color: rgba(252, 93, 123, 1);
          line-height: 31px;
        }
        img {
          width: 25px;
          height: 13px;
          background: linear-gradient(
            217deg,
            rgba(255, 184, 72, 1) 0%,
            rgba(255, 119, 55, 1) 100%
          );
          border-radius: 2px;
          position: absolute;
          top: 0;
          right: 0;
          margin: 12px 10px 258px 169px;
        }
      }
    }
  }
}
</style>
