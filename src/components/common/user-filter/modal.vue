<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">用户</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="搜索">
                <input type="text" v-model="filter.keywords" placeholder="用户昵称/手机号" />
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="会员">
                <Select v-model="filter.role_id" :filterable="true" :datas="roles" keyName="id" titleName="name"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="标签">
                <Select v-model="filter.tag_id" :filterable="true" :datas="tags" keyName="id" titleName="name"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData(true)">搜索</Button>
                <Button @click="reset">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="users">
          <TableItem title="用户" :width="240">
            <template slot-scope="{ data }">
              <copytext :copytext="data.id" />
              <span class="grey">/</span>
              <copytext :copytext="data.nick_name" />
              <span class="grey">/</span>
              <copytext :copytext="data.mobile" />
            </template>
          </TableItem>
          <TableItem title="标签" :width="200">
            <template slot-scope="{ data }">
              <span class="tag-item" v-for="tag in data.tags" :key="tag.id">
                <copytext :copytext="tag.name" />
              </span>
            </template>
          </TableItem>
          <TableItem title="VIP" :width="100">
            <template slot-scope="{ data }">
              <template v-if="data.role">{{ data.role.name }}</template>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="240">
            <template slot-scope="{ data }">
              <button class="h-btn h-btn-s h-btn-primary" @click="selectThisUser(data)">选择</button>
            </template>
          </TableItem>
        </Table>
      </div>
      <div class="float-box mb-10">
        <Pagination v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      user_id: this.value,
      user: null,
      loading: false,
      users: [],
      tags: [],
      roles: [],
      filter: {
        id: null,
        keywords: null,
        tag_id: null,
        role_id: null
      },
      pagination: {
        page: 1,
        size: 10,
        total: 0
      }
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    changePage() {
      this.getData();
    },
    reset() {
      this.filter.keywords = '';
      this.filter.role_id = null;
      this.filter.tag_id = null;
      this.getData(true);
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      Object.assign(data, this.filter);
      R.Member.List(data).then(resp => {
        this.users = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
        this.roles = resp.data.roles;
        this.tags = resp.data.tags;
      });
    },
    selectThisUser(user) {
      this.$emit('success', {
        id: user.id,
        nickname: user.nick_name
      });
    }
  }
};
</script>