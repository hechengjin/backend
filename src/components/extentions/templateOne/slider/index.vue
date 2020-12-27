<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">幻灯片</span>
    </div>
    <div class="h-panel-body">
      <div class="mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          icon="h-icon-plus"
          permission="addons.TemplateOne.slider.store"
          text="添加"
          @click="create()"
        ></p-button>
      </div>
      <Table :loading="loading" :datas="datas">
        <TableItem prop="sort" title="升序"></TableItem>
        <TableItem title="图片">
          <template slot-scope="{ data }">
            <img :src="data.thumb" width="100" height="75" />
          </template>
        </TableItem>
        <TableItem prop="url" title="地址"></TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.TemplateOne.slider.delete" @click="remove(datas, data)"></p-del-button>
            <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.TemplateOne.slider.update" text="编辑" @click="edit(data)"></p-button>
          </template>
        </TableItem>
      </Table>
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
    this.init();
  },
  methods: {
    init() {
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      this.loading = true;
      R.Extentions.templateOne.Slider.List().then(resp => {
        this.datas = resp.data.data;
        this.loading = false;
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
            R.Extentions.templateOne.Slider.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.templateOne.Slider.Delete({ id: item.id }).then(resp => {
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
            R.Extentions.templateOne.Slider.Update(data).then(resp => {
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
