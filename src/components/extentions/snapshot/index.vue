<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">照片</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row>
            <Cell :width="6">
              <FormItem label="会员">
                <Select
                  v-model="filter.type"
                  :filterable="true"
                  :datas="typeRows"
                  keyName="id"
                  titleName="name"
                ></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="用户ID">
                <input type="number" v-model="filter.user_id" placeholder="用户ID" />
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="资源id">
                <input type="number" v-model="filter.other_id" placeholder="资源ID" />
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
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem title="用户ID" :width="80">
            <template slot-scope="{data}">
              <copytext :copytext="data.user_id" />
            </template>
          </TableItem>
          <TableItem title="资源ID" :width="80">
            <template slot-scope="{data}">
              <copytext :copytext="data.other_id" />
            </template>
          </TableItem>
          <TableItem title="用户" :width="120">
            <template slot-scope="{data}">
              <span v-if="data.user">{{data.user.nick_name}}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem prop="type_text" title="类型" :width="80"></TableItem>
          <TableItem prop="type_title" title="资源"></TableItem>
          <TableItem title="用户" :width="100">
            <template slot-scope="{ data }">
              <span>{{data.user.nick_name}}</span>
            </template>
          </TableItem>
          <TableItem prop="count" title="照片" :width="50"></TableItem>
          <TableItem prop="last_snap_at" title="最后拍照时间" :width="100"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <Button color="primary" @click="see(data)">查看照片</Button>
            </template>
          </TableItem>
        </Table>
      </div>

      <div class="float-box mb-10">
        <Pagination
          class="mt-10"
          v-if="pagination.total > 0"
          align="right"
          v-model="pagination"
          @change="changePage"
        />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      filter: {
        type: '',
        user_id: null,
        other_id: null
      },
      typeRows: [
        {
          id: 'vod',
          name: '视频'
        },
        {
          id: 'live',
          name: '直播课程'
        },
        {
          id: 'book',
          name: '电子书'
        },
        {
          id: 'paper',
          name: '考试'
        },
        {
          id: 'practice',
          name: '练习'
        }
      ],
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    changePage() {
      this.getData();
    },
    reset() {
      this.filter.user_id = null;
      this.filter.other_id = null;
      this.filter.type = null;
      this.getData(true);
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      data.user_id = this.filter.user_id;
      data.other_id = this.filter.other_id;
      data.type = this.filter.type;
      R.Extentions.Snapshot.Images.Index(data).then(resp => {
        this.datas = resp.data.data;
        this.pagination.total = resp.data.total;
        this.loading = false;
      });
    },
    see(item) {
      if (!item.images) {
        HeyUI.$Message.warn('暂无照片');
        return;
      }
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./images'], resolve);
          },
          datas: {
            images: item.images,
            rid: item.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData();
          }
        }
      });
    }
  }
};
</script>
