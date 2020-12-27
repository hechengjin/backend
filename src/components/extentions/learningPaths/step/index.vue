<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">路径步骤</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="12">
              <FormItem label="学习路径" prop="path_id">
                <template v-slot:label>学习路径</template>
                <Select v-model="filter.path_id" :datas="paths" keyName="id" titleName="name" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="12">
              <FormItem>
                <Button color="primary" @click="getData()">过滤</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" icon="h-icon-plus" permission="addons.learnPaths.step.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="sort" title="升序"></TableItem>
          <TableItem prop="name" title="步骤名"></TableItem>
          <TableItem prop="desc" title="简述"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.learnPaths.step.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-primary h-btn-s" permission="addons.learnPaths.step.update" text="编辑" @click="edit(data)"></p-button>

              <p-button
                glass="h-btn h-btn-default h-btn-s"
                permission="addons.learnPaths.relation.list"
                text="关联"
                @click="showRelationPage(data)"
              ></p-button>
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
      loading: false,
      paths: [],
      filter: {
        path_id: null
      }
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
      this.loading = true;
      let data = {};
      data.path_id = this.filter.path_id;
      R.Extentions.learningPaths.Steps.List(data).then(resp => {
        this.datas = resp.data.data;
        this.paths = resp.data.paths;
        this.filter.path_id = resp.data.path_id;
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
            modal.close();
            R.Extentions.learningPaths.Steps.Store(data).then(resp => {
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.learningPaths.Steps.Delete({ id: item.id }).then(resp => {
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
            modal.close();
            R.Extentions.learningPaths.Steps.Update(data).then(resp => {
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    showRelationPage(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['../relation/index'], resolve);
          },
          datas: {
            id: item.id
          }
        }
      });
    }
  }
};
</script>
