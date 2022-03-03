
title: webComponent开发
category: 技能
date: <文章日期> [YYYY-MM-DD]
index_img: /images/webComponent.assets/image-20210323102419990.png

---

Web Components 是一套不同的技术，允许您创建可重用的定制元素（它们的功能封装在您的代码之外）并且在您的web应用中使用它们。

<!--more-->

# webComponent
> Web Components 是一套不同的技术，允许您创建可重用的定制元素（它们的功能封装在您的代码之外）并且在您的web应用中使用它们。

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)

[阮一峰的网络日志](http://www.ruanyifeng.com/blog/2019/08/web_components.html)



## 生命周期

`constructor -> attributeChangedCallback -> connectedCallback`



## `<template>`标签

```html
<template id="t-switch-templete">
    <div role="switch" aria-checked="true" class="t-switch">
        <input type="checkbox" name="" true-value="true" class="t-switch__input">
        <span class="t-switch__core">
            <span class="t-switch-inner"></span>
        </span>
    </div>
</template>
```



## 添加样式

```css
<style>
.t-switch{display:-webkit-inline-box;display:-ms-inline-flexbox;display:inline-flex;-webkit-box-align:center;-ms-flex-align:center;align-items:center;position:relative;font-size:14px;line-height:20px;height:20px;vertical-align:middle}.t-switch.is-disabled .t-switch__core,.t-switch.is-disabled .t-switch__label{cursor:not-allowed}.t-switch__core,.t-switch__label{display:inline-block;cursor:pointer;vertical-align:middle}.t-switch__label{-webkit-transition:.2s;transition:.2s;height:20px;font-size:14px;font-weight:500;color:#303133}.t-switch__label.is-active{color:#409EFF}.t-switch__label--left{margin-right:10px}.t-switch__label--right{margin-left:10px}.t-switch__label *{line-height:1;font-size:14px;display:inline-block}.t-switch__input{position:absolute;width:0;height:0;opacity:0;margin:0}.t-switch__core{margin:0;position:relative;width:40px;height:20px;border:1px solid rgba(0,0,0,.25);outline:0;border-radius:10px;-webkit-box-sizing:border-box;box-sizing:border-box;background:rgba(0,0,0,.25);-webkit-transition:border-color .3s,background-color .3s;transition:border-color .3s,background-color .3s}.t-switch__core:after{content:"";position:absolute;top:1px;left:1px;border-radius:100%;-webkit-transition:all .3s;transition:all .3s;width:16px;height:16px;background-color:#FFF}.t-switch.is-checked .t-switch__core{border-color:#409EFF;background-color:#409EFF}.t-switch.is-checked .t-switch__core::after{left:100%;margin-left:-17px}.t-switch.is-disabled{opacity:.6}.t-switch--wide .t-switch__label.t-switch__label--left span{left:10px}.t-switch--wide .t-switch__label.t-switch__label--right span{right:10px}.t-switch .label-fade-enter,.t-switch .label-fade-leave-active{opacity:0}
.t-switch.is-checked .t-switch-success {
    border-color: #67C23A;
    background-color: #67C23A;
}
.t-switch.is-checked .t-switch-danger {
    border-color: #F56C6C;
    background-color: #F56C6C;
}
.t-switch.is-checked .t-switch-warning {
    border-color: #E6A23C;
    background-color: #E6A23C;
}
.t-switch .t-switch-inner{
    color: #fff;
    font-size: 12px;
    margin: -1px 7px 0 20px;
    display: block;
    transition: margin .2s;
}
.t-switch.is-checked .t-switch-inner{
    margin: -1px 25px 0 6px;
}
</style>
```



## Shadow DOM

我们不希望用户能够看到`<user-card>`的内部代码，Web Component 允许内部代码隐藏起来，这叫做 Shadow DOM，即这部分 DOM 默认与外部 DOM 隔离，内部任何代码都无法影响外部。

```js
this.attachShadow( { mode: 'close' } );
```





![image-20210323102419990](/images/webComponent.assets/image-20210323102419990.png)

```js
class TSwitch extends HTMLElement {
  constructor() {
    super();
    let templete = `<template id="t-switch-templete">
                      <style>
                       .t-switch{display:-webkit-inline-box;display:-ms-inline-flexbox;display:inline-flex;-webkit-box-align:center;-ms-flex-align:center;align-items:center;position:relative;font-size:14px;line-height:20px;height:20px;vertical-align:middle}.t-switch.is-disabled .t-switch__core,.t-switch.is-disabled .t-switch__label{cursor:not-allowed}.t-switch__core,.t-switch__label{display:inline-block;cursor:pointer;vertical-align:middle}.t-switch__label{-webkit-transition:.2s;transition:.2s;height:20px;font-size:14px;font-weight:500;color:#303133}.t-switch__label.is-active{color:#409EFF}.t-switch__label--left{margin-right:10px}.t-switch__label--right{margin-left:10px}.t-switch__label *{line-height:1;font-size:14px;display:inline-block}.t-switch__input{position:absolute;width:0;height:0;opacity:0;margin:0}.t-switch__core{margin:0;position:relative;width:40px;height:20px;border:1px solid rgba(0,0,0,.25);outline:0;border-radius:10px;-webkit-box-sizing:border-box;box-sizing:border-box;background:rgba(0,0,0,.25);-webkit-transition:border-color .3s,background-color .3s;transition:border-color .3s,background-color .3s}.t-switch__core:after{content:"";position:absolute;top:1px;left:1px;border-radius:100%;-webkit-transition:all .3s;transition:all .3s;width:16px;height:16px;background-color:#FFF}.t-switch.is-checked .t-switch__core{border-color:#409EFF;background-color:#409EFF}.t-switch.is-checked .t-switch__core::after{left:100%;margin-left:-17px}.t-switch.is-disabled{opacity:.6}.t-switch--wide .t-switch__label.t-switch__label--left span{left:10px}.t-switch--wide .t-switch__label.t-switch__label--right span{right:10px}.t-switch .label-fade-enter,.t-switch .label-fade-leave-active{opacity:0}
                       .t-switch.is-checked .t-switch-success {
                          border-color: #67C23A;
                          background-color: #67C23A;
                       }
                       .t-switch.is-checked .t-switch-danger {
                          border-color: #F56C6C;
                          background-color: #F56C6C;
                       }
                       .t-switch.is-checked .t-switch-warning {
                          border-color: #E6A23C;
                          background-color: #E6A23C;
                       }
                       .t-switch .t-switch-inner{
                          color: #fff;
                          font-size: 12px;
                          margin: -1px 7px 0 20px;
                          display: block;
                          transition: margin .2s;
                       }
                        .t-switch.is-checked .t-switch-inner{
                          margin: -1px 25px 0 6px;
                       }
                      </style>
                      <div role="switch" aria-checked="true" class="t-switch">
                        <input type="checkbox" name="" true-value="true" class="t-switch__input">
                        <span class="t-switch__core">
                          <span class="t-switch-inner"></span>
                        </span>
                      </div>
                    </template>`;
    let target_div = document.createElement("div");
    target_div.innerHTML = templete;
    // document.body.append(target_div);
    let shadow = this.attachShadow( { mode: 'open' } );

    let templateElem = target_div.querySelector('#t-switch-templete');
    let content = templateElem.content.cloneNode(true);
    this.container = content.querySelector('.t-switch');
    this.inputDom = this.container.querySelector('.t-switch__input');
    this.switchInner = this.container.querySelector('.t-switch-inner');
    const checkedText = this.getAttribute('checked');
    const isChecked = (this.hasAttribute('checked') || checkedText === 'true') && checkedText !== 'false';
    this.activeText = this.getAttribute('active-text') || '';
    this.inActiveText = this.getAttribute('inactive-text') || '';
    this.activeValue = isChecked;

    if (isChecked) {
      this.container.classList.add(`is-checked`);
      this.switchInner.innerText = this.activeText;
    } else {
      this.switchInner.innerText = this.inActiveText;
    }
    const disabledText = this.getAttribute('disabled');
    const isDisable = (this.hasAttribute('disabled') || disabledText === 'true') && disabledText !== 'true';
    this.inputDom.setAttribute('disabled', isDisable);
    this.disabled = isDisable;

    const type = this.getAttribute('type');
    if (type) content.querySelector('span').classList.add(`t-switch-${type}`);

    if (isDisable) this.container.classList.add(`is-disabled`);
    this.bindEvent();
    shadow.appendChild(content);
    target_div.remove();
  }

  changeStatus(isChecked) {
    if (isChecked) {
      this.container.classList.add('is-checked');
      this.switchInner.innerText = this.activeText;
      this.activeValue = true;
    } else {
      this.container.classList.remove('is-checked');
      this.switchInner.innerText = this.inActiveText;
      this.activeValue = false;
    }
  }

  bindEvent() {
    const me = this;
    this.container.addEventListener('click', function() {
      if (this.classList.contains('is-disabled')) return false;
      me.changeStatus(!this.classList.contains('is-checked'));
    });
  }

    // 监听下面属性的变化，发生变化会调用attributeChangedCallback方法
  static get observedAttributes() {
    return ['type', 'disabled', 'active-text', 'inactive-text'];
  }

    // 属性改变回调
  attributeChangedCallback(attr, oldVal, newVal) {
    // 属性变更时调用：三个参数对应属性名，旧值，新值
    switch (attr) {
      case 'type':
        this.container.querySelector('span').classList.remove(`t-switch-${oldVal}`);
        this.container.querySelector('span').classList.add(`t-switch-${newVal}`);
        break;
      case 'checked':
        if (newVal === null) {
          this.changeStatus(false);
        } else if (oldVal === null) {
          this.changeStatus(true);
        }
        break;
      case 'active-text':
        this.activeText = newVal;
        if (this.activeValue) {
          this.switchInner.innerText = newVal;
        }
        break;
      case 'inactive-text':
        this.inActiveText = newVal;
        if (!this.activeValue) {
          this.switchInner.innerText = newVal;
        }
        break;
      case 'disabled':
        if (newVal === null || newVal === 'false') {
          this.container.classList.remove(`is-${attr}`);
          this.inputDom.setAttribute('disabled', false);
          this.disabled = false;
        } else if (oldVal === null || newVal === 'true') {
          this.container.classList.add(`is-${attr}`);
          this.inputDom.setAttribute('disabled', true);
          this.disabled = true;
        }
        break;
      default:
        break;
    }
  }

  value(v) {
    if (typeof v === 'boolean') {
      const isChecked = this.container.classList.contains('is-checked');
      if (v) {
        !isChecked && this.changeStatus(true);
      } else {
        isChecked && this.changeStatus(false);
      }
    } else {
      return this.activeValue;
    }
  }
}
window.customElements.define('t-switch', TSwitch);


```

webComponent样式和事件只在`Shadow DOM`内部生效所以可以在任何语言框架下直接使用，vant的微信小程序UI框架就是用这种方式进行开发的。