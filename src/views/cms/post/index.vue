<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">标题</label>
        <el-input v-model="query.title" clearable placeholder="标题" style="width: 185px;" class="filter-item" @keyup.enter.native="queryHandler" />
        <label class="el-form-item-label">内容</label>
        <el-input v-model="query.content" clearable placeholder="内容" style="width: 185px;" class="filter-item" @keyup.enter.native="queryHandler" />
        <label class="el-form-item-label">作者</label>
        <el-input v-model="query.author" clearable placeholder="作者" style="width: 185px;" class="filter-item" @keyup.enter.native="queryHandler" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="作者" />
          <el-form-item label="标题">
            <el-input v-model="form.title" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="内容">
            <el-input v-model="form.content" :rows="3" type="textarea" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="id" label="ID" />
        <el-table-column prop="title" label="标题" />
        <el-table-column prop="content" label="内容" />
        <el-table-column prop="authorId" label="作者" />
        <el-table-column prop="createBy" label="创建者" />
        <el-table-column prop="updateBy" label="更新者" />
        <el-table-column prop="createTime" label="创建日期" />
        <el-table-column prop="updateTime" label="更新时间" />
        <el-table-column v-if="checkPer(['admin','qslPost:edit','qslPost:del'])" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudQslPost from '@/api/cms/post/qslPost'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, title: null, content: null, authorId: null, createBy: null, updateBy: null, createTime: null, updateTime: null }
export default {
  name: 'QslPost',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '内容管理系统：内容管理', url: 'api/qslPost', idField: 'id', sort: 'id,desc', crudMethod: { ...crudQslPost }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'qslPost:add'],
        edit: ['admin', 'qslPost:edit'],
        del: ['admin', 'qslPost:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'title', display_name: '标题' },
        { key: 'content', display_name: '内容' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    },
    queryHandler() {
      const queryString = []
      for (const [key, value] of Object.entries(this.query)) {
        if (typeof key !== 'undefined') {
          queryString.push(key.concat('=like=').concat(value))
        }
      }
      this.query.filter = queryString.join(';')
      this.crud.toQuery()
      this.query = {}
    }
  }
}
</script>

<style scoped>

</style>
