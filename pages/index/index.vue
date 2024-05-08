<template>
	<div class="admin-container">
		<!-- 侧边栏 -->
		<div class="sidebar">
			<ul>
				<li :class="{ active: activeItem === 'user' }" @click="changeTab('user')">用户管理</li>
				<li :class="{ active: activeItem === 'lecture' }" @click="changeTab('lecture')">讲座管理</li>
				<li :class="{ active: activeItem === 'booking' }" @click="changeTab('booking')">预约管理</li>
				<li :class="{ active: activeItem === 'manage' }" @click="changeTab('manage')">管理员</li>
				<!-- <li :class="{ active: activeItem === 'image' }" @click="changeTab('image')">首页图</li> -->
			</ul>
		</div>

		<!-- 主内容 -->
		<div class="content">
			<!-- 用户栏 -->
			<div v-if="activeItem === 'user'">
				<div class="add-button" @click="showAddUserDialog = !showAddUserDialog">添加</div>
				<!-- 弹框部分 -->
				<!-- 添加用户 -->
				<div v-if="showAddUserDialog" class="dialog">
					<div class="dialog-content">
						<input type="text" v-model="addUsers.student_id" placeholder="学号">
						<input type="password" v-model="addUsers.password" placeholder="密码">
						<div class="button-group">
							<button @click="addUser">确认</button>
							<button @click="showAddUserDialog = false">取消</button>
						</div>
					</div>
				</div>
				<!-- 编辑用户 -->
				<div v-if="showEditUserDialog" class="dialog">
					<div class="dialog-content">
						<input type="text" v-model="users[editIndexId].student_id" placeholder="用户名">
						<input type="text" v-model="users[editIndexId].user_name" placeholder="用户名">
						<input type="password" v-model="users[editIndexId].password" placeholder="密码">
						<div class="button-group">
							<button @click="editUser(editIndexId)">确认</button>
							<button @click="showEditUserDialog = false">取消</button>
						</div>
					</div>
				</div>
				<!-- 删除用户 -->
				<div v-if="showDelUserDialog" class="dialog">
					<div class="dialog-content">
						<div class="deltext">
							<text>确认删除嘛？</text>
						</div>

						<div class="button-group">
							<button @click="delUser(delIndexId)">确认</button>
							<button @click="showDelUserDialog = false">取消</button>
						</div>
					</div>
				</div>
				<!-- 表格部分 -->
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
							<th>编辑</th>
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
							<td>
								<!-- 编辑按钮 -->
								<div class="action-button edit-button"
									@click="showEditUserDialog = !showEditUserDialog; editIndexId = index">编辑</div>
								<!-- 删除按钮 -->
								<div class="action-button delete-button"
									@click="showDelUserDialog = !showDelUserDialog; delIndexId = user.user_id">删除</div>
							</td>
						</tr>
					</tbody>
				</table>
			</div>
			<!-- 讲座栏 -->
			<div v-else-if="activeItem === 'lecture'">
				<!-- 弹框部分 -->
				<!-- 编辑讲座 -->
				<div v-if="showEditLectureDialog" class="dialog">
					<div class="dialog-content">
						<text>讲座名</text>
						<input type="text" v-model="lectures[editIndexId].lecture_name" placeholder="讲座名">
						<text>介绍</text>
						<input type="text" v-model="lectures[editIndexId].lecture_introduction" placeholder="介绍">
						<text>公告</text>
						<input type="text" v-model="lectures[editIndexId].lecture_announcement" placeholder="公告">
						<text>学院</text>
						<input type="text" v-model="lectures[editIndexId].college" placeholder="学院">
						<text>浏览数</text>
						<input type="text" v-model="lectures[editIndexId].viewed" placeholder="浏览数">
						<div class="button-group">
							<button @click="editLecture(editIndexId)">确认</button>
							<button @click="showEditLectureDialog = false">取消</button>
						</div>
					</div>
				</div>
				<!-- 删除讲座 -->
				<div v-if="showDelLectureDialog" class="dialog">
					<div class="dialog-content">
						<div class="deltext">
							<text>确认删除嘛？</text>
						</div>

						<div class="button-group">
							<button @click="delLecture(delIndexId)">确认</button>
							<button @click="showDelLectureDialog = false">取消</button>
						</div>
					</div>
				</div>
				<!-- 表格部分 -->
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
							<th>发布学生id</th>
							<th>图片</th>
							<th>编辑</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="(lecture, index) in lectures" :key="index">
							<td>{{ lecture.lecture_name }}</td>
							<td>{{ lecture.lecture_time.replace('T', ' ').replace('.000Z', '') }}</td>
							<td>{{ lecture.location }}</td>
							<td>{{ lecture.lecture_introduction }}</td>
							<td>{{ lecture.lecture_announcement }}</td>
							<td>{{ lecture.viewed }}</td>
							<td>{{ lecture.college }}</td>
							<td>{{ lecture.student_id }}</td>
							<td><img :src="lecture.lecture_image_url" alt="讲座图片" :title="lecture.lecture_image_url" />
							</td>
							<td>
								<!-- 编辑按钮 -->
								<div class="action-button edit-button"
									@click="showEditLectureDialog = !showEditLectureDialog; editIndexId = index">编辑
								</div>
								<!-- 删除按钮 -->
								<div class="action-button delete-button"
									@click="delIndexId = lecture.lecture_id;showDelLectureDialog = !showDelLectureDialog">
									删除</div>

							</td>
						</tr>
					</tbody>
				</table>
			</div>
			<!-- 预约栏 -->
			<div v-else-if="activeItem === 'booking'">
				<div class="add-button" @click="showAddBookingDialog = !showAddBookingDialog">添加</div>
				<!-- 弹框部分 -->
				<!-- 添加预约 -->
				<div v-if="showAddBookingDialog" class="dialog">
					<div class="dialog-content">
						<input type="text" v-model="addBookings.lecture_id" placeholder="讲座id">
						<input type="password" v-model="addBookings.student_id" placeholder="学生学号">
						<div class="button-group">
							<button @click="addBooking">确认</button>
							<button @click="showAddBookingDialog = false">取消</button>
						</div>
					</div>
				</div>
				<!-- 删除预约 -->
				<div v-if="showDelBookingDialog" class="dialog">
					<div class="dialog-content">
						<div class="deltext">
							<text>确认删除嘛？</text>
						</div>

						<div class="button-group">
							<button @click="delBooking(delIndexId)">确认</button>
							<button @click="showDelBookingDialog = false">取消</button>
						</div>
					</div>
				</div>
				<table class="alltable">
					<!-- 预约管理表格内容 -->
					<thead>
						<tr>
							<th>讲座id</th>
							<th>参加学生学号</th>
							<th>状态</th>
							<th>编辑</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="(booking, index) in bookings" :key="index">
							<td>{{ booking.lecture_id }}</td>
							<td>{{ booking.student_id }}</td>
							<td>
								<div class="action-button"
									:class="{'green-border': booking.join_status == 0, 'red-border': booking.join_status == 1} ">
									{{ booking.join_status === 0 ? '未参加' : '已参加' }}
								</div>
							</td>
							<td>
								<!-- 删除按钮 -->
								<div class="action-button delete-button"
									@click="showDelBookingDialog = !showDelBookingDialog; delIndexId = booking.booking_id">
									删除</div>
							</td>
						</tr>
					</tbody>
				</table>
			</div>
			<!-- 管理员栏 -->
			<div v-else-if="activeItem === 'manage'">
				<div class="add-button" @click="showAddManageDialog = !showAddManageDialog">添加</div>
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
								<div class="action-button delete-button" @click="delManage(manageItem.manage_id)">删除
								</div>

							</td>
						</tr>
					</tbody>
				</table>
			</div>
			<!-- 首页轮播图 -->
			<div v-else-if="activeItem === 'image'">
				<div class="add-button" @click="showAddImageDialog = !showAddImageDialog">添加</div>
				<!-- 弹框部分 -->
				<!-- 添加照片 -->
				<div v-if="showAddImageDialog" class="dialog">
					<div class="dialog-content">
						<text class="add-button" @click="UploadImage"> 选择图片上传</text>
						<input type="text" placeholder="http://xxx.com">
						<input type="text" placeholder="http://delxxx.com">
						<image :src="showImage" mode="aspectFit"></image>
						<div class="button-group">
							<button @click="addImage">确认</button>
							<button @click="showAddImageDialog = false">取消</button>
						</div>
					</div>
				</div>
				<table>
					<!-- 预约管理表格内容 -->
				</table>
			</div>

		</div>
	</div>
</template>

<script setup>
	import {
		ref,
		onMounted
	} from 'vue';
	import axios from 'axios';

	const activeItem = ref('user'); // 默认选中用户管理
	const lectures = ref([]); // 存放讲座信息的数组
	const users = ref([]); // 存放用户信息的数组
	const manage = ref([]); // 存放管理员的数组
	const bookings = ref([]); // 存放预约信息的数组
	const images = ref([]); // 存放首页轮播图的数组
	const uploadImage = ref({})
	const showImage = ref('')
	const addUsers = ref({});
	const manageName = ref('')
	const managePassword = ref('')
	const addBookings = ref({});

	const editIndexId = ref('')
	const delIndexId = ref('')

	const showAddUserDialog = ref(false)
	const showAddManageDialog = ref(false)
	const showAddImageDialog = ref(false)
	const showAddBookingDialog = ref(false)
	const showEditUserDialog = ref(false)
	const showEditManageDialog = ref(false)
	const showEditLectureDialog = ref(false)
	const showDelUserDialog = ref(false)
	const showDelLectureDialog = ref(false)
	const showDelBookingDialog = ref(false)
	const showDelImageDialog = ref(false)

	// 函数：切换选项卡
	const changeTab = (tabName) => {
		activeItem.value = tabName;
		if (tabName === 'user') {
			// 发送请求获取讲座信息
			fetchUsers();
		} else if (tabName === 'lecture') {
			// 发送请求获取讲座信息
			fetchLectures();
		} else if (tabName === 'booking') {
			// 发送请求获取预约信息
			fetchBookings();
		} else if (tabName === 'manage') {
			// 发送请求获取管理员信息
			fetchmanage();
		} else if (tabName === 'image') {
			// 发送请求获取图片信息
			fetchImages();
		}
	};

	function addUser() {
		let data = addUsers.value
		console.log('添加的user数据-vue', data)
		axios.post('http://localhost:8080/userEdit/add', {
				data
			})
			.then(response => {
				console.log('Response:', response.data);
				fetchUsers()
			})
			.catch(error => {
				console.log('error:', error);
				fetchUsers()
			})
		// 重置输入框
		addUsers.value = {};
		showAddUserDialog.value = false;
		// 更新数据
	}

	function editUser(id) {
		let data = users.value[id];
		console.log('修改的data:', data);
		// 发送请求修改讲座
		axios.post('http://localhost:8080/userEdit/edit', {
				data
			})
			.then(response => {
				console.log('edit response:', response.data);
				// 修改成功后更新
				fetchUsers();
			})
			.catch(error => {
				console.error('edit error:', error);
				fetchUsers();
			});
		// 修改成功与否都更新
		showEditUserDialog.value = false
	}

	function delUser(id) {
		// 发送请求删除用户
		axios.post('http://localhost:8080/userEdit/del', {
				id
			})
			.then(response => {
				console.log('Delete response:', response.data);
				// 删除成功后从前端移除该用户
				fetchUsers();
			})
			.catch(error => {
				console.error('Delete error:', error);
				fetchUsers();
			});
		// 删除成功与否都更新
		showDelUserDialog.value = false;
	}

	function editLecture(id) {
		let data = lectures.value[id];
		console.log('修改的data:', data);
		// 发送请求修改讲座
		axios.post('http://localhost:8080/lecturesInfoSend/edit', {
				data
			})
			.then(response => {
				console.log('edit response:', response.data);
				// 修改成功后更新
				fetchUsers();
			})
			.catch(error => {
				console.error('edit error:', error);
				fetchUsers();
			});
		// 修改成功与否都更新
		showEditLectureDialog.value = false
	}

	function delLecture(id) {
		// 发送请求删除用户
		axios.post('http://localhost:8080/lecturesInfoSend/del', {
				id
			})
			.then(response => {
				console.log('Delete response:', response.data);
				// 删除成功后从前端移除该用户
				fetchLectures();
			})
			.catch(error => {
				console.error('Delete error:', error);
				fetchLectures();
			});
		// 删除成功与否都更新
		showDelLectureDialog.value = false;
	}

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
		axios.post('http://localhost:8080/manageEdit/edit', {
				data
			})
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
		console.log('删除的管理员id', id);
		// 发送请求删除管理员
		axios.post('http://localhost:8080/manageEdit/del', {
				id
			})
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

	function addImage() {
		let data = uploadImage;
		console.log('要添加的images:', data);
		// 发送请求修改讲座
		axios.post('http://localhost:8080/imageInfo/add', {
				data
			})
			.then(response => {
				console.log('edit response:', response.data);
				// 修改成功后更新
				fetchUsers();
			})
			.catch(error => {
				console.error('edit error:', error);
				fetchUsers();
			});
		// 修改成功与否都更新
		showAddImageDialog.value = false
	}

	// 选择图片并上传
	function UploadImage() {
		uni.chooseImage({
			success: chooseImageRes => {
				const tempFilePaths = chooseImageRes.tempFilePaths;
				uni.uploadFile({
					url: 'http://127.0.0.1:8080/uploadImage', // 上传图片单独端口
					filePath: tempFilePaths[0],
					name: 'file',
					success: uploadFileRes => {
						console.log("上传结果", uploadFileRes);
						// 解析传回来的json
						let data = JSON.parse(uploadFileRes.data);
						// console.log("上传结果", data);

						if (data.status) {
							// 如果上传成功，则保存图片URL到lectureImage变量
							uploadImage.lectureImage = data.data.links.url; // 获取图片URL并赋值
							uploadImage.DellectureImage = data.data.links.delete_url
							showImage.value = uploadImage.lectureImage
							console.log("图片URL:", showImage.value); // 输出图片URL，以便调试
						} else {
							// 这里处理上传失败的情况-弹窗
							uni.showModal({
								title: '上传失败',
								content: data.message,
								showCancel: false
							});
						}
					},
					fail: uploadFileErr => {
						// let data = JSON.parse(uploadFileErr.data);
						// 这里处理网络错误或其他错误的情况
						console.error("上传失败", uploadFileErr);
						uni.showModal({
							title: '上传失败',
							content: '无法连接到服务器',
							showCancel: false
						});
					}
				});
			}
		});
	}


	function addBooking() {
		let data = addBookings.value
		console.log('添加的addBookings数据-vue', data)
		axios.post('http://localhost:8080/bookInfo/add', {
				data
			})
			.then(response => {
				console.log('Response:', response.data);
				fetchBookings()
			})
			.catch(error => {
				console.log('error:', error);
				fetchBookings()
			})
		// 重置输入框
		addBookings.value = {};
		showAddBookingDialog.value = false;
		// 更新数据
	}

	function delBooking(id) {
		console.log('删除的管理员id', id);
		axios.post('http://localhost:8080/bookInfo/del', {
				id
			})
			.then(response => {
				console.log('Delete response:', response.data);
				fetchBookings();
			})
			.catch(error => {
				console.error('Delete error:', error);
				fetchBookings();
			});
		showDelBookingDialog.value = false;
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

	function fetchBookings() {
		// 在组件挂载后，发送 axios 请求获取用户信息
		axios.get('http://127.0.0.1:8080/bookInfo/all')
			.then(response => {
				bookings.value = response.data; // 将获取到的用户信息存储在 users 数组中
				console.log('bookInfo', bookings.value)
			})
			.catch(error => {
				console.error('Failed to fetch bookings information:', error);
			});
	}

	function fetchImages() {
		// 在组件挂载后，发送 axios 请求获取用户信息
		axios.get('http://127.0.0.1:8080/imageInfo')
			.then(response => {
				images.value = response.data; // 将获取到的用户信息存储在 users 数组中
				console.log('imagesInfo', images.value)
			})
			.catch(error => {
				console.error('Failed to fetch imagesInfo information:', error);
			});
	}

	// 组件挂载后立即获取讲座信息
	onMounted(() => {
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

		.deltext {
			margin-bottom: 30px;
			width: 200px;
			display: flex;
			justify-content: center;
			align-items: center;
		}

		input {
			margin-bottom: 15px;
		}
	}

	.button-group {
		display: flex;
		justify-content: space-between;
		margin-top: 15px;

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

	.add-button {
		/* 将元素设置为块级元素 */
		display: flex;
		padding: 5px 10px;
		background-color: #4b9600;
		/* 浅绿色 */
		color: white;
		/* text-align: center; */
		align-items: center;
		justify-content: center;
		/* 垂直居中 */
		/* line-height: 100px; */
		border-radius: 10px;
		/* 圆角 */
		cursor: pointer;
		margin: 10px 150px;
		/* 将元素推到最右边 */
		/* margin-right: auto; */
	}

	.red-border {
		/* border: 1px solid #ffcccc; */
		background-color: #ffcccc;
		/* color: #FFFFFF; */
	}

	.green-border {
		/* border: 1px solid #99ff99; */
		background-color: #9a99a7;
		/* color: #FFFFFF; */
	}
</style>