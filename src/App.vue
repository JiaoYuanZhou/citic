
<template>
  <div class="box">
    <div class="card-wrapper">
      <el-card class="card">
        <template #header>
          <h3>普通上传</h3>
        </template>
        <div class="file-upload">
          <label for="fileInput" class="upload-label">选择单个文件：</label>
          <input id="fileInput" type="file" ref="fileInput" @change="handleFileChange" />
          <span v-if="selectedFile" class="file-name">{{ selectedFile.name }}</span>
          <el-button type="primary" @click="uploadFile">上传文件</el-button>
        </div>
      </el-card>
      <el-card class="card">
        <template #header>
          <h3>加密上传</h3>
        </template>
        <p><span style="color: red;">因财务文件可能涉及到保密信息，不能在网络上明文传输，拓展功能：故实现加密后传输功能，后端对其进行解密</span></p>
        <div class="file-upload">
          <label for="fileEncryptInput" class="upload-label">选择单个文件：</label>
          <input id="fileEncryptInput" type="file" ref="fileEncryptInput" @change="handleEncryptFileChange" />
          <span v-if="selectedFile" class="file-name">{{ selectedFile.name }}</span>
          <el-button type="primary" @click="uploadEncryptFile">上传加密文件</el-button>
        </div>
      </el-card>
      <el-card class="card">
        <template #header>
          <h3>同时上传多个文件</h3>
        </template>
        <p><span style="color: red;">因可能一次涉及到多个文件的传输，一次一次传比较麻烦，拓展功能：故实现同时上传多个文件功能</span></p>
        <div class="file-upload">
          <label class="upload-label">选择多个文件：</label>
          <input id="multiFileInput" type="file" ref="multiFileInput" multiple @change="handleMultiFileChange" />
          <span v-if="selectedMultiFiles.length > 0" class="file-name">
            {{ selectedMultiFiles.map(file => file.name).join(', ') }}
          </span>
          <el-button type="primary" @click="uploadMultiFiles">上传多个文件</el-button>
        </div>
      </el-card>
      <el-card class="card">
        <template #header>
          <h3>大文件分片上传</h3>
        </template>
        <p>
          <span style="color: red;">因大文件上传时间比较长，如果在上传的过程中出现了网络波动，那前面的都白传了，拓展功能：故实现分片上传，前端自动将文件分为多个1M大小的片，后端进行分片接收</span>
        </p>
        <div class="file-upload">
          <label class="upload-label">选择一个大文件进行分片上传：</label>
          <input type="file" ref="chunkFileInput" multiple @change="handleChunkFileChange" />
          <el-progress :percentage="uploadProgress" />
          <el-button type="primary" @click="uploadChunks">分片上传</el-button>
        </div>
      </el-card>
      <el-card class="card">
        <template #header>
          <h3>后台异步处理文件</h3>
        </template>
        <p><span style="color: red;">因文件的数据比较多，后台解析时间需要较长，很容易出现连接超时，拓展功能：故实现后台通过子线程异步文件处理，父线程立马返回</span></p>
        <div class="file-upload">
          <label for="fileSyncInput" class="upload-label">选择单个文件：</label>
          <input id="fileSyncInput" type="file" ref="fileSyncInput" @change="handleSyncFileChange" />
          <span v-if="selectedSyncFile" class="file-name">{{ selectedSyncFile.name }}</span>
          <el-button type="primary" @click="uploadSyncFile">后台异步文件处理</el-button>
        </div>
      </el-card>

    </div>
  </div>
  <div class="table">
    <h1>对应表格实时更新数据</h1>
    <el-button type="success" @click="addUserFn">新增一条数据</el-button>
        <el-table :data="tableData" style="width: 100%">
          <el-table-column prop="id" label="ID"></el-table-column>
          <el-table-column prop="name" label="姓名"></el-table-column>
          <el-table-column prop="age" label="年龄"></el-table-column>
          <el-table-column prop="highestEducation" label="最高学历"></el-table-column>
          <el-table-column prop="university" label="大学"></el-table-column>
          <el-table-column prop="major" label="专业"></el-table-column>
          <el-table-column prop="phone" label="手机号"></el-table-column>
          <el-table-column prop="email" label="邮箱"></el-table-column>
          <el-table-column prop="residenceAddress" label="常住地址"></el-table-column>
          <el-table-column prop="zipCode" label="邮编"></el-table-column>
          <el-table-column label="操作">
            <template #default="scope">
              <el-button type="primary">编辑</el-button>
              <el-button type="danger" @click="deleteUserFn(scope.row)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>

      <div class="example-pagination-block">
        <el-pagination layout="prev, pager, next" :total="totalItems" @current-change="handleCurrentChange"
          @size-change="handleSizeChange" />
      </div>
      </div>
      <el-dialog v-model="isShowModal" :title="user.id ? '编辑用户':'新增用户'" width="50%" @close="closeDialog">
          <el-form :model="user" ref="formEl" label-width="120px" :rules="rules">
            <el-form-item prop="name" label="姓名">
              <el-input v-model="user.name"></el-input>
            </el-form-item>
            <el-form-item prop="age" label="年龄">
              <el-input v-model="user.age"></el-input>
            </el-form-item>
            <el-form-item prop="highestEducation" label="最高学历">
              <el-input v-model="user.highestEducation"></el-input>
            </el-form-item>
            <el-form-item prop="university" label="大学">
              <el-input v-model="user.university"></el-input>
            </el-form-item>
            <el-form-item prop="major" label="专业">
              <el-input v-model="user.major"></el-input>
            </el-form-item>
            <el-form-item prop="phone" label="手机号">
              <el-input v-model="user.phone"></el-input>
            </el-form-item>
            <el-form-item prop="email" label="邮箱">
              <el-input v-model="user.email"></el-input>
            </el-form-item>
            <el-form-item prop="residenceAddress" label="常住地址">
              <el-input v-model="user.residenceAddress"></el-input>
            </el-form-item>
            <el-form-item prop="zipCode" label="邮编">
              <el-input v-model="user.zipCode"></el-input>
            </el-form-item>
          </el-form>
          <template #footer>
            <el-button @click="isShowModal = false">取消</el-button>
            <el-button type="success" @click="submitForm">提交</el-button>

          </template>
      </el-dialog>
</template>

<script>
import axios from 'axios';
import { ElMessage, ElMessageBox } from 'element-plus'
export default {
  data() {
    return {
      totalItems: 50,
      currentPage: 1,
      pageSize: 10,
      tableData: [],  // 存放后端返回的数据
      selectedMultiFiles: [], // 存放选择的多个文件
      selectedFile: null,
      selectedChunkFiles: null,
      uploadProgress: 0,
      selectedSyncFile: null,
      isShowModal:false,
      user:{
        name: ''
      },
      rules:{
        name: [{required: true,message:'请输入姓名',trigger:'change'}],
        age: [{required: true,message:'请输入年龄',trigger:'change'}],
        highestEducation: [{required: true,message:'请输入最高学历',trigger:'change'}],
        university: [{required: true,message:'请输入学校',trigger:'change'}],
        major: [{required: true,message:'请输入专业',trigger:'change'}],
        phone: [{required: true,message:'请输入电话',trigger:'change'}],
        email: [{required: true,message:'请输入邮箱',trigger:'change'}],
        residenceAddress: [{required: true,message:'请输入常住地址',trigger:'change'}],
        zipCode: [{required: true,message:'请输入邮编',trigger:'change'}]
      }
    };
  },
  mounted() {
    this.fetchData()
  },
  methods: {
    handleChunkFileChange() {
      this.selectedChunkFiles = this.$refs.chunkFileInput.files[0];
    },
    async uploadChunks() {
      this.uploadProgress = 0;
      const chunkSize = 1024 * 1024; // 1MB 分片大小
      const totalChunks = Math.ceil(this.selectedChunkFiles.size / chunkSize);

      // 遍历分片并逐片上传
      for (let index = 0; index < totalChunks; index++) {
        const start = index * chunkSize;
        const end = Math.min(start + chunkSize, this.selectedChunkFiles.size);
        const chunk = this.selectedChunkFiles.slice(start, end);

        const formData = new FormData();
        formData.append('chunk', chunk);
        formData.append('filename', this.selectedChunkFiles.name);
        formData.append('totalSize', this.selectedChunkFiles.size);
        formData.append('index', index);

        try {
          // 发送分片到服务器
          const response = await axios.post('http://120.53.92.176/person/uploadChunks', formData, {
            onUploadProgress: (progressEvent) => {
              const percentCompleted = Math.round((progressEvent.loaded * 100) / progressEvent.total);
              console.log('Upload Progress:', percentCompleted, '%');
              this.uploadProgress = (index / totalChunks) * 100; // 更新进度条
            },
          });

          console.log('Chunk', index, 'uploaded successfully:', response.data);
        } catch (error) {
          console.error('Error uploading chunk', index, ':', error);
          // 处理上传失败的情况，例如中断上传或重试
          break; // 如果需要中断上传
        }
      }

      console.log('All chunks uploaded successfully');
    },

    handleFileChange() {
      this.selectedFile = this.$refs.fileInput.files[0];
    },

    handleEncryptFileChange() {
      this.selectedFile = this.$refs.fileEncryptInput.files[0];
    },

    async uploadSyncFile() {
      if (!this.selectedSyncFile) {
        console.error("No file selected");
        return;
      }

      const formData = new FormData();
      formData.append("file", this.selectedSyncFile);

      // 使用axios或其他方式上传加密后的文件
      axios.post("http://120.53.92.176/person/syncImport", formData)
        .then(response => {
          if (response.status === 200) {
            // 请求成功
            console.log("File uploaded successfully:", response.data);
            ElMessage({
              type: 'success',
              message: response.data.msg,
            })
          } else {
            // 请求成功，但状态码不是 200，可能是 490
            console.error("File upload failed. Status Code:", response.status);
            if (response.status === 490) {
              console.error("Custom error message:", response.data.msg);
              ElMessage({
                type: 'error',
                message: response.data.msg,
              })
            }
          }
        })
        .catch(error => {
          // 请求失败
          console.error("File upload failed:", error.response.data);
          ElMessage({
            type: 'error',
            message: error.response.data,
          })
        });
      this.fetchData()
    },

    handleMultiFileChange() {
      // 使用refs获取选择的多个文件
      const newFiles = Array.from(this.$refs.multiFileInput.files);
      // 将新选择的文件与之前的文件合并
      this.selectedMultiFiles = this.selectedMultiFiles.concat(newFiles);
    },

    async uploadMultiFiles() {
      if (this.selectedMultiFiles.length === 0) {
        console.error("No files selected");
        return;
      }

      const formData = new FormData();

      this.selectedMultiFiles.forEach((file, index) => {
        formData.append(`files`, file);
      });

      // 使用axios或其他方式上传加密后的多个文件
      axios.post("http://120.53.92.176/person/importFiles", formData)
        .then(response => {
          ElMessage({
                type: 'success',
                message: response.data.msg,
              })
              this.fetchData()
        })
        .catch(error => {
          console.error("Files upload failed:", error);
        });
    },
    handleCurrentChange(newPage) {
      this.currentPage = newPage;
      this.fetchData(); // 在这里调用获取数据的方法
    },
    handleSizeChange(newSize) {
      this.pageSize = newSize;
      this.fetchData(); // 在这里调用获取数据的方法
    },
    fetchData() {
      axios.get("http://120.53.92.176/person/all", { params: { currentPage: this.currentPage, pageSize: this.pageSize } })
        .then(response => {
          // 处理后端返回的数据
          this.tableData = response.data.data.records;
          this.total = response.data.data.total;
          this.pageSize = response.data.data.size;
          this.currentPage = response.data.data.current;
        })
        .catch(error => {
          console.error("Failed to fetch data:", error);
        });
    },
    handleSyncFileChange() {
      this.selectedSyncFile = this.$refs.fileSyncInput.files[0];
    },
    async encryptFile(file) {
      const arrayBuffer = await file.arrayBuffer();
      const iv = crypto.getRandomValues(new Uint8Array(16)); // 初始化向量

      const key = await crypto.subtle.importKey("raw", new TextEncoder().encode("super_secret_key"), { name: "AES-GCM", length: 256 }, false, ["encrypt"]);
      const encryptedBuffer = await crypto.subtle.encrypt({ name: "AES-GCM", iv }, key, arrayBuffer);

      const encryptedFile = new Blob([iv, encryptedBuffer], { type: file.type });
      return encryptedFile;
    },

    async uploadFile() {
      if (!this.selectedFile) {
        console.error("No file selected");
        return;
      }
      const formData = new FormData();
      formData.append("file", this.selectedFile);

      // 使用axios或其他方式上传加密后的文件
      // 请替换以下代码为实际的上传逻辑
      axios.post("http://120.53.92.176/person/importData", formData)
        .then(response => {
          if (response.status === 200) {
            // 请求成功
            console.log("File uploaded successfully:", response.data);
            ElMessage({
              type: 'success',
              message: response.data.msg,
            })
          } else {
            // 请求成功，但状态码不是 200，可能是 490
            console.error("File upload failed. Status Code:", response.status);
            if (response.status === 490) {
              console.error("Custom error message:", response.data.msg);
              ElMessage({
                type: 'error',
                message: response.data.msg,
              })
            }
          }
          this.fetchData()
        })
        .catch(error => {
          // 请求失败
          console.error("File upload failed:", error.response.data);
          ElMessage({
            type: 'error',
            message: error.response.data,
          })
        });
      
    },
    async uploadEncryptFile() {
      if (!this.selectedFile) {
        console.error("No file selected");
        return;
      }

      const encryptedFile = await this.encryptFile(this.selectedFile);

      const formData = new FormData();
      formData.append("file", encryptedFile);

      // 使用axios或其他方式上传加密后的文件
      // 请替换以下代码为实际的上传逻辑
      axios.post("http://120.53.92.176/person/importEncryptData", formData)
        .then(response => {
          if (response.status === 200) {
            // 请求成功
            console.log("File uploaded successfully:", response.data);
            ElMessage({
              type: 'success',
              message: response.data.msg,
            })
          } else {
            // 请求成功，但状态码不是 200，可能是 490
            console.error("File upload failed. Status Code:", response.status);
            if (response.status === 490) {
              console.error("Custom error message:", response.data.msg);
              ElMessage({
                type: 'error',
                message: response.data.msg,
              })
            }
          }
        })
        .catch(error => {
          // 请求失败
          console.error("File upload failed:", error.response.data);
          ElMessage({
            type: 'error',
            message: error.response.data,
          })
        });
      this.fetchData()
    },
    async deleteUserFn(user){
      const id = user.id
      axios.delete(`http://120.53.92.176/person/${id}`).then(res=>ElMessage.success('删除成功'))
      this.fetchData()
    },
     addUserFn(){
      this.user = {}
      this.isShowModal = true
    },
    editUserFn(user){
      this.user = user
      this.isShowModal = true
      console.log('edit的user',user)
    },
    async submitForm(){
      console.log(this.user)
      this.$refs.formEl.validate((valid, fields) => {
        if (valid) {
          axios.post(`http://120.53.92.176/person/saveOrUpdate`,{
            ...this.user
          }).then(res=>{
            ElMessage.success('成功')
            this.isShowModal = false
          })
        } else {
          console.log('error submit!', fields)
        }
      })
    },
    closeDialog(){
      this.$refs.formEl.resetFields()
    }
  }
};
</script>

<style>
.file-upload {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.upload-label {
  margin-right: 10px;
}

.card-wrapper {
  display: flex;
  /* justify-content: ju; */
  /* align-items: center; */
  flex-wrap: wrap;

}

.box{
  padding: 2rem;
}

.card {
  width: 30%;
  margin: 10px;
}
.table{
  width: 100%;
  margin: 0 auto;
  padding: 2rem;
}
</style>



