<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">章节</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="mb-10">
        <p-button glass="h-btn h-btn-primary" icon="h-icon-plus" permission="addons.meedu_books.book.store" text="添加" @click="create()"></p-button>
      </div>
      <Table :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID"></TableItem>
        <TableItem prop="sort" title="升序"></TableItem>
        <TableItem prop="name" title="章节名"></TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.meedu_books.book.delete" @click="remove(datas, data)"></p-del-button>
            <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.meedu_books.book.update" text="编辑" @click="edit(data)"></p-button>
          </template>
        </TableItem>
      </Table>
    </div>
  </div>
</template>
<script>
export default {
  props: ['bid'],
  data() {
    return {
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.getData();
    },
    changePage() {
      this.getData();
    },
    getData() {
      this.loading = true;
      R.Extentions.meeduBooks.Chapter.List({ bid: this.bid }).then(resp => {
        this.datas = resp.data;
        this.loading = false;
      });
    },
    create() {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['./create'], resolve);
          },
          datas: {
            bid: this.bid
          }
        },
        events: {
          success: (modal, data) => {
            R.Extentions.meeduBooks.Chapter.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.meeduBooks.Chapter.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    },
    edit(item) {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['./edit'], resolve);
          },
          datas: {
            id: item.id
          }
        },
        events: {
          success: (modal, data) => {
            R.Extentions.meeduBooks.Chapter.Update(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    }
  }
};
</script>
