title: 右键菜单
category: 技能
date: 2019-08-14
index_img: 

---

两者区别可能是有没有使用菜单宽度跟屏幕宽度对菜单坐标进行计算，计算后可以处理在屏幕边缘单击时菜单超出屏幕无法显示的问题

<!--more-->

#### 右键菜单

> 自己写的（非vue）

两者区别可能是有没有使用菜单宽度跟屏幕宽度对菜单坐标进行计算，计算后可以处理在屏幕边缘单击时菜单超出屏幕无法显示的问题

```js
this.gridpage.element.on('mouseup', '.k-grid-content tr', function(e) {
      let tr = $(this),
        data = tr.data('data') || me.gridpage.dataItem(tr),
        targetDom = $(e.target);

      if (e.button === 2) {
        tr.bind('contextmenu', (e) => {
          // 快速编辑      2019/09/12 14:15:43      --柯军
          me._fastEdit(data, targetDom);
          tr.unbind('contextmenu');
          return false;
        })
      }
      return false;
    });

/**
   * @createtime 2019/09/12 14:14:29
   * @author 柯军 <arthaskj@163.com>
   * @param {object} data tr单条数据
   * @param {dom} targetDom 右键选中的目标元素
   * @desc tr上右键打开快速编辑
   */
  _fastEdit(data, targetDom) {
    const me = this;
    let params = {
      routeKey: me.routeKeyConstruct(me.EXTENSION_DATA.query[0]),
      orderNo: data.orderNo
    }
    me.customAPI('cbt', 'GetRequestReplyOrderDetail', params).then((res) => {
      if (res) {
        let canSubmit = me.renderButton({
            type: 'showOrHide',
            key: 'canReply',
            data: res.buttonPermissions,
          }),
          canModify = me.renderButton({
            type: 'showOrHide',
            key: 'canModify',
            data: res.buttonPermissions,
          });

        let pop = new KJ.Popconfirm({
          title: `快速操作(${data.orderNo || '缺少委托单号'})`,
          init() {
            this.target = Box.create('HY.traderequest.indicationcommond.children.cashSale.details.response.fastEdit', {
              $params: {
                orderNo: data.orderNo,
                data: res,
                canSubmit: canSubmit,
                canModify: canModify,
                disEditable: !canSubmit && !canModify
              },
              HEADEROPTION: me.HEADEROPTION,
              renderTo: this.el.content,
            })
          },
          buttons: [
            {
              text: '回复',
              theme: true,
              display: !canSubmit,
              handle(e) {
                let params = e.target.getValue();
                me.fnReply(params, () => {
                  e.fnClose();
                })
              }
            },
            {
              text: '修改',
              theme: true,
              display: !canModify,
              handle(e) {
                let params = e.target.getValue();
                me.fnModify(params, () => {
                  e.fnClose();
                })
              }
            },
            {
              text: '取消',
              handle(e) {
                e.fnClose();
              }
            }
          ],
          renderTo: targetDom
        });
      }
    })
  },
```

```js
/**
 * @Author: 柯军
 * @Date: 2019/09/11 17:00:04
 * @Description: 气泡确认框
 */
window.KJ = window.KJ || {};
window.KJ.Popconfirm = class Popconfirm {
  _setParams(props) {
    this.props = props;

    this.title = props.title;// 编辑框标题
    this.icon = props.icon;// 编辑框标题图标

    this.buttons = props.buttons;// 按钮组数据
    this.width = props.width;// 窗体宽度
    this.height = props.height;// 窗体高度

    this.init = props.init;// 编辑框初始化函数，可以在这里渲染内容
    this.onClose = props.onClose;// 编辑框关闭时的触发事件

    this.renderTo = props.renderTo;// 编辑框挂载对象

    this.el = {
      target: $(this._getTemplate()),
    }
    this.el.btngroup = this.el.target.find('.h-popover-buttons');// 编辑框按钮组
    this.el.content = this.el.target.find('.h-popover-message');// 编辑框主体
    this.el.arrow = this.el.target.find('.h-popover-arrow');
  }

  constructor(props) {
    // 获取当前最大的z-index
    this._getMaxIndex();

    this._setParams(props);

    // 展示mask遮罩
    this._showMask();

    // 初始化内容部分
    this._initContent();

    // 初始化按钮组
    this._initButtons();

    // 窗体插入dom流
    $('body').append(this.el.target);

    // 定位窗体位置
    this._setCoordinate();
  }

  /**
   * @createtime 2019/09/12 15:55:54
   * @author 柯军 <arthaskj@163.com>
   * @desc 获取页面最大z-index
   */
  _getMaxIndex() {
    var maxZ = Math.max.apply(null,
      $.map($('body *'), (e, n) => {
        if ($(e).css('position') !== 'static')
          return parseInt($(e).css('z-index')) || -1;
      }));

    this.maxIndex = maxZ || 10002;

    return maxZ;
  }

  /**
   * @createtime 2019/09/12 15:55:36
   * @author 柯军 <arthaskj@163.com>
   * @desc 触发init事件
   */
  _initContent() {
    this.init && this.init(this);
    // 页面resize触发关闭事件      2019/09/12 11:24:08      --柯军
    $(window).resize(() => {
      this.fnClose();
    })
  }

  /**
   * @createtime 2019/09/12 15:55:19
   * @author 柯军 <arthaskj@163.com>
   * @desc 处理按钮数据，没有则不显示
   */
  _initButtons() {
    let opt = Object.assign({}, this.props),
      {buttons} = opt;

    if (buttons && buttons.length) {
      buttons.forEach((button, index) => {
        if (button.display) return;
        let buttonDom = $(this._renderButton(button));

        buttonDom.bind('click', () => {
          if (typeof button.handle === 'string') {
            opt[button.handle] && opt[button.handle](this);
          } else if (typeof button.handle === 'function') {
            button.handle(this);
          }
        })

        this.el.btngroup.append(buttonDom);
      })
    } else {
      this.el.btngroup.remove();
    }

  }

  /**
   * @createtime 2019/09/12 15:54:46
   * @author 柯军 <arthaskj@163.com>
   * @param {button} 按钮数据
   * @desc 渲染单个button
   */
  _renderButton(button) {
    let buttonTpl = `<button type="button" class="h-btn ${button.theme ? 'h-btn-primary' : ''} h-btn-sm">
                      <span>${button.text || ''}</span>
                    </button>`
    return buttonTpl;
  }

  /**
   * @createtime 2019/09/12 15:54:34
   * @author 柯军 <arthaskj@163.com>
   * @desc 计算控件坐标
   */
  _calCoordinate() {
    let target = this.renderTo,
      width = target.width(),
      height = target.height(),
      offset = target.offset(),
      left = offset.left,
      top = offset.top,
      targetWidth = this.el.target.width(),
      targetHeight = this.el.target.height(),
      centerLeft = left + width / 2,
      bodyWidth = $('body').width(),
      calHeight = top - targetHeight - 6;

    // 超出右侧      2019/09/12 14:58:40      --柯军
    if (centerLeft + targetWidth / 2 > bodyWidth) {
      left = bodyWidth - targetWidth - 10;
      this.el.arrow.css('left', `calc(50% - ${ - (centerLeft + targetWidth / 2 - bodyWidth)}px)`);
    } else if (centerLeft > targetWidth / 2) { // 左右正常      2019/09/12 14:58:51      --柯军
      left = centerLeft - targetWidth / 2;
      this.el.arrow.css('left', `calc(50% - 0px)`);
    } else if (centerLeft < targetWidth / 2) { // 超出左侧      2019/09/12 14:59:05      --柯军
      left = 10;
      this.el.arrow.css('left', `calc(50% - ${targetWidth / 2 - centerLeft}px)`);
    }

    // 正常显示      2019/09/12 14:59:21      --柯军
    if (calHeight > 0) {
      top = calHeight;
    } else { // 超出顶部
      this.el.target.removeClass('h-popover-placement-top').addClass('h-popover-placement-bottom');
      top = top + height + 6;
    }

    return {left, top};
  }

  /**
   * @createtime 2019/09/12 15:54:13
   * @author 柯军 <arthaskj@163.com>
   * @desc 设置控件坐标
   */
  _setCoordinate() {
    let coordinate = this._calCoordinate();
    this.el.target.css({
      left: coordinate.left,
      top: coordinate.top,
      width: parseInt(this.width) ? parseInt(this.width) + 'px' : this.width || 'auto',
      height: parseInt(this.height) ? parseInt(this.height) + 'px' : this.height || 'auto',
    });
  }

  // 显示页面遮罩      2019/08/28 09:29:15      --柯军
  _showMask() {
    this.mask = $(`<div class="h-drawer-mask" style="display: none; z-index: ${this.maxIndex + 1}; opacity: 0.5;"></div>`);
    $('body').append(this.mask);
    this.mask.fadeIn(200);
    this.mask.bind('mouseup', () => {
      this.mask.bind('contextmenu', (e) => {
        return false;
      })
      this.fnClose();
    })
  }

  // 删除遮罩      2019/08/28 09:29:09      --柯军
  _hideMask() {
    this.mask.fadeOut(200);
  }

  /**
   * @createtime 2019/09/12 15:53:58
   * @author 柯军 <arthaskj@163.com>
   * @desc 页面关闭
   */
  fnClose() {
    this.onClose && this.onClose(this);
    this._hideTarget();
    this._hideMask();
    setTimeout(() => {
      this.el.target.remove();
      this.mask.remove();
    }, 200);
  }

  _hideTarget() {
    this.el.target.fadeOut(200);
  }

  _getTemplate() {
    return `<div class="h-popover h-popover-placement-top" style="left: 309px;top: 379px;transform-origin: 50% 103px;z-index:${this.maxIndex + 2};">
              <div class="h-popover-content">
                <div class="h-popover-arrow"></div>
                <div class="h-popover-inner" role="tooltip">
                  <div>
                  ${this.title ? `<div class="h-popover-header">
                                    <i class="fa ${this.icon || 'fa-edit'}"></i>
                                    <div class="h-popover-title">
                                      ${this.title}
                                    </div>
                                  </div>` : ''}
                    
                    <div class="h-popover-inner-content">
                      <div class="h-popover-message"></div>
                    </div>
                    <div class="h-popover-buttons"></div>

                  </div>
                </div>
              </div>
            </div>`
  }
}
```



#### vue右键菜单

[转自掘金水君子](https://juejin.im/post/5dc14607f265da4d432a3cf6)

> 组件封装

```js
<template>
	<div v-if="show"
	     class="menu_style" 
	     :style="{top:y+'px',left:x+'px'}">
		<ul>
			<!-- 分别传递事件给父元素调用 -->
			<li @click="()=>{$emit('open')}">打开</li>
			<li @click="()=>{$emit('update')}">编辑</li>
			<li @click="()=>{$emit('del')}">删除</li>
		</ul>
	</div>
</template>
<script>
	export default{
		name:'right_menu',
		props:{
			x:{
				type:[Number],
				default:0
			},
			y:{
				type:[Number],
				default:0
			},
			showMenu:{
				type:[Boolean],
				default:false
			}
		},
		data(){
			return {
				show:false,
			}
		},
		methods:{
			// 点击别处时隐藏目录，并传递一个关闭事件
			closeMenu(e){
				this.show = false;
				this.$emit("close",false)
			},
		},
		mounted(){
			// 监听body上的点击
			document.querySelector("body").addEventListener("click",this.closeMenu)
		},
		beforeDestroy(){
			// 移除监听
			document.querySelector("body").removeEventListener("click",this.closeMenu)
		},
		watch:{
			// 监听，保持显示状态与父元素一致
			showMenu(val){
				this.show = val;
			}
		},
	}
</script>
<style scoped>
	.menu_style{
		position: absolute;
		width: 150px;
		background-color: #fff;
		border-radius: 2px;
		box-shadow: 2px 2px 14px #d0d0d0;
	}
	.menu_style>ul>li{
		text-indent: 25px;
		height: 38px;
		line-height: 38px;
		border-bottom: 1px dashed #f0f0f0;
		cursor: pointer;
	}
	.menu_style>ul>li:hover{
		background: #E0EEFF;
	}
</style>

```

> 组件使用

```js
<template>
    <div class="modal_data_box">
        <ul>
            <li class="role_list" 
                v-for="(item,index) in role" 
                :key="index"
                @contextmenu.prevent="(e)=>{
                    x_index = e.layerX;
                    y_index = e.layerY;
                    ctrlId = item.id;
                    showMenu = true;
                }">
                <img :src="item.head_portrait" alt="">
                <p>{{item.name}}</p>
            </li>
        </ul>
	<!--组件-->
	<right-menu :x="x_index"
	            :y="y_index" 
	            :showMenu="showMenu"
	            @close="closeMenu"
	            @open="openDetail"
	            @del="delAttr"
	            @update="updateArr">
	</right-menu>
    </div>
</template>
<script>
    // 导入组件
	import rightMenu from '../module/right_menu.vue';
	export default{
		name:"roleModal",
		components:{rightMenu},
		data(){
			return {
				x_index:0,
				y_index:0,
				ctrlId:'',
				showMenu:false,
				role:[],
			}
		},
		methods:{
		    //关闭回调  
		    closeMenu(state){
		        console.log('关闭')
		        this.showMenu = state;
		    },
		    //打开详情回调 			
		    openDetail(){
		        console.log('编辑')
		    },
		    //删除回调 			
		    delAttr(){
		        console.log('删除')
		    },
		    //编辑回调 			
		    updateArr(){
		        console.log('编辑')
		    },
		}
	}
</script>

```

