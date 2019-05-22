/*
 * @Description: 表格分页组件
 */
<template>
  <Row class="footer-page">
    <ul class="page-left">
      <li class="l-spacing">
          <Select v-model="size" class="page-select" placeholder="" @on-change="pageSize">
            <Option v-for="item in selectList" :value="item.value" :key="item.value">{{ item.label }}</Option>
          </Select>
      </li>
      <!--  src="../../../images/3.png" -->
      <li :class="disabled1?'disabled':'able'" id="page-home" @click="home" style="font-weight:bold;margin-top:3px"> <img :src="disabled1 ? imgurlLeft2: imgurlLeft22 " alt="" /> </li>
      <li :class="disabled1?'disabled':'able'" id="page-previous" @click="previous" style="margin-top:3px"> <img  :src="disabled1 ? imgurlLeft1: imgurlLeft11 " alt="" > </li>
      <li class="l-spacing">{{currentPage}}/{{totalPage}}</li>
      <li :class="disabled2?'disabled':'able'" id="page-next" @click="next"  style="margin-top:3px"> <img  :src="disabled2 ? imgurlRight1: imgurlRight11 " alt="" > </li>
      <li :class="disabled2?'disabled':'able'" id="page-lastPage" @click="lastPage" style="font-weight:bold;margin-top:3px"> <img :src="disabled2 ? imgurlRight2: imgurlRight22 " alt=""> </li>
      <li class="l-spacing">
        <span>转到&nbsp;</span>
        <Input v-model="pick" class="page-input" @on-keypress="pagePick" />
        <span>&nbsp;页</span>
      </li>
      <li class="l-spacing" @click="upDate" style="cursor: pointer;font-weight:bold;font-size:17px"><Icon type="md-refresh" /></li>
    </ul>
    <ul style="float:left;width: 33.3%;text-align:center">
      <li style="float:right;font-weight:bold">{{sumHint}}</li>
    </ul>
    <ul class="page-right">
      <li class="o-spacing">当前显示{{start}}-{{end}}条</li>
      <li class="r-spacing">共计{{dataLength}}条</li>
    </ul>
  </Row>
</template>

<script>
  import imgurlLeft1 from '../../../images/1.png';
  import imgurlLeft11 from '../../../images/11.png';
  import imgurlLeft2 from '../../../images/3.png';
  import imgurlLeft22 from '../../../images/33.png';
  
  import imgurlRight1 from '../../../images/2.png';
  import imgurlRight11 from '../../../images/22.png';
  import imgurlRight2 from '../../../images/4.png';
  import imgurlRight22 from '../../../images/44.png';
  



  export default {
    name: '',
    props: [''],
    data () {
      return {
        imgurlLeft1,
        imgurlLeft11,        
        imgurlLeft2,
        imgurlLeft22,        
        imgurlRight1,
        imgurlRight11,        
        imgurlRight2,
        imgurlRight22,
        

        // 原始数据
        martix: [],
        faker: [],
        clone: [],
        flag: true,
        // 当前页码
        currentPage: 1,
        // 总页数
        totalPage: 1,
        // 表格数据的长度
        dataLength: 0,
        //汇总字段
        sumHint:'',
        // 每页显示多少条
        size: 20,
        // 开始条目
        start: 0,
        // 结束条目
        end: 0,
        // 上一页与首页的样式
        disabled1: true,
        // 下一页与尾页的样式
        disabled2: false,
        // 跳转到指定页
        pick: '',
        // 下拉框制定显示条目
        selectList: [{
            value: 10,
            label: 10
          },
          {
            value: 20,
            label: 20
          },
          {
            value: 30,
            label: 30
          },
          {
            value: 40,
            label: 40
          },
          {
            value: 50,
            label: 50
          },
          {
            value: 100,
            label: 100
          }
        ],
        searchList:{},
        likeStr:'',//普通查询信息
        parentId:''
      };
    },

    components: {},

    computed: {},

    beforeMount () {},

    mounted () {
      //  this.start-1:offset
      //  this.size:limit
    },

    methods: {
      upDate(){
        // this.$emit('pager');
        this.revert() 
      },
      // 初始化
      init (total,sumHint,parentId) {
        // 数据总长度
        this.pick='';
        this.dataLength = total;
        this.sumHint = sumHint;
        this.parentId = parentId;
        this.page(this.currentPage)
      },
      initSearch (total,sumHint,str) {
        
        str instanceof Object ? this.likeStr = str.likeStr : this.likeStr = str;//判断查询条件是否为高级查询
        this.currentPage = 1;// 数据总长度
        this.dataLength = total;
        this.sumHint = sumHint;
        // this.likeStr = str;
        debugger
        this.page(this.currentPage);
      },
      // 重置  刷新
      revert () {
        // this.currentPage = 1
        // this.disabled1 = true
        debugger
        this.page(this.currentPage);
        this.pageClass(this.currentPage);
        this.$emit('pager', [this.start-1, this.size,this.likeStr,this.parentId]);
        
      },
      // 分页根方法
      page (currentPage) {
        
        let total = this.dataLength,
            size = this.size,
            start = (currentPage - 1) * size,
            end = currentPage * size
        end = (end > total) ? total : end
        // 起始数据
        this.start = start + 1
        // 结束数据
        this.end = end;
        // 分页页数
        this.totalPage = Math.ceil(total / size);
        if (total < size) {
          this.pageClass(0);
        }
      },
      // 首页
      home () {
        this.currentPage = 1;
        this.disabled1 = true;
        this.page(this.currentPage);
        this.$emit('pager', [this.start-1, this.size ,this.likeStr,this.parentId]);
        this.pageClass(this.currentPage);
      },
      // 前一页
      previous () {
        if (this.currentPage > 1) {
          this.currentPage--;
          this.page(this.currentPage);
          this.$emit('pager', [this.start-1, this.size ,this.likeStr,this.parentId]);
          this.pageClass(this.currentPage);
        } else {
          this.disabled1 = true;
        }
      },
      // 下一页
      next () {
        debugger
        if (this.currentPage < this.totalPage) {
          this.currentPage++;
          this.page(this.currentPage);
          this.$emit('pager', [this.start-1, this.size ,this.likeStr,this.parentId]);
          this.pageClass(this.currentPage);
        } else {
          this.disabled2 = true;
        }
      },
      // 尾页
      lastPage () {
        if (this.dataLength > this.size || this.dataLength == this.size) {
          if (this.dataLength % this.size != 0) {
            this.currentPage = parseInt(this.dataLength / this.size) + 1;
          } else {
            this.currentPage = parseInt(this.dataLength / this.size);
          }
          this.disabled2 = true;
          this.page(this.currentPage);
          this.$emit('pager', [this.start-1, this.size ,this.likeStr,this.parentId]);
          this.pageClass(this.currentPage);
        }
      },
      // 跳转到指定页
      pagePick () {
        // 回车确认
        if (event.keyCode == 13) {
          if (this.pick < this.totalPage || this.pick == this.totalPage) {
            this.currentPage = this.pick;
            this.page(this.currentPage);
            this.$emit('pager', [this.start-1, this.size ,this.likeStr,this.parentId]);
            this.pageClass(this.currentPage);
          }
        }
      },
      // 每页显示数据长度
      pageSize () {
        this.currentPage = 1;
        this.page(this.currentPage);
        this.$emit('pager', [this.start-1, this.size ,this.likeStr,this.parentId]);
      },
      // 分页样式
      pageClass (currentPage) {
        // 分页切换样式
        if (currentPage == 1) {
          this.disabled1 = true;
        } else {
          this.disabled1 = false;
        }
        if (currentPage == this.totalPage) {
          this.disabled2 = true;
        } else {
          this.disabled2 = false;
        }
        if (currentPage == 0) {
          this.disabled1 = true;
          this.disabled2 = true;
        }
      }
    },

    watch: {}

  }
</script>

<style lang="less" scoped>
  @import './Pager.less';
</style>

<style scoped>
 
  .page-input >>> .ivu-input {
    width: 30px;
    height: 20px;
    border-radius: 0;
    border-color: #1B77E8;
    color: #1B77E8;
  }

  .page-select >>> .ivu-select-selection {
    width: 40px;
    height: 20px;
    border-radius: 0;
    border-color: #1B77E8;
  }

  .page-select >>> .ivu-select-selection i {
    color: #1B77E8;
    right: 2px;
  }

  .page-select >>> .ivu-select-selection span {
    height: 20px;
    line-height: 20px;
    color: #1B77E8;
    padding-left: 3px;
  }
</style>
