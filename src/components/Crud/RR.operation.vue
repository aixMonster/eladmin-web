<!--搜索与重置-->
<template>
  <span>
    <el-button class="filter-item" size="mini" type="success" icon="el-icon-search" @click="queryHandler">搜索</el-button>
    <el-button v-if="crud.optShow.reset" class="filter-item" size="mini" type="warning" icon="el-icon-refresh-left" @click="crud.resetQuery()">重置</el-button>
  </span>
</template>
<script>
import { crud } from '@crud/crud'
export default {
  mixins: [crud()],
  props: {
    itemClass: {
      type: String,
      required: false,
      default: ''
    }
  },
  methods: {
    queryHandler() {
      const queryString = []
      if (this.crud.params.prepare !== null && this.crud.params.prepare !== '' && this.crud.params.prepare !== undefined) {
        queryString.push(this.crud.params.prepare)
        this.crud.query.filter = ''
      }
      for (const [key, value] of Object.entries(this.crud.query)) {
        if (value !== null && value !== '' && value !== undefined) {
          queryString.push(key.concat('=like=').concat(value))
        }
      }
      this.crud.query.filter = queryString.join(';')
      this.crud.toQuery()
      this.crud.query.filter = ''
    }
  }
}
</script>
