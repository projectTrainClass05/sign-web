<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" :model="filters">
				<el-form-item>
					<el-input v-model="filters.name" placeholder="姓名"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" v-on:click="getUsers">查询</el-button>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" @click="handleAdd">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-table :data="users" highlight-current-row v-loading="listLoading" @selection-change="selsChange" style="width: 100%;">
			<!--<el-table-column type="selection" width="55">-->
			<!--</el-table-column>-->
			<!--<el-table-column type="index" width="60">-->
			<!--</el-table-column>-->
			<el-table-column prop="id" label="学号" width="120" sortable>
			</el-table-column>
			<el-table-column prop="name" label="姓名" width="120" sortable>
			</el-table-column>
			<el-table-column prop="sex" label="性别" width="100" :formatter="formatSex" sortable>
			</el-table-column>
			<el-table-column prop="age" label="年龄" width="100"  sortable>
			</el-table-column>
			<el-table-column prop="school.school" label="学校" width="130" sortable>
			</el-table-column>
			<el-table-column prop="college.collegename" label="学院" width="180" sortable>
			</el-table-column>
			<el-table-column prop="major.major" label="专业" min-width="180" sortable>
			</el-table-column>
			<el-table-column prop="role.name" label="身份" min-width="120" sortable>
			</el-table-column>
			<el-table-column label="操作" width="150">
				<template scope="scope">
					<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
					<el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--工具条-->
		<el-col :span="24" class="toolbar">
			<el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button>
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--编辑界面-->
		<el-dialog title="编辑" v-model="editFormVisible" :close-on-click-modal="false">
			<el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
				<el-form-item label="姓名" prop="name">
					<el-input v-model="editForm.name" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="性别">
					<el-radio-group v-model="editForm.sex">
						<el-radio class="radio" :label="1">男</el-radio>
						<el-radio class="radio" :label="0">女</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="年龄">
					<el-input-number v-model="editForm.age" :min="0" :max="200"></el-input-number>
				</el-form-item>
				<el-form-item label="学校">
					<!--<el-input-number v-model="editForm.age" :min="0" :max="200"></el-input-number>-->
					<el-select v-model="school" filterable placeholder="请选择">
						<el-option
								v-for="item in schools"
								:key="item.id"
								:label="item.school"
					:value="item.id">
					</el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="学院">
				<!--<el-input-number v-model="editForm.age" :min="0" :max="200"></el-input-number>-->
				<el-select v-model="college" filterable  placeholder="请选择">
					<el-option
							v-for="item in colleges"
							:key="item.id"
							:label="item.collegename"
							:value="item.id">
					</el-option>
				</el-select>
			</el-form-item>
				<el-form-item label="专业">
					<!--<el-input-number v-model="editForm.age" :min="0" :max="200"></el-input-number>-->
					<el-select v-model="major" filterable  placeholder="请选择">
						<el-option
								v-for="item in majors"
								:key="item.id"
								:label="item.major"
								:value="item.id">
						</el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="身份">
					<el-select v-model="role"  placeholder="请选择">
						<el-option
								v-for="item in roles"
								:key="item.id"
								:label="item.name"
								:value="item.id">
						</el-option>
					</el-select>
				</el-form-item>
				<!--<el-form-item label="地址">-->
					<!--<el-input type="textarea" v-model="editForm.addr"></el-input>-->
				<!--</el-form-item>-->
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click.native="editFormVisible = false" @click = "restore">取消</el-button>
				<el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
			</div>
		</el-dialog>

		<!--新增界面-->
		<el-dialog title="新增" v-model="addFormVisible" :close-on-click-modal="false">
			<el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
				<el-form-item label="姓名" prop="name">
					<el-input v-model="addForm.name" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="性别">
					<el-radio-group v-model="addForm.sex">
						<el-radio class="radio" :label="1">男</el-radio>
						<el-radio class="radio" :label="0">女</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="年龄">
					<el-input-number v-model="addForm.age" :min="0" :max="200"></el-input-number>
				</el-form-item>
				<el-form-item label="生日">
					<el-date-picker type="date" placeholder="选择日期" v-model="addForm.birth"></el-date-picker>
				</el-form-item>
				<el-form-item label="地址">
					<el-input type="textarea" v-model="addForm.addr"></el-input>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click.native="addFormVisible = false">取消</el-button>
				<el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
			</div>
		</el-dialog>
	</section>
</template>

<script>
	import util from '../../common/js/util'
	//import NProgress from 'nprogress'
	import { getUserListPage, removeUser, batchRemoveUser, editUser, addUser } from '../../api/api';
	import axios from 'axios';

	export default {
		data() {
			return {
				filters: {
					name: ''
				},
				users: [],
				total: 0,
                role:'',
				roles:[
					{
					    id:1,
						name:'学生'
					},{
					    id:2,
						name:'教师'
					},{
					    id:3,
						name:'管理员'
					}
				],
				school:'',
				oldschool:'',
				college:'',
				oldcollege:'',
				major:'',
				oldmajor:'',
				page: 1,
				listLoading: false,
				sels: [],//列表选中列

				editFormVisible: false,//编辑界面是否显示
				editLoading: false,
				editFormRules: {
					name: [
						{ required: true, message: '请输入姓名', trigger: 'blur' }
					]
				},
                schools:[],
				colleges:[],
				majors:[],
				//编辑界面数据
				editForm: {
                    id: '',
                    name: '',
                    school: '',
                    colloge: '',
                    major: '',
                    role: ''
				},

				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				addFormRules: {
					name: [
						{ required: true, message: '请输入姓名', trigger: 'blur' }
					]
				},
				//新增界面数据
				addForm: {
					id: '',
					name: '',
					school: '',
					colloge: '',
					major: '',
					role: ''
				}

			}
		},
		methods: {
			//性别显示转换
			formatSex: function (row, column) {
				return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getUsers();
			},
			//获取用户列表
			getUsers() {
				let para = {
					page: this.page,
					name: this.filters.name
				};
				console.log(para);
				this.listLoading = true;


				//这里应该通过接口获取到数据

                var url = global.baseurl + 'getuserlist';
                var token = sessionStorage.getItem('token');
                var _this = this;
                axios.post(url,{ 'token':token})
                    .then(function (response){
                        _this.users = response.data;
                        _this.total = _this.users.length;
                        _this.listLoading = false;

                    });




				//NProgress.start();
//				getUserListPage(para).then((res) => {
//					this.total = res.data.total;
//					this.users = res.data.users;
//					this.listLoading = false;
//					//NProgress.done();
//				});
			},
			//删除
			handleDel: function (index, row) {
				this.$confirm('确认删除该记录吗?', '提示', {
					type: 'warning'
				}).then(() => {
					this.listLoading = true;
					//NProgress.start();
					let para = { id: row.id };
					removeUser(para).then((res) => {
						this.listLoading = false;
						//NProgress.done();
						this.$message({
							message: '删除成功',
							type: 'success'
						});
						this.getUsers();
					});
				}).catch(() => {

				});
			},
			//显示编辑界面
			handleEdit: function (index, row) {
				this.editFormVisible = true;
				this.editForm = Object.assign({}, row);
				this.school = this.editForm.school.school;
				this.college = this.editForm.college.collegename;
				this.major = this.editForm.major.major;
				this.role = this.editForm.role.name;
                var url = global.baseurl + 'getallschool';
                var _this = this;
                axios.get(url)
                    .then(function (response){
                        console.log(response.data);
                        _this.schools = response.data;
                    });

                var url = global.baseurl + 'getcollegebyschoolid'+'/'+this.editForm.school.id;
                var _this = this;
                axios.get(url)
                    .then(function (response){
                        _this.colleges = response.data;
                    });

                var url = global.baseurl + 'getmajorbycollegeid'+'/'+this.editForm.college.id;
                var _this = this;
                axios.get(url)
                    .then(function (response){
                        _this.majors = response.data;
                    });
			},
			//显示新增界面
			handleAdd: function () {
				this.addFormVisible = true;
				this.addForm = {
					name: '',
					sex: -1,
					age: 0,
					birth: '',
					addr: ''
				};
			},
			//编辑
			editSubmit: function () {
				this.$refs.editForm.validate((valid) => {
					if (valid) {
						this.$confirm('确认提交吗？', '提示', {}).then(() => {
							this.editLoading = true;
							//NProgress.start();
							let para = Object.assign({}, this.editForm);
							para.birth = (!para.birth || para.birth == '') ? '' : util.formatDate.format(new Date(para.birth), 'yyyy-MM-dd');
							editUser(para).then((res) => {
								this.editLoading = false;
								//NProgress.done();
								this.$message({
									message: '提交成功',
									type: 'success'
								});
								this.$refs['editForm'].resetFields();
								this.editFormVisible = false;
								this.getUsers();
							});
						});
					}
				});
			},
			//新增
			addSubmit: function () {
				this.$refs.addForm.validate((valid) => {
					if (valid) {
						this.$confirm('确认提交吗？', '提示', {}).then(() => {
							this.addLoading = true;
							//NProgress.start();
							let para = Object.assign({}, this.addForm);
							para.birth = (!para.birth || para.birth == '') ? '' : util.formatDate.format(new Date(para.birth), 'yyyy-MM-dd');
							addUser(para).then((res) => {
								this.addLoading = false;
								//NProgress.done();
								this.$message({
									message: '提交成功',
									type: 'success'
								});
								this.$refs['addForm'].resetFields();
								this.addFormVisible = false;
								this.getUsers();
							});
						});
					}
				});
			},
			selsChange: function (sels) {
				this.sels = sels;
			},
			//批量删除
			batchRemove: function () {
				var ids = this.sels.map(item => item.id).toString();
				this.$confirm('确认删除选中记录吗？', '提示', {
					type: 'warning'
				}).then(() => {
					this.listLoading = true;
					//NProgress.start();
					let para = { ids: ids };
					batchRemoveUser(para).then((res) => {
						this.listLoading = false;
						//NProgress.done();
						this.$message({
							message: '删除成功',
							type: 'success'
						});
						this.getUsers();
					});
				}).catch(() => {

				});
			},
            restore: function () {
				this.school = this.oldschool;
				this.college = this.oldcollege;
				this.major = this.oldmajor;
            }
//			getCollegeBySchool:function(value){
//			    if(value){
//                    var url = global.baseurl + 'getcollegebyschoolid'+'/'+value;
//                    var _this = this;
//                    axios.get(url)
//                        .then(function (response){
//                            _this.colleges = response.data;
//                            console.log(_this.colleges);
//                        });
//				}
//			}
		},
		watch:{
		    	school:function (val,oldval) {
		    	    this.oldschool = oldval;
		    	    this.oldcollege = this.college;
		    	    this.oldmajor = this.major;
					var url = global.baseurl + 'getcollegebyschoolid'+'/'+val;
                    var _this = this;
                    axios.get(url)
                        .then(function (response){
                            _this.colleges = response.data;
                        });

                    if(oldval){
						this.college = '';
						this.major = '';
                    }
            	},
            college:function (val,oldval) {
		    	    if(val){
						var url = global.baseurl + 'getmajorbycollegeid'+'/'+val;
                  	  	var _this = this;
						axios.get(url)
                        .then(function (response){
                            _this.majors = response.data;
                            console.log(_this.majors);
                        });
                    }
                if(oldval){
                    this.major = '';
                }
            }
		},
		mounted() {
			this.getUsers();
		}
	}

</script>

<style scoped>

</style>