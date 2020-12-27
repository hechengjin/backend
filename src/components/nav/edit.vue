<style lang="less"></style>
<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :labelWidth="80" :rules="rules" :model="nav">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="平台" prop="platform">
              <Select v-model="nav.platform" :datas="platforms" keyName="id" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="上级" prop="parent_id">
              <Select v-model="nav.parent_id" :disabled="nav.platform === 'h5'" :datas="navs" keyName="id" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="升序" prop="sort">
              <input type="number" v-model="nav.sort" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="链接名" prop="name">
              <input type="text" v-model="nav.name" />
            </FormItem>
          </Cell>
          <Cell :width="12">
            <FormItem label="链接地址" prop="url">
              <input type="text" v-model="nav.url" />
            </FormItem>
          </Cell>
          <Cell :width="12">
            <FormItem label="Active" prop="active_routes">
              <input type="text" v-model="nav.active_routes" />
              <warn text="不清楚可不填写"></warn>
            </FormItem>
          </Cell>
          <Cell :width="24" v-if="nav.platform === 'PC'">
            <FormItem label="新窗口打开" prop="blank">
              <h-switch v-model="nav.blank" :trueValue="1" :falseValue="0"></h-switch>
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
import Nav from 'model/Nav';

export default {
  props: ['id'],
  data() {
    return {
      nav: Nav.parse({}),
      platforms: [],
      navs: [],
      rules: {
        required: ['sort', 'name', 'url']
      }
    };
  },
  watch: {
    'nav.platform'() {
      if (this.nav.platform === 'h5') {
        this.nav.parent_id = 0;
      }
    }
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Nav.Create().then(res => {
        let data = [];
        for (let i = 0; i < res.data.navs.length; i++) {
          if (res.data.navs[i].id !== this.id) {
            data.push(res.data.navs[i]);
          }
        }
        this.navs = data;
        this.platforms = res.data.platforms;
      });

      R.Nav.Edit({ id: this.id }).then(resp => {
        this.nav = resp.data;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Nav.Update(this.nav).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
