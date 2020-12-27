<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">分类</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Button color="h-btn h-btn-primary" @click="create()">添加</Button>
      </div>
      <div class="float-box mb-10">
        <Table ref="table" :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem prop="sort" title="升序" :width="80"></TableItem>
          <TableItem prop="name" title="分类名" treeOpener></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <Poptip content="确认删除？" @confirm="remove(datas, data)">
                <button class="h-btn h-btn-s h-btn-red">删除</button>
              </Poptip>
              <button class="h-btn h-btn-s h-btn-primary" @click="edit(data)">编辑</button>
            </template>
          </TableItem>
        </Table>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    changePage() {
      this.getData();
    },
    getData() {
      this.loading = true;
      R.Extentions.paper.PaperCategory.List(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.loading = false;
        setTimeout(() => {
          this.$refs.table.expandAll();
        }, 500);
      });
    },
    create() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./create'], resolve);
          }
        },
        events: {
          success: (modal, data) => {
            R.Extentions.paper.PaperCategory.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.paper.PaperCategory.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    },
    edit(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
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
            R.Extentions.paper.PaperCategory.Update(data).then(resp => {
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
