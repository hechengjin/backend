<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="slider">
        <Row :space="10">
          <Cell :width="8">
            <FormItem label="平台" prop="platform">
              <Select v-model="slider.platform" :datas="platforms" keyName="id" titleName="title"></Select>
            </FormItem>
          </Cell>
          <Cell :width="8">
            <FormItem label="URL" prop="url">
              <input type="text" v-model="slider.url" />
            </FormItem>
          </Cell>
          <Cell :width="8">
            <FormItem label="升序" prop="sort">
              <input type="number" v-model="slider.sort" />
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="24">
            <FormItem label="封面" prop="thumb">
              <image-upload v-model="slider.thumb" name="封面" help="长宽比3:1，建议尺寸：1200x400像素"></image-upload>
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  mounted() {
    R.Slider.Edit({ id: this.id }).then(res => {
      this.slider = res.data;
    });
  },
  data() {
    return {
      slider: {
        thumb: '',
        url: '',
        sort: 0
      },
      platforms: [
        {
          id: 'PC',
          title: 'PC'
        },
        {
          id: 'H5',
          title: 'H5'
        },
        {
          id: 'APP',
          title: 'APP'
        },
        {
          id: 'MINI',
          title: '微信小程序'
        }
      ],
      rules: {
        required: ['thumb', 'url', 'sort', 'platform']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Slider.Update(this.slider).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
