<template>
  <div class="classify">
      <div class="oneClass">
          <ul>
                <li 
			  		v-for="item in oneClassList"
					@click="selOneClass(item.classid)"
					:class="{on:item.classid==selId}"
				>
				{{item.classdesc}}
				</li>
          </ul>
      </div>
      <div class="twoClass">
          <ul>
              <li v-for="item in twoClassList">
                  <h6>{{item.name}}</h6>
                  <dl>
                      <dd v-for="i in item.threeCategoryList">
                          <router-link :to="{name:'goodsList',query:{threeCategory:i.id}}">
                            <img v-lazy="i.imgUrl" alt="">
                            <p>{{i.threeName}}</p>
                          </router-link>
                      </dd>
                  </dl>
              </li>
          </ul>
      </div>
	  <div class="foot_height"></div>
      <foot></foot>
      <loading :isShow='loadingIsShow'></loading>
  </div>
</template>
<script>
import foot from "@/components/foot";
import loading from "@/components/loading";
export default {
  name: "classify",
  data() {
    return {
      loadingIsShow: false,
      isFirstEnter: true,
      oneClassList: [],
      twoClassList: [],
      selId: null,
      total: {}
    };
  },
  components: {
    foot,
    loading
  },
  methods: {
    getOneClassData() {
      this.$http.post(this.API.getCategory).then(res => {
        if (res.data.message) {
          this.oneClassList = res.data.classList;
          this.selOneClass(res.data.classList[0].classid);
        }
      });
    },
    getTwoClassData(d) {
      this.loadingIsShow = true;
      this.$http.post(this.API.getCategoryTwo, `categoryId=${d}`).then(res => {
        if (res.data.message) {
          this.twoClassList = res.data.classTwoList;
          this.total[d] = res.data.classTwoList;
          this.loadingIsShow = false;
        }
      });
    },
    selOneClass(e) {
      this.selId = e;
      if (this.total[e]) {
        this.twoClassList = this.total[e];
      } else {
        this.getTwoClassData(e);
      }
    }
  },
  created() {
    this.isFirstEnter = true;
  },
  mounted() {},
  activated() {
    if (!this.$route.meta.isKeepAlive || this.isFirstEnter) {
      console.log("我是classify的activated方法");
      this.getOneClassData();
    }
    this.$route.meta.isKeepAlive = false;
    this.isFirstEnter = false;
  },
  beforeRouteEnter(to, from, next) {
    if (from.name == "goodsList") {
      to.meta.isKeepAlive = true;
    }
    next();
  },
  deactivated() {}
};
</script>
<style scoped>
.classify {
  background: #fff;
}
.classify .oneClass {
  position: fixed;
  top: 0;
  left: 0;
  width: 200px;
  height: 100%;
  background: #fff;
}
.classify .oneClass ul li {
  height: 40px;
  line-height: 40px;
  margin: 60px 0;
  text-align: center;
  font-size: 28px; /*px*/
  box-sizing: border-box;
  border-left: 6px solid #fff; /*px*/
}
.classify .oneClass ul li.on {
  border-left: 6px solid red; /*px*/
  color: red;
}
.classify .twoClass {
  margin-left: 200px;
  border-left: 1px solid #999; /*px*/
}
.classify .twoClass ul li h6 {
  font-size: 30px; /*px*/
  padding-top: 50px;
  padding-left: 40px;
  padding-bottom: 20px;
}
.classify .twoClass ul li dl {
  display: flex;
  flex-wrap: wrap;
  margin-left: 40px;
}
.classify .twoClass ul li dl dd {
  width: 33%;
  margin-bottom: 60px;
}
.classify .twoClass ul li dl dd img {
  width: 60%;
  display: block;
  margin: 0 auto;
}
.classify .twoClass ul li dl dd p {
  font-size: 24px; /*px*/
  text-align: center;
}
.foot_height {
  height: 100px;
}
</style>


