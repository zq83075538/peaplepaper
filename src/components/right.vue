<template>
<el-container v-loading="loading">
  <el-collapse-transition>
    <div class="transition-box" style="position:relative;padding:10px;">

        <el-switch
          v-show="selection"
          v-model="numberType"
          active-color="#13ce66"
          inactive-color="#ff4949"
          active-text="数值0"
          inactive-text="数值1"
          @change="numberChange"
        >
        </el-switch>
        <el-button @click="refresh()" type="primary" v-show="updateNow"><i class="el-icon-refresh"></i> 立即更新</el-button>
        <el-button v-show="ids.length" @click="removeDialog()" type="danger"><i class="el-icon-delete"></i> 批量删除</el-button>
    </div>
  </el-collapse-transition>
  <el-main>
    <el-table :data="tableData" @selection-change="handleSelectionChange" border @row-click="rowClick">
      <el-table-column
      type="selection"
      width="40">
    </el-table-column>
      <template v-for="(o,idx) in showview[api.split('type=')[1]]||[]">
        <el-table-column :prop="o.prop" :label="o.label" :width="o.width" :key="idx">
        </el-table-column>
      </template>
      <el-table-column prop="" label="操作" width="120">
        <template slot-scope="scope">
          <el-button type="text" @click="editDialog(scope.row)">编辑</el-button>
          <el-button type="text" @click="removeDialog(scope.row)">删除</el-button>
        </template>
      </el-table-column>

    </el-table>

    <el-dialog title="编辑" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item v-for="(o,idx) in showview[api.split('type=')[1]]||[]" :label="o.label" :label-width="formLabelWidth" :key="idx">
          <el-input v-model="form[o.prop]" :readonly="o.readonly" auto-complete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="edit">确 定</el-button>
      </div>
    </el-dialog>

    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="20%">
      <span>确定要删除吗？</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="remove">确 定</el-button>
      </span>
    </el-dialog>

  </el-main>
  <el-footer v-show="totalPage / pageSize > 1">
    <div style="margin-top:20px">
      <el-pagination
        :small="true"
        layout="prev, pager, next"
        :total="totalPage"
        :page-size="pageSize"
        :current-page.sync="currentPage"
        @current-change="getList($route.params.api,arguments[0])">
      </el-pagination>
    </div>
  </el-footer>
</el-container>
</template>
<script>
import { API, rows } from "../config";
import { showview } from "./right_data";
const canupdateArr = [
  "portrait_media",
  "paper_level",
  "news_top",
  "news_paper",
  "web_article",
  "weibo_article",
  "wechat_article",
  "app_article",
  "content_real_time",
  "content_reach_monitor",
  "content_input_monitor",
  "media_position",
  "read_recommend_number",
  "recommend_rank",
  "media_30",
  "media_panel",
  "wx_rank",
  "wb_rank"
];
const dataSelection = ["read", "read_panel"];
export default {
  data() {
    return {
      tableData: [],
      dialogFormVisible: false,
      dialogVisible: false,
      form: {},
      formLabelWidth: "120px",
      showview,
      currentPage: this.$route.params.page - 0,
      totalPage: 1,
      pageSize: rows.rows,
      ids: [],
      table: "",
      loading: true,
      showbtn: false,
      numberType: -1,
      selection: false
    };
  },
  props: ["api"],
  methods: {
    rowClick(row, e, f) {
      let inType = [
        {
          type: "news_paper",
          src: "image"
        },
        {
          type: "media_position",
          src: "logo"
        },
        {
          type: "media_panel",
          src: "url"
        }
      ];
      let idxOfType = inType.map(o => o.type).indexOf(row.type);
      if (idxOfType === -1 || f.label === "操作") return;
      let src = row[inType[idxOfType]["src"]];
      let img = new Image();
      img.onload = () => {
        this.$alert(`<img src="${src}" style="width:100%;" />`, {
          dangerouslyUseHTMLString: true,
          closeOnClickModal: true,
          closeOnPressEscape: true
        });
      };
      img.onerror = () => {
        this.$message.error("图片链接失效");
      };
      img.src = src;
    },
    handleSelectionChange(val) {
      this.ids = val.map(o => o._id);
      this.table = val[0] ? val[0].table : "";
    },
    messageError(msg) {
      const h = this.$createElement;
      this.$message.error(msg);
    },
    getList(path, page) {
      this.loading = true;
      page &&
        this.$router.push({
          name: "appmain",
          params: { api: path, page: page }
        });
      let data = page
        ? { page, ...rows }
        : { ...rows, page: this.$route.params.page };
      let url = path ? API.host + path : API.host + this.$route.params.api;
      $.ajax({
        url,
        data,
        success: data => {
          this.loading = false;
          this.currentPage = data.msg.page;
          this.totalPage = data.msg.total;
          this.tableData = data.msg.list;
        },
        error: err => {
          this.loading = false;
          this.messageError("服务器繁忙，请稍后再试");
        }
      });
    },
    editDialog(row) {
      this.dialogFormVisible = true;
      this.form = row;
    },
    removeDialog(row) {
      if (row) {
        this.dialogVisible = true;
        this.form = row;
      } else {
        this.ids.length
          ? (this.dialogVisible = true)
          : this.messageError("请选择要删除的条目");
      }
    },
    edit() {
      $.ajax({
        type: "post",
        url: API.host + "save",
        data: this.form,
        success: res => {
          if (res.msg === "ok") {
            this.dialogFormVisible = false;
            this.$message({ message: "修改成功", type: "success" });
          } else {
            this.messageError(res.msg);
          }
        }
      });
    },
    remove() {
      let ids = this.form._id ? [this.form._id] : this.ids;
      let table = this.form.table || this.table;
      $.ajax({
        type: "post",
        url: API.host + "remove",
        data: JSON.stringify({
          ids,
          table
        }),
        success: res => {
          if (res.msg === "ok") {
            this.dialogVisible = false;
            this.$message({ message: "删除成功", type: "success" });
            this.getList();
          } else {
            this.messageError(res.msg);
          }
        }
      });
    },
    setUpdateBtn(path) {
      this.updateNow = canupdateArr.indexOf(path.split("type=")[1]) !== -1;
      this.selection = dataSelection.indexOf(path.split("type=")[1]) !== -1;
      let idx = dataSelection.indexOf(path.split("type=")[1]);
      if (idx !== -1) {
        let cat = ['content','local'][idx];
        $.ajax({
          url: API.host + "readinfo",
          data: {cat},
          success: res => {
            if (res.code === 200) {
              this.numberType = res.msg ? false : true;
            } else {
              this.$message({ message: "服务器异常，请稍后再试", type: "error" })
            }

          }
        });
      }
    },
    refresh() {
      $.ajax({
        type: "post",
        url: API.host + "fresh",
        data: {
          type: this.api.split("type=")[1]
        },
        success: res => {
          if (res.msg === "ok") {
            this.$message({ message: "数据更新成功", type: "success" });
            this.getList();
          } else {
            this.messageError(res.msg);
          }
        }
      });
    },
    numberChange(e) {
      let { api } = this.$route.params;
      let idx = dataSelection.indexOf(api.split("type=")[1]);
      let cat = ['content','local'][idx];
      $.post(API.host + 'changeread?type=' + (e ? 0 : 1) + '&cat=' + cat, res => {
        if(res.code !== 200){
          this.numberType = !this.numberType;
          this.$message({ message: "服务器异常，请稍后再试", type: "error" })
        }
      })
    }
  },
  watch: {
    api(n) {
      this.getList(n);
      this.setUpdateBtn(n);
      this.currentPage = 1;
    }
  },
  created() {
    let { api } = this.$route.params;
    this.getList(api);
    this.setUpdateBtn(api);
  }
};
</script>