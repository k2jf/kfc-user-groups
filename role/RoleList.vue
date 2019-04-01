<!-- 角色列表 -->
<template>
  <div style="height: 100%;">
    <Table
      :columns="role.columns"
      :data="role.data"
      size="small"
      :loading="role.loading"
      class="margin-bottom"></Table>
  </div>
</template>
<script>
// eslint-disable-next-line
import { Table, Icon } from 'iview'

import { api } from '../api'

export default {
  name: 'RoleList',
  components: {
    Table
  },
  props: {
    currentGroup: {
      type: Object,
      required: false,
      default: () => {
        return {}
      }
    },
    isReloadRoleList: {
      type: Boolean,
      required: false
    }
  },
  data () {
    return {
      role: {
        loading: false,
        page: 1,
        size: 10,
        data: [],
        columns: [
          { title: '角色', key: 'name', minWidth: 110 },
          { title: '描述', key: 'description', minWidth: 130 },
          {
            title: '操作',
            width: 100,
            render: (h, params) => {
              return h(
                'span',
                {
                  style: {
                    cursor: 'pointer'
                  },
                  on: {
                    click: () => {
                      this.deleteRole(params.row.name)
                    }
                  }
                },
                [
                  h(
                    'Icon',
                    {
                      props: {
                        type: 'ios-trash',
                        size: 16,
                        color: '#5cadff'
                      }
                    }
                  ),
                  h(
                    'span', '移除'
                  )
                ]
              )
            }
          }
        ]
      }
    }
  },
  watch: {
    currentGroup: {
      handler (curVal, oldVal) {
        if (curVal && curVal.id) {
          this.getRoleList()
        }
      }
    },
    isReloadRoleList: {
      handler (curVal, oldVal) {
        if (this.currentGroup) {
          // 添加角色后刷新角色列表页面
          this.getRoleList()
        }
      }
    }
  },
  mounted () {
    if (this.currentGroup) {
      this.getRoleList()
    }
  },
  methods: {
    // 删除角色
    deleteRole (name) {
      this.$axios.put(`${api.groups}/${this.currentGroup.id}/remove`, { roles: [{ name: name }] }).then(res => {
        this.getRoleList()
      })
    },
    // 获取角色列表
    getRoleList () {
      this.role.loading = true
      let { page, size } = this.role
      let groupId = this.currentGroup.id

      this.$axios.get(`${api.roles}?page=${page}&size=${size}&groupId=${groupId}`).then(res => {
        this.role.data = res.data.result
        this.role.total = res.data.pages.total
        this.role.loading = false
      })
    }
  }
}
</script>