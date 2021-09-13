# Element 表格 + 分页器实现前端分页

```vue
<template>
	<!--表格-->
	<el-table
          :data="Table.slice((teacher_currentPage - 1) * teacher_pagesize,teacher_currentPage * teacher_pagesize)"
          style="width: 100%">
          <el-table-column
            prop="name"
            label="姓名"
            align="center"
            width="">
          </el-table-column>
        </el-table>
	 <!--分页器-->
        <el-pagination
          background
          layout="total, sizes, prev, pager, next, jumper"
          :page-sizes="[10, 15, 20, 30]"
          :page-size="pagesize"
          :current-page="currentPage"
          @current-change="currentFy"
          @size-change="handleSizeChange"
          :total="Table.length"
        >
        </el-pagination>
</template>
<script>
	export.default {
        data(){
            return{
                Table:[{}],
                pagesize:10, // 每页显示的条数
                currentPage:1 // 显示当前页数
            }
        },
        methods:{
            currentFy(currentPage){
                this.currentPage = currentPage;
            },
            handleSizeChange(size) {
              this.pagesize = size;
            },
        }
    }
</script>
```

值得注意的是 **Table** 的初始化数据里,要保留一条信息, 如果数组为空,则 **slice** 操作符会报错