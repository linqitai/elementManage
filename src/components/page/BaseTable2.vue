<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>基础表格</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <el-table :data="tableData" style="width: 100%" border>
            <el-table-column prop="date" label="日期" width="180">
            </el-table-column>
            <el-table-column prop="name" label="姓名" width="180">
            </el-table-column>
            <el-table-column prop="address" label="地址">
            </el-table-column>
        </el-table>
        <div class="block" v-if="total>pageSize">
            <el-pagination layout="prev, pager, next" :total="total">
            </el-pagination>
        </div>
        <div>{{time|formatTime}}</div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            url: './static/vuetable.json',
            curPage: 1,
            pageSize: 10,
            tableData: [],
            total: 0,
            time: new Date().getTime()
        }
    },
    created() {
        this.getData();
    },
    methods: {
        getData() {
            let self = this;
            if (process.env.NODE_ENV === 'development') {
                self.url = '/ms/table/list';
            };
            self.$axios.post(self.url, { page: self.curPage }).then((res) => {
                console.log(res.data.list);
                self.tableData = res.data.list;
                self.total = self.tableData.length
                console.log("total:" + self.total)
                console.log(new Date().getSeconds()+":"+new Date().getMilliseconds())
                self.tableData.filter(function(item){
                    return item.date = self.getFullTime(item.date)
                })
                console.log(new Date().getSeconds()+":"+new Date().getMilliseconds())
            })
        }
    }
}
</script>

<style scoped>
    .el-table__header{
        background-color: #ffffff !important;
    }
</style>