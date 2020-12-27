<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">章节</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary h-btn-s" permission="addons.Paper.practice_chapter.store" text="添加" @click="create()"></p-button>
        <p-del-button permission="addons.Paper.practice.delete" text="批量删除" @click="deleteSubmit()"></p-del-button>
      </div>
      <div class="float-box mb-10">
        <Table ref="table" :loading="loading" :checkbox="true" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem prop="sort" title="升序" :width="120"></TableItem>
          <TableItem prop="name" title="章节名"></TableItem>
          <TableItem prop="question_count" title="题目数" unit="个"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-button
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.Paper.practice_chapter.update"
                text="编辑"
                @click="edit(data)"
              ></p-button>
              <p-button
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.Paper.practice_chapter.questions"
                text="试题"
                @click="showQuestionsPage(data)"
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
  props: ['id'],
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
      R.Extentions.paper.PracticeChapter.List({ pid: this.id }).then(resp => {
        this.datas = resp.data.data;
        this.loading = false;
      });
    },
    deleteSubmit() {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要删除的章节');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.paper.PracticeChapter.Delete({ ids: ids }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    create() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./create'], resolve);
          },
          datas: {
            pid: this.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData();
          }
        }
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
            id: item.id,
            pid: this.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData();
          }
        }
      });
    },
    showQuestionsPage(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./questions'], resolve);
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
