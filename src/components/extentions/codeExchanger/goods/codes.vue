<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">兑换码</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box">
        <Form>
          <Row :space="10">
            <Cell :width="12">
              <FormItem label="搜索">
                <input type="text" v-model="filter.code" placeholder="兑换码" />
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="UID">
                <user-filter v-model="filter.user_id"></user-filter>
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
      <div class="float-box mt-10">
        <p-del-button permission="addons.CodeExchanger.codes.delete.multi" @click="deleteSubmit()"></p-del-button>

        <p-button
          glass="h-btn h-btn-primary h-btn-s"
          permission="addons.CodeExchanger.codes.generate"
          text="批量生成10个"
          @click="showGeneratePage(10)"
        ></p-button>
        <p-button
          glass="h-btn h-btn-primary h-btn-s"
          permission="addons.CodeExchanger.codes.generate"
          text="批量生成50个"
          @click="showGeneratePage(50)"
        ></p-button>
      </div>
      <div class="float-box mt-10">
        <Table :loading="loading" :datas="datas" :checkbox="true" ref="table">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem title="兑换码">
            <template slot-scope="{ data }">
              <copytext :copytext="data.code" />
            </template>
          </TableItem>
          <TableItem title="使用" :width="300">
            <template slot-scope="{ data }">
              <span v-if="data.is_used === 1" class="red">
                <span v-if="data.user">{{ data.user.nick_name }}</span>
                <span>/</span>
                <span>{{ data.used_at }}</span>
              </span>
              <span v-else>否</span>
            </template>
          </TableItem>
        </Table>
      </div>
      <div class="float-box mt-10 mb-10">
        <Pagination v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['gid'],
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      datas: [],
      loading: false,
      filter: {
        code: null,
        user_id: null
      }
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.filter.code = null;
      this.filter.user_id = null;
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      data.gid = this.gid;
      Object.assign(data, this.filter);
      R.Extentions.CodeExchanger.Codes.Index(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
      });
    },
    deleteSubmit() {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要删除的数据');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.CodeExchanger.Codes.DeleteMulti({ ids: ids }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    showGeneratePage(count = 10) {
      R.Extentions.CodeExchanger.Codes.Generate({ gid: this.gid, count: count }).then(res => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    }
  }
};
</script>
