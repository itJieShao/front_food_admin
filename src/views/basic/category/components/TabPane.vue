<template>
  <div>
    <el-row :gutter="20" style="margin-bottom: 20px">
      <el-col :span="8">
        <el-input
          v-model="listData.value"
          placeholder="请输入标签名称搜索"
        ></el-input>
      </el-col>
      <el-col :span="4">
        <el-button @click="searchBtn" type="primary" icon="el-icon-search"
          >搜索</el-button
        >
      </el-col>
      <el-col :span="12" style="display: flex; justify-content: flex-end">
        <el-button type="success" icon="el-icon-plus" @click="openDialog()"
          >新增标签</el-button
        >
      </el-col>
    </el-row>
    <el-table v-loading="loading" :data="list" style="width: 100%">
      <el-table-column width="50" align="center" label="顺序">
        <template slot-scope="scope">
          <span>{{ scope.$index + 1 }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="标签名称">
        <template slot-scope="scope">
          <span>{{ scope.row.name }}</span>
        </template>
      </el-table-column>
      <el-table-column width="180" fixed="right" align="center" label="操作">
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="openDialog(scope.row.id, scope.row.name, scope.$index)"
            >修改</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="listData.page"
      :limit.sync="listData.page_size"
      @pagination="getList"
    />

    <el-dialog
      :title="id ? '修改标签' : '新增标签'"
      :visible.sync="dialogFormVisible"
    >
      <el-form label-width="100px">
        <el-form-item label="标签名称">
          <el-input v-model="name" placeholder="请输入标签名称"></el-input>
        </el-form-item>
        <div style="display: flex; justify-content: flex-end">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="updateCategory">确 定</el-button>
        </div>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import {
  getUnitList,
  saveUnit,
  getLabelList,
  saveLabel,
} from "@/api/basic/category";
import Pagination from "@/components/Pagination";
export default {
  props: ["type"],
  components: { Pagination },
  data() {
    return {
      list: [],
      listData: {
        page: 1,
        page_size: 10,
        value: "",
        //type: this.type,
      },
      loading: false,
      total: 0,
      dialogFormVisible: false,
      dialogFormTitle: "",
      id: "",
      name: "",
      editIndex: 0,
    };
  },
  created() {
    this.getList();
  },
  methods: {
    openDialog(id, name, index) {
      this.editIndex = index;
      this.id = id || "";
      this.name = name;
      this.dialogFormVisible = true;
    },
    updateCategory() {
      let aData = {
        id: this.id,
        //type: this.type,
        name: this.name,
      };
      let api = this.type == 1 ? saveLabel : saveUnit
      api(aData).then((res) => {
        if (res) {
          this.$notify({
            title: "成功",
            message: "保存成功",
            type: "success",
            duration: 1000,
            onClose: () => {
              this.dialogFormVisible = false;
              if (aData.id) {
                this.list[this.editIndex].name = aData.name;
              } else {
                this.getList();
              }
            },
          });
        }
      });
    },
    getList() {
      this.loading = true;
      let api = this.type == 1 ? getLabelList : getUnitList
      api(this.listData).then((res) => {
        this.total = res.count;
        this.list = res.list;
        this.loading = false;
      });
    },
    searchBtn() {
      this.listData.page = 1;
      this.getList();
    },
  },
};
</script>

