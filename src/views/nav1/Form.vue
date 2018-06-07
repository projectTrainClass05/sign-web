<template>
	<section>
	<el-form ref="form" :model="form" label-width="80px" @submit.prevent="onSubmit" style="margin:20px;width:60%;min-width:600px;">
		<el-row>
			<el-col :span="8">
		<el-form-item label="课程类型">
			<el-select v-model="form.type" placeholder="请选择课程类型">
				<el-option label="暂不选择" value="0"></el-option>
				<el-option label="公共课" value="3"></el-option>
				<el-option label="专业课" value="2"></el-option>
				<el-option label="选修课" value="1"></el-option>
			</el-select>
		</el-form-item>
			</el-col>

			<el-col :span="16">
		<el-form-item label="课程名称"  >
			<el-input v-model="form.name" placeholder="选填"></el-input>
		</el-form-item>
			</el-col>
		</el-row>
		<el-row>
		<el-col :span="8">
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
		</el-col>
		<el-col :span="8">
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

		</el-col>
		<el-col :span="8">
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

		</el-col>
		</el-row>
		<!--<el-form-item label="活动时间">-->
			<!--<el-col :span="11">-->
				<!--<el-date-picker type="date" placeholder="选择日期" v-model="form.date1" style="width: 100%;"></el-date-picker>-->
			<!--</el-col>-->
			<!--<el-col class="line" :span="2">-</el-col>-->
			<!--<el-col :span="11">-->
				<!--<el-time-picker type="fixed-time" placeholder="选择时间" v-model="form.date2" style="width: 100%;"></el-time-picker>-->
			<!--</el-col>-->
		<!--</el-form-item>-->
		<el-form-item>
			<el-button type="primary" @click="onSubmit">立即查询</el-button>
			<el-button @click="reset">重置</el-button>
		</el-form-item>
	</el-form>

	<el-table :data="courses" style="width: 100%" v-bind:class="{visible1:visFlag , visible2:visFlag2}">
		<el-table-column prop="id" label="课程id" width="180" sortable>
		</el-table-column>
		<el-table-column prop="name" label="课程名" min-width="150" sortable>
		</el-table-column>
		<el-table-column prop="school.school" label="学校" min-width="180" sortable>
		</el-table-column>
		<el-table-column prop="college.collegename" label="学院" min-width="180" sortable>
		</el-table-column>
		<el-table-column prop="major.major" label="专业" min-width="180" sortable>
		</el-table-column>
		<el-table-column prop="classtype" label="类别" width="150"  :formatter="formatType" sortable>
		</el-table-column>
		<el-table-column label="操作" min-width="150">
			<template scope="scope">
				<el-button size="small" @click="createClass(scope.$index, scope.row)">创建班级</el-button>
				<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
				<el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
			</template>
		</el-table-column>
	</el-table>
	</section>
</template>

<script>
	import axios from 'axios'
	export default {
		data() {
			return {
                visFlag:false,
				visFlag2:true,
				form: {
					name: '',
                    school:'',
					major:'',
					college:'',
					type:'',
				},
				school:'',
                schools:[],
				college:'',
				colleges:[],
				major:'',
				majors:[],
				courses:[{
				    'id':'',
					'name':''
				}]
			}
		},
		methods: {
			onSubmit() {
			    if(this.form.name){

				}
				this.visFlag = true;
				this.visFlag2 = false;

				var _this = this;
                var url3 = global.baseurl + 'getcoursebymsg';
				axios.post(url3,{
				    'form':this.form
				}).then(function (response){
                    _this.courses = response.data;
                });
			},

			reset(){
                this.form= {
                    name: '',
                        school:'',
                        mojor:'',
                        college:'',
                        course:'',
                        region: '',
                        date1: '',
                        date2: '',
                        type:'',
                };
                this.school = '';
                this.college = '';
                this.major = '';
                this.visFlag = false;
                this.visFlag2 =true;
			},

			getSchoolList(){
			    var url = global.baseurl + 'getallschool';
                var _this = this;
                axios.get(url)
                    .then(function (response){
                        console.log(response.data);
                        _this.schools = response.data;
                    });

			},

            formatType(row, column){
                return row.classtype == 2 ? '专业课' : row.sex == 3 ? '公共课' : '选修课';
			},

            createClass:function (index, row) {

			},

            handleEdit:function (index, row) {

            },

            handleDel:function (index, row) {

            }




		},


        watch:{
            school:function (val) {

                this.form.school = this.school;
//                this.oldschool = oldval;
//                this.oldcollege = this.college;
//                this.oldmajor = this.major;
				if(val){
					var url = global.baseurl + 'getcollegebyschoolid'+'/'+val;
					var _this = this;
					axios.get(url)
						.then(function (response){
							_this.colleges = response.data;
                            _this.colleges.splice(0,0,{
                                'id':0,
                                'collegename':'暂不选择'
                            });
						});
                }
				this.college = '';
            },
            college:function (val) {

                this.form.college = this.college;
                if(val){
                    var url = global.baseurl + 'getmajorbycollegeid'+'/'+val;
                    var _this = this;
                    axios.get(url)
                        .then(function (response){
                            _this.majors = response.data;
                            _this.majors.splice(0,0,{
                                'id':0,
                                'major':'暂不选择'
                            });
                        });


                }
                this.major = '';

            },
            major:function (val) {

                this.form.major = this.major;


            }
        },

        mounted(){
            this.getSchoolList();
        }
	}

</script>
<style>
	.visible1{
		width: 100%;
		display: block;
	}
	.visible2{
		width: 100%;
		display: none;
	}
</style>