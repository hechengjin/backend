<style lang="less" scoped>
.menu-box {
  position: relative;
  width: 360px;
  height: 500px;
  float: left;
  background-color: rgba(0, 0, 0, 0.02);

  .menus {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    width: 100%;
    height: 60px;
    background-color: rgba(0, 0, 0, 0.06);
    display: flex;

    .item {
      position: relative;
      flex: 1;
      text-align: center;
      height: 60px;
      line-height: 60px;
      border-right: 1px solid rgba(0, 0, 0, 0.07);

      &:hover {
        background-color: rgba(0, 0, 0, 0.07);
        cursor: pointer;
      }

      &.add-button {
        background-color: rgba(0, 0, 0, 0.05);
      }

      .children {
        position: absolute;
        bottom: 70px;
        left: 50%;
        margin-left: -50px;
        margin-left: -50px;
        width: 100px;
        height: auto;
        z-index: 11;
        background-color: rgba(0, 0, 0, 0.07);

        .childrem-item {
          width: 100%;
          height: 50px;
          line-height: 50px;
          text-align: center;
          float: left;
          border-bottom: 1px solid rgba(0, 0, 0, 0.06);
        }
      }
    }
  }
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">微信公众号菜单</span>
    </div>
    <div class="h-panel-body">
      <Row :space="10">
        <Cell :width="10">
          <div class="menu-box">
            <div class="menus">
              <div class="item" v-for="(menu, index) in menus" :key="'menu'+index">
                <span @click="editMenu(menu, index, null)">{{menu.name}}</span>
                <div class="children">
                  <div
                    class="add-children-button children-item"
                    v-if="menu.sub_button.list.length < 5"
                    @click="addChildren(index)"
                  >
                    <i class="h-icon-plus"></i>
                  </div>
                  <div
                    class="childrem-item"
                    @click="editMenu(childrenItem, index, childrenIndex)"
                    v-for="(childrenItem, childrenIndex) in menu.sub_button.list"
                    :key="'children-'+index+'-'+childrenIndex"
                  >{{childrenItem.name}}</div>
                </div>
              </div>
              <div class="add-button item" @click="addMenu" v-if="menus.length < 3">
                <i class="h-icon-plus"></i>
              </div>
            </div>
          </div>
        </Cell>
        <Cell :width="10">
          <Form mode="block" v-if="position.one !== null">
            <FormItem label="按钮类型">
              <Select v-model="menu.type" :datas="types" keyName="id" titleName="name"></Select>
            </FormItem>
            <FormItem label="菜单名">
              <input type="text" v-model="menu.name" />
            </FormItem>

            <template v-if="menu.type === 'view'">
              <FormItem label="打开网址">
                <input type="text" v-model="menu.url" />
              </FormItem>
            </template>
            <template v-else-if="menu.type === 'click'">
              <FormItem label="EventKey">
                <input type="text" v-model="menu.key" />
              </FormItem>
            </template>
            <template v-else-if="menu.type === 'miniprogram'">
              <FormItem label="小程序AppId">
                <input type="text" v-model="menu.appid" />
              </FormItem>
              <FormItem label="小程序Path">
                <input type="text" v-model="menu.pagepath" />
              </FormItem>
            </template>

            <FormItem>
              <Button color="primary" @click="save">保存</Button>
              <Button color="default" @click="deleteMenu">删除此菜单</Button>
            </FormItem>
          </Form>
        </Cell>
        <Cell :width="4">
          <div class="flaot-box mb-10">
            <warn text="注意，微信公众号菜单的更新和删除均有缓存，操作后的请等待10分后进行验证。" />
          </div>
          <div class="float-box mb-10">
            <p-del-button permission="mpWechat.menu.update" text="同步到微信公众号" @click="sync"></p-del-button>
          </div>
          <div class="float-box mb-10">
            <p-del-button permission="mpWechat.menu.update" text="清空菜单" @click="empty"></p-del-button>
          </div>
        </Cell>
      </Row>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      menus: [],
      types: [
        {
          id: 'view',
          name: '跳转网页'
        },
        {
          id: 'click',
          name: '点击事件'
        },
        {
          id: 'miniprogram',
          name: '打开小程序'
        }
      ],
      position: {
        one: null,
        two: null
      },
      menu: {
        type: null,
        name: '',
        url: null,
        key: null,
        appid: null,
        pagepath: null
      }
    };
  },
  mounted() {
    this.getMenu();
  },
  methods: {
    getMenu() {
      R.MpWecaht.Menu().then(res => {
        if (typeof res.data.menu.selfmenu_info.button !== 'undefined') {
          let menus = res.data.menu.selfmenu_info.button;
          for (let i = 0; i < menus.length; i++) {
            if (typeof menus[i].sub_button === 'undefined') {
              menus[i].sub_button = {
                list: []
              };
            }
          }
          this.menus = menus;
        }
      });
    },
    addMenu() {
      if (this.menus.length >= 3) {
        HeyUI.$Message.warn('一级菜单最多三个');
        return;
      }
      let menu = {
        name: '请修改',
        type: null,
        sub_button: {
          list: []
        }
      };
      this.menus.push(menu);

      this.position.one = this.menus.length - 1;
      this.position.two = null;
      this.menu = menu;
    },
    editMenu(item, one, two) {
      this.position.one = one;
      this.position.two = two;

      let menu = {
        type: item.type,
        name: item.name,
        sub_button: {
          list: []
        }
      };
      if (typeof item.sub_button !== 'undefined') {
        menu.sub_button = item.sub_button;
      }
      if (menu.type === 'click') {
        menu.key = null;
      } else if (menu.type === 'miniprogram') {
        menu.appid = null;
        menu.pagepath = null;
      } else if (menu.type === 'view') {
        menu.url = item.url;
      }
      this.menu = menu;
    },
    save() {
      console.log(this.position);
      if (this.position.one === null) {
        HeyUI.$Message.warn('数据错误');
        return;
      }

      if (this.position.two === null || typeof this.position.two === 'undefined') {
        Object.assign(this.menus[this.position.one], this.menu);
      } else {
        Object.assign(this.menus[this.position.one]['sub_button']['list'][this.position.two], this.menu);
      }
    },
    deleteMenu() {
      if (this.position.one === null) {
        HeyUI.$Message.warn('数据错误');
        return;
      }

      if (this.position.two === null) {
        this.menus.splice(this.position.one, 1);
      } else {
        this.menus[this.position.one]['sub_button']['list'].splice(this.position.two, 1);
      }

      this.menu = {};
      this.position.one = null;
      this.position.two = null;
    },
    addChildren(index) {
      if (typeof this.menus[index].sub_button === 'undefined') {
        this.menus[index].sub_button = { list: [] };
      }
      if (this.menus[index].sub_button.list.length >= 5) {
        HeyUI.$Message.warn('二级菜单最多添加5个');
        return;
      }
      let menu = {
        name: '请修改',
        type: null,
        appid: null,
        url: null,
        pagepath: null
      };
      this.menus[index].sub_button.list.push(menu);
      this.position.one = index;
      this.position.two = this.menus[index].sub_button.list.length - 1;
      this.menu = menu;
    },
    sync() {
      let data = {
        button: []
      };
      if (this.menus.length === 0) {
        HeyUI.$Message.warn('菜单为空');
        return;
      }

      // 复制一个新的对象[否则下面的操作将会影响到this.menus]
      var menus = JSON.parse(JSON.stringify(this.menus));
      for (let i = 0; i < menus.length; i++) {
        let menu = menus[i];
        if (menu.type === null) {
          HeyUI.$Message.warn(`菜单${menu.name}未选择类型`);
          return;
        }
        if (typeof menu.sub_button !== 'undefined') {
          // 有子菜单
          let children = [];
          for (let j = 0; j < menu.sub_button.list.length; j++) {
            let childrenMenu = menu.sub_button.list[j];
            let newChildrenMenu = {
              name: childrenMenu.name,
              type: childrenMenu.type
            };
            if (newChildrenMenu.type === null) {
              HeyUI.$Message.warn(`菜单${newChildrenMenu.name}未选择类型`);
              return;
            }
            if (childrenMenu.type === 'view') {
              newChildrenMenu.url = childrenMenu.url;
            } else if (childrenMenu.type === 'click') {
              newChildrenMenu.key = childrenMenu.key;
            } else if (childrenMenu.type === 'miniprogram') {
              newChildrenMenu.appid = childrenMenu.appid;
              newChildrenMenu.pagepath = childrenMenu.pagepath;
            }
            children.push(newChildrenMenu);
          }
          // 删除
          delete menu.sub_button;
          if (children.length > 0) {
            let newMenu = {
              name: menu.name,
              sub_button: children
            };
            menu = newMenu;
          }
        }
        data.button.push(menu);
      }

      R.MpWecaht.MenuUpdate({ menu: data }).then(() => {
        HeyUI.$Message.success('菜单更新成功');
        this.position.one = null;
        this.position.two = null;
        this.getMenu();
      });
    },
    empty() {
      R.MpWecaht.MenuEmpty().then(() => {
        this.position.one = null;
        this.position.two = null;
        HeyUI.$Message.success('微信公众号菜单已清空');
      });
    }
  }
};
</script>