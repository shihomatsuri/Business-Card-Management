<template>
<div class="create-edit-page">
    <div class="page-header">
        <el-page-header v-if="this.props.type === 'create'" @back="$emit('close-window')" content="添加名片申请"> </el-page-header>
        <el-page-header v-if="this.props.type === 'edit'" @back="$emit('close-window')" content="编辑名片申请"> </el-page-header>
    </div>

    <el-form ref="form" :model="form" :rules="rules" label-width="100px">
        <!-- 第一行 -->
        <el-row>
            <el-col :span="8">
                <el-form-item label="标题" prop="title">
                    <el-input v-model="form.title" placeholder="请输入标题"></el-input>
                </el-form-item>
            </el-col>
            <el-col :span="8">
                <el-form-item label="申请人" prop="empName">
                    <el-input v-model="form.empName"></el-input>
                </el-form-item>
            </el-col>
            <el-col :span="8">
                <el-form-item label="所属部门" prop="depCode">
                <el-select v-model="form.depCode" placeholder="请选择">
                    <el-option
                    v-for="dep in departments"
                    :key="dep.value"
                    :label="dep.label"
                    :value="dep.value">
                    <span style="float: left">{{ dep.label }}</span>
                    <span style="float: right; color: #8492a6; font-size: 13px">{{ dep.value }}</span>
                    </el-option>
                </el-select>
                </el-form-item>
            </el-col>
        </el-row>
        <!-- 第二行 -->
        <el-row>
            <el-col :span="8">
                <el-form-item label="需求数量" prop="needNumber">
                    <el-input v-model="form.needNumber" placeholder="请输入数量"></el-input>
                </el-form-item>
            </el-col>
            <el-col :span="8">
                <el-form-item label="申请日期" prop='applyDate'>
                    <el-date-picker
                    v-model="form.applyDate"
                    type="date"
                    value-format="yyyy-MM-dd"
                    placeholder="选择日期">
                    </el-date-picker>
                </el-form-item>
            </el-col>
            <el-col :span="8">
                <el-form-item label="需要日期" prop="needDate">
                    <el-date-picker
                    v-model="form.needDate"
                    type="date"
                    value-format="yyyy-MM-dd"
                    placeholder="选择日期">
                    </el-date-picker>
                </el-form-item>
            </el-col>
        </el-row>
        <!-- 第三行 -->
        <el-row>
            <el-col :span="16">
                <el-form-item label="申请说明" prop="note">
                    <el-input
                    type="textarea"
                    :autosize="{ minRows: 2, maxRows: 4}"
                    placeholder="请输入说明"
                    v-model="form.note">
                    </el-input>
                </el-form-item>
            </el-col>
        </el-row>
    </el-form>

    <el-row>
        <el-button @click="submitForm()" type="primary">提 交</el-button>
        <el-button @click="clearForm('form')">清 空</el-button>
        <el-button @click="$emit('close-window')">退 出</el-button>
    </el-row>
</div>
</template>

<script>
export default {
    props: ['props'],
    data() {
        return {
            form: {
                applyID: 1,
                title: '',
                empID: 0,
                empName: '',
                depID: 0,
                depCode: '',
                depName: '',
                applyDate: '',
                needDate: '',
                needNumber: 0,
                cEmpID: 0,
                cEmpName: '',
                auMan: '',
                auTime: '',
                note: '',
                memo: '',
                cDate: '',
                states: 0
            },

            rules: {
                title: [
                    { required: true, message: '请输入标题', trigger: 'blur' },
                    { min: 3, max: 100, message: '长度在 3 到 100 个字符', trigger: 'blur' }
                ],
                empName: [
                    { required: true, mesage: '请输入姓名', trigger: 'blur'}
                ],
                depCode: [
                    { required: true, message: '请选择部门', trigger: 'change' }
                ],
                applyDate: [
                    { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
                ],
                needDate: [
                    { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
                ],
                needNumber: [
                    { required: true, mesage: '请输入数量', trigger: 'blur'}
                ],
                note: [
                    { required: true, message: '请输入说明', trigger: 'blur' },
                    { min: 1, max: 500, message: '长度在 1 到 500 个字符', trigger: 'blur' }
                ]
            },

            user: {
                userID: 1,
                userName: '陆阳',
                dep: {
                    depID: 1,
                    depCode: '1001',
                    depName: '产品部'
                }
            },

            departments: [{
                value: '1001',
                label: '产品部'
            },{
                value: '1002',
                label: '销售部'
            },{
                value: '1003',
                label: '人事部'
            },{
                value: '1004',
                label: '售后部'
            }]
        }
    },
    created() {
        if(this.props.type === 'create') {
            this.form.empID = this.user.userID;
            this.form.empName = this.user.userName;
            this.form.cEmpID = this.user.userID;
            this.form.cEmpName = this.user.userName;
            this.form.depID = this.user.dep.depID;
            this.form.depCode = this.user.dep.depCode;
            this.form.depName = this.user.dep.depName;
            this.form.applyDate = this.formatDate(new Date());
            this.form.cDate = this.formatDate(new Date());
        } else if(this.props.type === 'edit') {
            this.form = this.props.data;
        }
    },
    methods: {
        submitForm() {
            const submitInfo = {
                type: this.props.type,
                data: this.form
            };
            this.$emit('submit-form', submitInfo);
        },
        clearForm(formName) {
            this.$refs[formName].resetFields();
        },
        formatDate(inputTime) {
            var date = new Date(inputTime);
            var y = date.getFullYear();
            var m = date.getMonth() + 1;
            m = m < 10 ? ('0' + m) : m;
            var d = date.getDate();
            d = d < 10 ? ('0' + d) : d; 
            return y + '-' + m + '-' + d;
        }
    }
}
</script>

<style scoped>
.create-edit-page
{
    top: 25px;
    width: 95%;
    height: 80vh;
    margin-top: 60px;
    position: absolute;
    background: #ffffff;
}
.page-header
{
    margin-bottom: 10px;
}
</style>