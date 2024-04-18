<template>
  <div class="admin-container">
    <div class="sidebar">
      <ul>
        <li :class="{ active: activeItem === 'user' }" @click="changeTab('user')">用户管理</li>
        <li :class="{ active: activeItem === 'lecture' }" @click="changeTab('lecture')">讲座管理</li>
        <li :class="{ active: activeItem === 'booking' }" @click="changeTab('booking')">预约管理</li>
        <li :class="{ active: activeItem === 'manage' }" @click="changeTab('manage')">管理员</li>
      </ul>
    </div>
    <div class="content">
      <div v-if="activeItem === 'user'">
        <table class="alltable">
          <thead>
            <tr>
              <th>用户名</th>
              <th>学号</th>
              <th>学院</th>
              <th>密码</th>
              <th>头像</th>
              <th>性别</th>
              <th>电话号码</th>
              <th>邮箱</th>
              <!-- 其他用户信息列 -->
            </tr>
          </thead>
          <tbody>
            <tr v-for="(user, index) in users" :key="index">
              <td>{{ user.user_name }}</td>
              <td>{{ user.student_id }}</td>
              <td>{{ user.college }}</td>
              <td>{{ user.password }}</td>
              <td><img :src="user.my_image_url" alt="用户头像" style="width: 50px; height: 50px;"></td>
              <td>{{ user.gender }}</td>
              <td>{{ user.phone_number }}</td>
              <td>{{ user.email }}</td>
              <!-- 其他用户信息列 -->
            </tr>
          </tbody>
        </table>
      </div>
      <div v-else-if="activeItem === 'lecture'">
        <table class="alltable">
          <thead>
            <tr>
              <th>讲座名称</th>
              <th>讲座时间</th>
              <th>地点</th>
              <th>介绍</th>
              <th>公告</th>
              <th>浏览量</th>
              <th>学院</th>
              <th>风格</th>
              <!-- 新添加的列 -->
              <th>图片</th>
              <!-- <th>删除图片URL</th> -->
              <!-- <th>纬度</th> -->
              <!-- <th>经度</th> -->
            </tr>
          </thead>
          <tbody>
            <tr v-for="(lecture, index) in lectures" :key="index">
              <td>{{ lecture.lecture_name }}</td>
              <td>{{ lecture.lecture_time }}</td>
              <td>{{ lecture.location }}</td>
              <td>{{ lecture.lecture_introduction }}</td>
              <td>{{ lecture.lecture_announcement }}</td>
              <td>{{ lecture.viewed }}</td>
              <td>{{ lecture.college }}</td>
              <td>{{ lecture.style }}</td>
              <!-- 新添加的列 -->
              <td><img :src="lecture.lecture_image_url" alt="讲座图片" :title="lecture.lecture_image_url" /></td>
              <!-- <td :style="{ maxWidth: '200px' }">{{ lecture.lecture_image_delurl }}</td> -->
              <!-- <td>{{ lecture.latitude }}</td> -->
              <!-- <td>{{ lecture.longitude }}</td> -->
            </tr>
          </tbody>
        </table>
      </div>
      <div v-else-if="activeItem === 'booking'">
        <table>
          <!-- 预约管理表格内容 -->
        </table>
      </div>

      <div v-else-if="activeItem === 'manage'">
        <div style="width: 100px; height: 100px;" @click="showAddManageDialog = !showAddManageDialog">添加</div>
        <!-- 弹框部分 -->
        <!-- 添加管理员 -->
        <div v-if="showAddManageDialog" class="dialog">
          <div class="dialog-content">
            <input type="text" v-model="manageName" placeholder="管理员用户名">
            <input type="password" v-model="managePassword" placeholder="密码">
            <div class="button-group">
              <button @click="addManage">确认</button>
              <button @click="showAddManageDialog = false">取消</button>
            </div>
          </div>
        </div>
        <!-- 编辑管理员 -->
        <div v-if="showEditManageDialog" class="dialog">
          <div class="dialog-content">
            <input type="text" v-model="manage[editIndexId].manage_name" placeholder="管理员用户名">
            <input type="password" v-model="manage[editIndexId].password" placeholder="密码">
            <div class="button-group">
              <button @click="editManage(editIndexId)">确认</button>
              <button @click="showEditManageDialog = false">取消</button>
            </div>
          </div>
        </div>
        <table class="alltable">
          <thead>
            <tr>
              <th>管理ID</th>
              <th>用户名</th>
              <th>插入时间</th>
              <th>密码</th>
              <th>编辑</th>
            </tr>
          </thead>
          <tbody>
            <!-- 显示 -->
            <tr v-for="(manageItem, index) in manage" :key="index">
              <td>{{ manageItem.manage_id }}</td>
              <td>{{ manageItem.manage_name }}</td>
              <td>{{ manageItem.insert_time.replace('T', ' ').replace('.000Z', '') }}</td>
              <td>{{ manageItem.password }}</td>
              <td>
                <!-- 编辑按钮 -->
                <div class="action-button edit-button"
                  @click="showEditManageDialog = !showEditManageDialog; editIndexId = index">编辑</div>
                <!-- 删除按钮 -->
                <div class="action-button delete-button" @click="delManage(manageItem.manage_id)">删除</div>

              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const activeItem = ref('user'); // 默认选中用户管理
const lectures = ref([]); // 存放讲座信息的数组
const users = ref([]); // 存放用户信息的数组
const manage = ref([]); // 存放管理员的数组
const manageName = ref('')
const managePassword = ref('')

const editIndexId = ref('')

const showAddManageDialog = ref(false)
const showEditManageDialog = ref(false)
// 函数：切换选项卡
const changeTab = (tabName) => {
  activeItem.value = tabName;
  if (tabName === 'user') {
    // 发送请求获取讲座信息
    fetchUsers();
  }
  else if (tabName === 'lecture') {
    // 发送请求获取讲座信息
    fetchLectures();
  } else if (tabName === 'booking') {
    // 发送请求获取预约信息
    fetchBookings();
  } else if (tabName === 'manage') {
    // 发送请求获取管理员信息
    fetchmanage();
  }

};

// 模拟删除操作
const deleteUser = (userId) => {
  // 这里应该调用后端API
  const index = users.value.findIndex(user => user.id === userId);
  if (index !== -1) users.value.splice(index, 1);
};

// 模拟编辑操作
const editUser = (userId) => {
  // 这里应该打开一个表单模态框，并进行编辑
  console.log('Editing user:', userId);
};

function addManage() {
  axios.post('http://localhost:8080/manageEdit/add', {
    manage_name: manageName.value,
    password: managePassword.value,
  })
    .then(response => {
      console.log('Response:', response.data);
      fetchmanage()
    })
    .catch(error => {
      console.log('error:', error);
      fetchmanage()
    })
  // 重置输入框
  manageName.value = '';
  managePassword.value = '';
  showAddManageDialog.value = false;
  // 更新数据
}
// 使用axios发送HTTP POST请求修改信息
function editManage(id) {
  let data = manage.value[id];
  console.log('修改的data:', data);
  // 发送请求修改管理员
  axios.post('http://localhost:8080/manageEdit/edit', { data })
    .then(response => {
      console.log('edit response:', response.data);
      // 修改成功后更新
      fetchmanage();
    })
    .catch(error => {
      console.error('Delete error:', error);
      fetchmanage();
    });
  // 修改成功与否都更新
  showEditManageDialog.value = false
}
// 使用axios发送HTTP POST请求删除信息
function delManage(id) {
  // 发送请求删除管理员
  axios.post('http://localhost:8080/manageEdit/del', { id })
    .then(response => {
      console.log('Delete response:', response.data);
      // 删除成功后从前端移除该管理员
      fetchmanage();
    })
    .catch(error => {
      console.error('Delete error:', error);
      fetchmanage();
    });
  // 删除成功与否都更新
}

// 使用axios发送HTTP GET请求获取信息
function fetchLectures() {
  axios.get('http://localhost:8080/lecturesInfo') // 后端接口地址
    .then(response => {
      lectures.value = response.data.result;
      console.log('lectures', lectures.value)
    })
    .catch(error => {
      console.error('Failed to fetch lectures:', error);
    });
}

function fetchUsers() {
  // 在组件挂载后，发送 axios 请求获取用户信息
  axios.get('http://127.0.0.1:8080/userInfo/all')
    .then(response => {
      users.value = response.data; // 将获取到的用户信息存储在 users 数组中
      console.log('users', users.value)
    })
    .catch(error => {
      console.error('Failed to fetch user information:', error);
    });
}

function fetchmanage() {
  axios.get('http://127.0.0.1:8080/manageInfo/all')
    .then(response => {
      manage.value = response.data;
      console.log('manage:', manage.value); // 打印响应数据
    })
    .catch(error => {
      console.error('Failed to fetch manage information::', error); // 打印错误信息
    })
}

onMounted(() => {
  // 组件挂载后立即获取讲座信息
  fetchUsers();
});
</script>


<style scoped>
.admin-container {
  display: flex;
  /* height: 100 vh; */
  min-height: 100vh;
  width: 100%;
  background-color: #eaeaea;
}

.sidebar {
  width: 10vmax;
  /* height: 100%; */
  background-color: #2c3e50;
  color: white;
  padding: 20px;
  padding-top: 44px;
  flex-grow: 1;
  /* 填充剩余空间，让内容区域占满剩余空间 */
}

.content {
  width: 90vmax;
  /* height: 100%; */
  max-height: 100vh;
  overflow-y: auto;
  /* 添加垂直滚动条 */
  background-color: #ecf0f1;
  padding: 20px;
  padding-top: 44px;
  /* height: 100em; */
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  padding: 10px;
  cursor: pointer;
  transition: background-color 0.3s;
  border-radius: 8px;
  /* 添加圆角 */
}

li:hover {
  background-color: #c3c3c3;
}

.active {
  background-color: #a2a2a2;
}

.alltable {
  /* 添加边框样式 */
  border-collapse: collapse;
  width: 100%;
}

.alltable th,
.alltable td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
  /* white-space: pre-wrap; */
  /* 控制文本是否换行 */
  overflow-wrap: break-word;
  max-width: 250rpx;
  user-select: text;
  /* 允许用户选择文本 */

}

.alltable th {
  background-color: #f2f2f2;
}

.alltable tr:nth-child(even) {
  background-color: #f2f2f2;
}

.alltable img {
  /* background-color: #f2f2f2; */
  /* width: 90em; */
  /* height: 90em; */
  overflow: hidden;
  width: 100%;
  height: 100%;
}

/* .dialog {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  padding: 20px;
  border: 1px solid #ccc;
} */
.dialog {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

.dialog-content {
  background-color: white;
  padding: 60px 80px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);

  input {
    margin-bottom: 15px;
  }
}

.button-group {
  display: flex;
  justify-content: space-between;

  button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
}

.button-group button:first-child {
  background-color: orange;
  color: white;
}

.button-group button:last-child {
  background-color: #ff5555;
  color: white;
}


/* 操作按钮样式 */
.action-button {
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 5px;
  display: inline-block;
  margin: 5px;
}

/* 编辑按钮样式 */
.edit-button {
  background-color: orange;
  color: white;
}

/* 删除按钮样式 */
.delete-button {
  background-color: red;
  color: white;
}
</style>
