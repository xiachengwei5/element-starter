<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>Vue表格组件</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="plugins-tips">
            vue-datasource：一个用于动态创建表格的vue.js服务端组件。
            访问地址：<a href="https://github.com/coderdiaz/vue-datasource" target="_blank">vue-datasource</a>
        </div>
        <datasource language="en" :table-data="getData" :columns="columns" :pagination="information.pagination"
                :actions="actions"
                v-on:change="changePage"
                v-on:searching="onSearch"></datasource>
    </div>
</template>

<script>
    import axios from 'axios';
    import Datasource from 'vue-datasource';
    export default {
        data: function(){
            const self = this;
            return {
                url: '../../../static/datasource.json',
                information: {
                    pagination:{},
                    data:[]
                },
                columns: [
                    {
                        name: '序号',
                        key: 'id',
                    },
                    {
                        name: '姓名',
                        key: 'name',
                    },
                    {
                        name: '邮箱',
                        key: 'email',
                    },
                    {
                        name: 'IP地址',
                        key: 'ip',
                    }
                ],
                actions: [
                    {
                        text: '点我显示选中的数据',
                        class: 'btn-primary',
                        event(e, row) {
                            if(row != null){
                                self.$message('选中的行数：id:' + row.row.id +"; name:"+ row.row.name +"; email:"+ row.row.email+"; ip:"+ row.row.ip);
                            } else {
                                self.$message('未选中任何行');
                            }

                        }
                    }
                ],
                query:''        //查询条件
            }
        },
        components: {
            Datasource
        },
        methods: {
            //根据页面设置动态控制每页显示的记录数，此处数据从json中读取，分页不好处理
            changePage(values) {
                this.information.pagination.per_page = values.perpage;
                this.information.data = this.information.data;
            },
            onSearch(searchQuery) {
                this.query = searchQuery;
            }
        },
        computed:{
            //页面数据筛选功能
            getData(){
                const self = this;
                return self.information.data.filter(function (d) {
                    //alert(d.name +"==>"+ self.query);
                    if(d.name.indexOf(self.query) > -1){
                        return d;
                    }
                })
            }
        },
        beforeMount(){
            if(process.env.NODE_ENV === 'development'){
                this.url = '/ms/table/source';
            };
            axios.get(this.url).then( (res) => {
                this.information = res.data;
            })
        }
    }
</script>

<style src="../../../static/css/datasource.css"></style>