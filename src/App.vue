<template>
  <div id="app">
    <div class="put">
      <!-- 上传数据对话框 -->
      <el-dialog title="上传数据" :visible.sync="putDialogVisible" width="50%" @close="putDialogClosed">
        <el-form label-width="70px" ref="putFormRef">
          <el-form-item label="name">
            <el-input v-model="name"></el-input>
          </el-form-item>
          <el-form-item label="energy">
            <el-input v-model="energy"></el-input>
          </el-form-item>
          <el-form-item label="ca">
            <el-input v-model="ca"></el-input>
          </el-form-item>
          <el-form-item label="p">
            <el-input v-model="p"></el-input>
          </el-form-item>
          <el-form-item label="upper_limit">
            <el-input v-model="upper_limit"></el-input>
          </el-form-item>
          <el-form-item label="lower_limit">
            <el-input v-model="lower_limit"></el-input>
          </el-form-item>
          <el-form-item label="price">
            <el-input v-model="price" prop="mobile"></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="putDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addInfo()">确 定</el-button>
        </span>
      </el-dialog>
    </div >
    <div class="mainbag">
    <div class="leftPart">
    <div class="title1" float="right">
  上传数据：
  </div>
    <div class="put2">
      <el-form v-model="dynamicValidateForm" ref="dynamicValidateForm" label-width="100px" class="demo-dynamic">
        <h3>需上传数据信息：</h3>

        <el-form-item v-if="this.dynamicValidateForm.info.length===0">
        </el-form-item>
        <el-form-item v-else>
          <table class="ta">
            <thead>
              <tr>
                <th>name</th>
                <th>energy</th>
                <th>ca</th>
                <th>p</th>
                <th>upper_limit</th>
                <th>lower_limit</th>
                <th>price</th>
                <th>#</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in dynamicValidateForm.info">
                <td>{{ item.name }}</td>
                <td>{{ item.energy }}</td>
                <td>{{ item.ca }}</td>
                <td>{{ item.p }}</td>
                <td>{{ item.upper_limit }}</td>
                <td>{{ item.lower_limit }}</td>
                <td>{{ item.price }}</td>
                <el-button @click="deleteinfo(index)">删除</el-button>
              </tr>
            </tbody>
          </table>
        </el-form-item>
        <el-form-item>
          <el-button @click="showputDialog()">添加数据</el-button>
          <el-button type="primary" @click="putUserInfo()">提交</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
  <div class="rightpart">
    <div class="title1">
    获取结果：</div>
    <div class="biaodan">
      <el-input v-model="inputData.energy" placeholder="请输入energy值" clearable>energy</el-input>
      <el-input v-model="inputData.ca" placeholder="请输入ca含量">ca</el-input>
      <el-input v-model="inputData.p" placeholder="请输入p含量">p</el-input>
      <el-button @click="getres">获取结果数据</el-button>
      <!-- <div>{{ inputData }}</div> -->
      <div class="result">
        <div v-if="result.minPrice === null">0</div>
        <div v-else>
          <div>
            <div>最佳比率：{{ result.optimalRatio }}</div>
          </div>
          <div>最低价钱:{{ result.minPrice }}</div>
        </div>
      </div>
    </div>
  </div>
  </div>
</div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
      // 上传数据
      name: '',
      energy: '',
      ca: '',
      p: '',
      upper_limit: '',
      lower_limit: '',
      price: '',
      // 控制编辑资料对话框的显示与隐藏
      putDialogVisible: false,
      dynamicValidateForm: {
        info: [],
      },
      result: { minPrice: null },
      // 获取结果
      inputData: {
        energy: '',
        ca: '',
        p: '',
      }
    }
  },
  methods: {
    showputDialog() {
      this.putDialogVisible = true;
    },
    putDialogClosed() {
      this.$refs.putFormRef.resetFields()
    },
    addInfo() {
      this.dynamicValidateForm.info.push({
        name: this.name,
        energy: this.energy,
        ca: this.ca,
        p: this.p,
        upper_limit: this.upper_limit,
        lower_limit: this.lower_limit,
        price: this.price
      })
    },
    deleteinfo(index) {
      this.dynamicValidateForm.info.pop(index)
    },
    async putUserInfo() {
      const res = await this.$http.post('/feed/getFeed', this.dynamicValidateForm.info)
      console.log(res)
      if (res.status !== 200) {
        return this.$message.error("上传数据失败")
      }
      // 关闭对话框
      this.editDialogVisible = false
      // 提示修改成功
      this.$message.success("上传数据成功")
    },
    async getres() {
      const result = await this.$http.get('/feed/getRes', {
        params: this.inputData
      })
      if (result.status !== 200) {
        return this.$message.error("获取数据失败")
      }
      const res = result.data;
      this.result = res.data
      console.log(this.result)
      console.log(this.result.minPrice)
      this.$message.success("获取数据成功")
    }
  }
}
</script>

<style scoped lang="less">
#app{
  background-image: url(./assets/imgs/main.jpg);
  background-repeat: no-repeat;
  background-size: 100%;
}
.leftPart{
  width:800px;
  padding-left: 0px;
  margin-top:20px;
  margin-left:20%;
  background-color: rgba(212, 217, 218,.8);
  border-radius: 30px;
  padding:10px;
  box-shadow:8px 8px 10px rgb(163, 164, 168);

}
.rightpart{
  width:54%;
  margin-left:300px;
  margin-top:90px;
  background-color: rgba(236, 233, 233,.8);
  border-radius: 30px;
  padding:10px;
  box-shadow:8px 8px 10px rgb(163, 164, 168);
}

.title1{
text-align:center;
margin-top:5px;
padding-top:10px;
height: 100px;
width: 700px;
border-radius:30px ;
padding-left: 100px;
font-size: 40px;


}
.biaodan{
  padding-top:20px;
  padding-left:10px;
  padding-right:10px;
}
</style>