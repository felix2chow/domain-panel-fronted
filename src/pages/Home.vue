<template>
  <div
    v-loading="logining"
    element-loading-text="正在登录"
    element-loading-spinner="el-icon-loading"
    element-loading-background="rgba(0, 0, 0, 0.8)"
  >
    <el-carousel
      class="pmd"
      arrow="always"
    >
      <el-carousel-item
        v-for="item in carousel_text"
        :key="item"
      >
        <h3>{{ item }}</h3>
      </el-carousel-item>
    </el-carousel>
    <el-main>
      <el-row>
        <el-col :sm="{span:20,offset:2}">
          <el-row :gutter="20">
            <el-col :sm="6">
              <el-card
                class="card-body"
                :body-style="{ padding: '0px' }"
              >
                <img
                  src="../../static/images/multi-theme.png"
                  class="image"
                >
                <div>
                  <span>米表多语言、多主题</span>
                </div>
              </el-card>
            </el-col>
            <el-col :sm="6">
              <el-card
                class="card-body"
                :body-style="{ padding: '0px' }"
              >
                <img
                  src="../../static/images/manage.png"
                  class="image"
                >
                <div>
                  <span>完善的管理系统</span>
                </div>
              </el-card>
            </el-col>
            <el-col :sm="6">
              <el-card
                class="card-body"
                :body-style="{ padding: '0px' }"
              >
                <img
                  src="../../static/images/https.png"
                  class="image"
                >
                <div>
                  <span>自动签发HTTPS</span>
                </div>
              </el-card>
            </el-col>
            <el-col :sm="6">
              <el-card
                class="card-body"
                :body-style="{ padding: '0px' }"
              >
                <img
                  height="160px"
                  width="100%"
                  src="../../static/images/parking.png"
                  class="image"
                >
                <div>
                  <span>专业域名停放</span>
                </div>
              </el-card>
            </el-col>
          </el-row>
          <el-row>
            <el-col
              :sm="{span:14,offset:5}"
              style="margin-top:20px;margin-bottom:20px"
            >
              <el-table :data="tableData">
                <el-table-column
                  prop="date"
                  label="级别"
                >
                </el-table-column>
                <el-table-column
                  prop="name"
                  label="黄金会员"
                >
                </el-table-column>
                <el-table-column
                  prop="address"
                  label="超级会员"
                >
                </el-table-column>
              </el-table>
            </el-col>
          </el-row>
          <p
            class="plain-title"
            style="text-align:center;"
          >
            <span style="background-color:white;padding-left:10px;padding-right:10px;">合作伙伴</span>
          </p>
          <hr style="z-index:-1;margin-top:-12px;height:1px;border:none;border-top:1px dashed #0066CC;">
          <p style="text-align:center">
            <a
              class="friend-link"
              href="http://www.cg"
              title="草根域名"
              target="_black"
              rel="nofollow"
            >
              <img
                width="200"
                height="53"
                src="https://www.riluo.cn/static/offical/images/wwwcg.png"
                alt="草根域名"
              >
            </a>
            <a
              class="friend-link"
              href="http://name.tg"
              title="域名特工"
              target="_black"
              rel="nofollow"
            >
              <img
                width="200"
                height="53"
                src="https://www.riluo.cn/static/offical/images/nametg.png"
                alt="域名特工"
              >
            </a>
            <a
              class="friend-link"
              href="https://mi.riluo.cn"
              title="奶爸域名"
              target="_black"
            >
              <img
                width="200"
                height="53"
                src="https://www.riluo.cn/upload/logo/1-logo.png"
                alt="奶爸域名"
              >
            </a>
            <a
              class="friend-link"
              href="https://www.idc.ee/"
              title="雄狮主机"
              target="_black"
              rel="nofollow"
            >
              <img
                width="200"
                height="53"
                src="https://hk.cdnassets.com/ui/resellerdata/480000_509999/494274/supersite2/supersite/themes/MinimalGreen-MyTheme/images/logo.gif"
                alt="哪煮米域名比价"
              >
            </a>
          </p>
        </el-col>
      </el-row>
    </el-main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      carousel_text: ["专业域名停放", "省心资产管理", "高端投资交流"],
      logining: false,
      tableData: [
        {
          date: "域名停放",
          name: "❌",
          address: "✅"
        },
        {
          date: "米表HTTPS",
          name: "❌",
          address: "✅"
        },
        {
          date: "域名数限制",
          name: "100",
          address: "1000"
        },
        {
          date: "米表数",
          name: "1",
          address: "5"
        },
        {
          date: "可用主题",
          name: "官方默认",
          address: "所有主题"
        },
        {
          date: "价格",
          name: "5 元/月",
          address: "10 元/月"
        }
      ]
    };
  },
  mounted() {
    var code = this.getParameterByName("code");
    var state = this.getParameterByName("state");
    if (code && state && !this.$store.state.user) {
      this.logining = true;
      this.$http
        .get(
          "https://" +
            document.location.host +
            "/hack/oauth2-callback?code=" +
            code +
            "&state=" +
            state
        )
        .then(res => {
          this.$message.success("登录成功！");
          this.$store.commit("SET_USER", res.data);
          window.location.href = "/";
        })
        .catch(() => {
          this.logining = false;
        });
    }
  },
  methods: {
    getParameterByName: function(name, url) {
      if (!url) url = window.location.href;
      name = name.replace(/[\[\]]/g, "\\$&");
      var regex = new RegExp("[?&]" + name + "(=([^&#]*)|&|#|$)"),
        results = regex.exec(url);
      if (!results) return null;
      if (!results[2]) return "";
      return decodeURIComponent(results[2].replace(/\+/g, " "));
    }
  }
};
</script>

<style lang="less" scoped>
.pmd {
  background-color: #f6f9fc;
  text-align: center;
  .el-carousel__item {
    line-height: 300px;
    h3 {
      font-size: 100px;
      display: inline-block;
      vertical-align: middle;
      line-height: normal;
      @media (max-width: 768px) {
        font-size: 50px;
      }
    }
  }
}
.card-body img {
  height: 160px;
  width: 100%;
  @media (max-width: 768px) {
    height: 230px;
  }
}
.card-body div {
  background-color: #f6f9fc;
  padding: 14px;
  text-align: center;
}
.friend-link img {
  margin: 20px;
  border: #eeeeee 1px solid;
  @media (max-width: 768px) {
    margin-top: 10px;
    margin-bottom: 0px;
    width: 37%;
  }
}
@media (max-width: 768px) {
  .card-body {
    margin-bottom: 20px;
  }
}
</style>