<template>
<div class="business-card-list">
    <div class="page-header">
        <el-page-header content="名片申请管理"> </el-page-header>
    </div>
    
    <!-- 顶部索引条 -->
    <el-row>
    <el-col :span="1">
        <el-button @click="createPage()" type="primary" size="small" icon="el-icon-plus">添加</el-button>
    </el-col>
    <el-col :span="6">
        <el-input placeholder="请输入检索标题" v-model="searchKeyword" size="small" @keyup.enter.native="searchData()" clearable>
            <el-button slot="append" icon="el-icon-search" @click="searchData()"></el-button>
        </el-input>
    </el-col>
    <el-col :span="1">
        <el-button plain size="small" icon="el-icon-s-data">高级检索</el-button>
    </el-col>
    </el-row>

    <!-- 内容列表 -->
    <el-table :data="displayData" stripe style="width: 100%">
        <el-table-column prop="applyDate" label="申请日期" sortable width="120"> </el-table-column>
        <el-table-column prop="title" label="申请标题" width="240"> </el-table-column>
        <el-table-column prop="depCode" label="部门编号" sortable width="120"> </el-table-column>
        <el-table-column prop="depName" label="部门名称" sortable width="120"> </el-table-column>
        <el-table-column prop="empName" label="创建人" sortable width="120"> </el-table-column>
        <el-table-column prop="needDate" label="需要日期" sortable width="120"> </el-table-column>
        <el-table-column prop="needNumber" label="需要数量" sortable width="120"> </el-table-column>
        <el-table-column prop="auMan" label="审批人" sortable width="120"> </el-table-column>
        <el-table-column prop="auTime" label="审批时间" sortable> </el-table-column>
        <el-table-column label="操作" width="100">
            <template slot-scope="scope">
                <el-button @click="editPage(scope.row)" type="text" size="small">编辑</el-button>
                <el-popconfirm 
                title="确定要删除这条记录吗？"
                icon-color="red"
                @confirm="removeItem(scope.$index)">
                    <el-button type="text" size="small" slot="reference">删除</el-button>
                </el-popconfirm>
            </template>
        </el-table-column>
    </el-table>

    <!-- 页码控制 -->
    <div class="block pagination">
        <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[10, 20, 30, 50]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="searchResult.length">
        </el-pagination>
    </div>

    <!-- 引入创建/编辑页组件 -->
    <BusinessCardCreateEdit
    v-if="activateCreateEdit"
    :props="loadCreateEdit"
    @submit-form="updateList"
    @close-window="closeCreateEdit">
    </BusinessCardCreateEdit>
</div>
</template>

<script>
import jasonData from "/static/data.json";
import BusinessCardCreateEdit from "./BusinessCardCreateEdit.vue";

export default {
    name: "BusinessCardList",
    components: {
        BusinessCardCreateEdit,
    },

    data() {
        return {
            // 数据存储
            jasonData,
            // 创建/编辑页组件控制与通讯
            activateCreateEdit: false,
            loadCreateEdit: {
                type: '',
                data: { }
            },
            // 页面控件
            popVisible: false,
            currentPage: 1,
            pageSize: 10,
            // 页面搜索
            searchKeyword:'',
            searchResult: [ ]
        };
    },

    computed: {
        displayData() {
            const startIndex = (this.currentPage - 1) * this.pageSize;
            const endIndex = this.currentPage * this.pageSize;
            return this.searchResult.slice(startIndex, endIndex);
        }
    },

    methods: {
        createPage() {
            this.loadCreateEdit.type = 'create';
            this.activateCreateEdit = true;
        },
        editPage(row) {
            this.loadCreateEdit.type = 'edit';
            this.loadCreateEdit.data = row;
            this.activateCreateEdit = true;
        },
        removeItem(index) {
            jasonData.splice(index, 1);
            this.popVisible = false;
        },
        closeCreateEdit() {
            this.activateCreateEdit = false;
        },
        updateList(submitInfo) {
            this.activateCreateEdit = false;
            if(submitInfo.type === 'create') {
                this.jasonData.push(submitInfo.data);
            }
            this.$message({
                message: '提交成功',
                type: 'success'
            });
        },
        handleSizeChange(size) {
            this.pageSize = size;
        },
        handleCurrentChange(currentPage) {
            this.currentPage = currentPage;
        },
        searchData() {
            if(this.searchKeyword === '' || this.searchKeyword === null) {
                this.searchResult = this.jasonData;
                return undefined;
            }
            this.searchResult = this.jasonData.filter(item => 
                item.title.indexOf(this.searchKeyword) >= 0
            );
            this.currentPage = 1;
        }

    },

    beforeCreate() {
        document.title = '名片管理';
    },

    created() {
        this.searchResult = this.jasonData;
    }
};
</script>

<style scoped>
.business-card-list
{
    text-align: left;
}
.page-header
{
    margin-bottom: 10px;
}
.business-card-list .pagination
{
    margin-top: 10px;
}
</style>
