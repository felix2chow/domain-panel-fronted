<template>
  <el-form :status-icon="true" ref="form" :rules="rules" :model="form" label-width="100px">
    <el-form-item label="标题·中" prop="name_cn">
      <el-input v-model="form.name_cn" placeholder="日落米表托管"></el-input>
    </el-form-item>
    <el-form-item label="标题·英" prop="name_en">
      <el-input v-model="form.name_en" placeholder="Runcuo Domains Management"></el-input>
    </el-form-item>
    <el-form-item label="米表主题" prop="theme">
      <el-select v-model="form.theme" placeholder="请选择">
        <el-option v-for="item in themes" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="报价页主题" prop="offer_theme">
      <el-select v-model="form.offer_theme" placeholder="请选择">
        <el-option v-for="item in offer_themes" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="域名" prop="domain">
      <el-input v-model="form.domain" placeholder="riluo.cn"></el-input>
    </el-form-item>
    <el-form-item label="简介·中" prop="desc_cn">
      <el-input type="textarea" :rows="3" v-model="form.desc_cn" placeholder="日落米表托管（riluo.cn）是由资深域名投资人奶爸创建，前身是09年上线的知名域名平台泡名网，后因业务整合，于2013年9月1号将泡名网业务全部转移到日落米表托管（riluo.cn）上。"></el-input>
    </el-form-item>
    <el-form-item label="简介·英" prop="desc_en">
      <el-input type="textarea" :rows="3" v-model="form.desc_en" placeholder="RiLuo.cn is created by a senior domain name investor, Naiba, who was formerly known as the well-known domain name platform in 2009. It will be bubble-named on September 1, 2013 due to business integration. All of the business was transferred to Runcheo's domain name asset management platform (riluo.cn)."></el-input>
    </el-form-item>
    <el-form-item v-if="isEdit">
      <img style="max-width:192px;max-height:48px" :src="'/upload/logo/'+panel.ID+'-logo.png'">
    </el-form-item>
    <el-form-item label="Logo·中" prop="logo_cn">
      <el-input id="logo_cn" type="file" accept="image/png" v-model="form.logo_cn"></el-input>
    </el-form-item>
    <el-form-item v-if="isEdit">
      <img style="max-width:192px;max-height:48px" :src="'/upload/logo/'+panel.ID+'-logo_en.png'">
    </el-form-item>
    <el-form-item label="Logo·英" prop="logo_en">
      <el-input id="logo_en" type="file" accept="image/png" v-model="form.logo_en"></el-input>
    </el-form-item>
    <el-form-item label="统计类型" prop="at">
      <el-select v-model="form.at" placeholder="请选择">
        <el-option v-for="item in ats" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="统计ID" prop="ga">
      <el-input v-model="form.ga" placeholder="XN-XXXXXXX"></el-input>
    </el-form-item>
    <el-form-item>
      <el-alert title="请将米表域名cname到 parking.riluo.cn." type="success" :closable="false">
      </el-alert>
    </el-form-item>
    <el-form-item>
      <el-button @click="onSubmit" type="primary">{{isEdit?"确认修改":"新建米表"}}</el-button>
      <el-button @click="reset">重置</el-button>
      <el-button v-if="isEdit" type="danger" @click="onDelete">删除</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import axios from "axios";
export default {
  props: {
    isEdit: Boolean,
    panel: Object
  },
  data() {
    var confimDomain = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入域名"));
      } else if (!value.match(/^[a-zA-Z0-9-]{1,61}(?:\.[a-zA-Z]{2,})+$/g)) {
        callback(new Error("域名格式不规范"));
      } else {
        callback();
      }
    };
    var checkFile = (rule, value, callback) => {
      if (value === "" && rule.required) {
        callback(new Error("请选择logo上传"));
      } else {
        var logo = document.getElementById(rule.field).files[0];
        if (logo) {
          if (logo.size > 1024 * 700) {
            callback(new Error("文件大小超过700k"));
          } else if (!logo.name.endsWith(".png")) {
            callback(new Error("只能是 png 文件"));
          } else {
            callback();
          }
        } else {
          callback();
        }
      }
    };
    return {
      themes: [], //主题列表
      offer_themes: [], //报价页主题列表
      ats: [], //米表统计类型
      form: {
        theme: this.panel ? this.panel.Theme : "",
        offer_theme: this.panel ? this.panel.OfferTheme : "",
        id: this.panel ? this.panel.ID : "",
        name_cn: this.panel ? this.panel.Name : "",
        name_en: this.panel ? this.panel.NameEn : "",
        domain: this.panel ? this.panel.Domain : "",
        desc_cn: this.panel ? this.panel.Desc : "",
        desc_en: this.panel ? this.panel.DescEn : "",
        logo_cn: "",
        logo_en: "",
        at: this.panel ? this.panel.AnalysisType : null,
        ga: this.panel ? this.panel.Analysis : ""
      },
      rules: {
        name_cn: [
          { required: true, message: "请输入中文米表名称" },
          { min: 1, max: 20, message: "长度在 1 到 20 个字符" }
        ],
        name_en: [
          { required: true, message: "请输入英文米表名称" },
          { min: 1, max: 40, message: "长度在 1 到 40 个字符" }
        ],
        theme: [{ required: true, message: "请选择主题" }],
        offer_theme: [{ required: true, message: "请选择报价页主题" }],
        domain: [{ validator: confimDomain, required: true }],
        desc_cn: [
          { required: true, message: "请输入中文米表介绍" },
          {
            min: 1,
            max: 255,
            message: "长度在 1 到 255 个字符"
          }
        ],
        desc_en: [
          { required: true, message: "请输入英文米表介绍" },
          {
            min: 1,
            max: 1000,
            message: "长度在 1 到 1000 个字符"
          }
        ],
        logo_cn: [{ validator: checkFile, required: !this.isEdit }],
        logo_en: [{ validator: checkFile, required: !this.isEdit }]
      }
    };
  },
  mounted() {
    axios
      .all([this.$http.get("themes"), this.$http.get("analysis_types")])
      .then(
        axios.spread((themes, analysis_types) => {
          Object.keys(themes.data.themes).forEach(item => {
            this.themes.push({ label: themes.data.themes[item], value: item });
          });
          Object.keys(themes.data.offer_themes).forEach(item => {
            this.offer_themes.push({
              label: themes.data.offer_themes[item],
              value: item
            });
          });
          Object.keys(analysis_types.data).forEach(item => {
            this.ats.push({ label: analysis_types.data[item], value: item });
          });
        })
      );
  },
  methods: {
    onDelete() {
      const nb = this;
      this.$confirm(nb.panel.Name + " " + nb.panel.Domain, "确认删除米表", {
        cancelButtonText: "取消",
        confirmButtonText: "确定",
        callback: action => {
          if (action == "confirm") {
            const loading = nb.$loading({
              lock: true,
              text: "正在删除",
              spinner: "el-icon-loading",
              background: "rgba(255, 255, 255, 0.7)"
            });
            nb.$http
              .delete("panel/" + nb.panel.ID)
              .then(function() {
                nb.$message.success("删除成功！");
                nb.$store.commit("REMOVE_PANEL", nb.panel.ID);
                nb.$router.push("/dashboard");
              })
              .catch(function(error) {
                console.log(error);
              })
              .then(function() {
                loading.close();
              });
          } else {
            nb.$message.info("取消删除" + nb.panel.Name);
          }
        }
      });
    },
    reset() {
      this.$refs.form.resetFields();
    },
    onSubmit() {
      const nb = this;
      const msg = this.isEdit ? "修改米表" : "新建米表";
      this.$refs.form.validate(valid => {
        if (valid) {
          const loading = this.$loading({
            lock: true,
            text: "正在" + msg,
            spinner: "el-icon-loading",
            background: "rgba(255, 255, 255, 0.7)"
          });
          //封包参数
          var data = new FormData();
          Object.keys(nb.form).forEach(function(key) {
            if (!key.startsWith("logo")) {
              data.append(key, nb.form[key]);
            }
          });
          data.append("logo_cn", document.getElementById("logo_cn").files[0]);
          data.append("logo_en", document.getElementById("logo_en").files[0]);
          (this.isEdit
            ? this.$http.put("panel", data, {
                headers: {
                  "Content-Type": "multipart/form-data"
                }
              })
            : this.$http.post("panel", data, {
                headers: {
                  "Content-Type": "multipart/form-data"
                }
              })
          )
            .then(function(response) {
              nb.$message.success(msg + "成功！");
              nb.$store.commit("SET_PANEL", response.data);
              nb.$router.push("/panel/" + response.data.ID + "/");
            })
            .catch(function(error) {
              console.log(error);
            })
            .then(function() {
              loading.close();
            });
        } else {
          this.$message.info("请您检查输入重新提交。");
          return false;
        }
      });
    }
  }
};
</script>

<style lang="less" scoped>
</style>
