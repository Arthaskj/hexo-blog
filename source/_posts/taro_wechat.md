title: Taro开发微信小程序(Taro、React、TypeScript)
category: 技能
tags: taro,微信,小程序
date: 2021-03-16
index_img: /images/taro_wechat.assets/image-20210316173301151.png

---

本来是打算用Taro写一个小程序的，但是，Taro的升级真的好变态。。。想要新加的功能就得升级，他需要相关的包一起升级，不升级的话项目根本起不来。。。试着升级到3.X.X,结果一堆保存。最后还是进行回滚才能正常启动。。。只这一次就让我放弃了这个框架。。。

<!--more-->

## Taro开发微信小程序(Taro、React、TypeScript)

本来是打算用Taro写一个小程序的，但是，Taro的升级真的好变态。。。想要新加的功能就得升级，他需要相关的包一起升级，不升级的话项目根本起不来。。。试着升级到3.X.X,结果一堆保存。最后还是进行回滚才能正常启动。。。只这一次就让我放弃了这个框架。。。

![image-20210316173301151](/images/taro_wechat.assets/image-20210316173301151.png)

### 一、App.tsx

```js
import Taro, { Component, Config } from '@tarojs/taro'
import { Provider } from '@tarojs/mobx'
import 'taro-ui/dist/style/index.scss'
import Index from './pages/index'

import page from './store'
import global from './store/global'

import './app.less'

// 如果需要在 h5 环境中开启 React Devtools
// 取消以下注释：
// if (process.env.NODE_ENV !== 'production' && process.env.TARO_ENV === 'h5')  {
//   require('nerv-devtools')
// }

const store = {
  global,
  page,
}

class App extends Component {

  componentDidMount () {}

  /**
   * 指定config的类型声明为: Taro.Config
   *
   * 由于 typescript 对于 object 类型推导只能推出 Key 的基本类型
   * 对于像 navigationBarTextStyle: 'black' 这样的推导出的类型是 string
   * 提示和声明 navigationBarTextStyle: 'black' | 'white' 类型冲突, 需要显示声明类型
   */
  config: Config = {
    pages: [
      'pages/index/index',
      'pages/dailySummary/index',
      'pages/todo/index',
      'pages/dailySummary/components/detail',
      'pages/index/components/detail',
    ],
    window: {
      backgroundTextStyle: 'light',
      navigationBarBackgroundColor: '#fff',
      navigationBarTitleText: 'WeChat',
      navigationBarTextStyle: 'black',
    },
  }

  componentDidShow () {}

  componentDidHide () {}

  componentDidCatchError () {}

  // 在 App 类中的 render() 函数没有实际作用
  // 请勿修改此函数
  render () {
    return (
	<Provider store={store}>
		<Index />
	</Provider>
    )
  }
}

Taro.render(<App />, document.getElementById('app'))

```

### 二、toto.tsx

```js
import Taro, { Component, Config } from '@tarojs/taro'
import { View } from '@tarojs/components';
import { AtSwipeAction, AtMessage, AtToast, AtCheckbox } from 'taro-ui';
import { observer, inject } from '@tarojs/mobx';
import KFab from '../../components/fab';
import { GetAll, DeleteById, Update, Add, ChangeStatus } from './apis';
import KEditor from '../../components/editor';
import { IDataItem, IState } from './IIndex';
import KTabBar from '../../components/tabbar';

interface IProps {
  global?: any
}

@inject('global')
@observer
class Todo extends Component<IProps> {

	constructor (props) {
		super(props)
		this.state = {
			showModal: false,
			listItems: [],
      toastMessage: '内容不能为空',
      toastOpen: false,
		}
  }

  state: IState

	componentDidMount() {
		this.fnQuery();
	}

  config: Config = {
		navigationBarTitleText: '待办',
	}

	fnQuery = () => {
    GetAll().then((res: Array<IDataItem>) => {
      // 更新导航栏待办数据number
      this.props.global.onSetDodoLength(res.length);
      this.setState({
        listItems: res,
      })
    })
  }

	openModal = (show: boolean) => {
		this.setState({
			showModal: show,
      activeId: '',
		})
	}

  onSaveTodo = (data: IDataItem) => {
    data.userId = this.props.global.userInfo.userId;
    data.userName = this.props.global.userInfo.userName;
    Add(data).then(() => {
      this.fnQuery();
      this.setState({
        showModal: false,
      })
    })
  }

  onUpdateTodo = (data: IDataItem) => {
    Update(data).then(() => {
      this.fnQuery();
      this.setState({
        showModal: false,
      })
    })
  }

  onSubmit = (content: string, data: IDataItem) => {
    if (!content) {
      this.setState({ toastOpen: true })
      setTimeout(() => {
        this.setState({ toastOpen: false })
      }, 2300)
      return false;
    }
    if (data && data._id) {
      data.content = content;
      this.onUpdateTodo(data);
    } else {
      this.onSaveTodo({ content });
    }
  }

  onSwipeClick = (data: IDataItem, e: { text: string }) => {
	  const { text } = e;
	  if (text === '删除') {
      this.deleteTodo(data);
    } else if (text === '编辑') {
	    this.onEditTodo(data);
    }
  }

  deleteTodo = (data: IDataItem) => {
    const { _id } = data;
    DeleteById({ _id }).then(() => {
      Taro.atMessage({
        'message': '删除成功',
        'type': 'success',
      })
      this.fnQuery();
    })
  }

  onEditTodo = (data = {}) => {
    this.setState({
      data,
      showModal: true,
    })
  }

  onHandleChange = (checkedList, _id) => {
    const { listItems, activeId } = this.state;
    if (activeId) {
      this.setState({ activeId: '' });
      return false;
    }
    const completed = checkedList.length === 1;
    listItems.forEach(x => {
      if (x._id === _id) {
        x.completed = completed;
      }
    })
    this.setState({
      listItems,
    })
    ChangeStatus({ _id, completed });
  }

  onFormatCheckboxData = (x: IDataItem) => {
    const { _id, content } = x;
    const str = content.toString();
    if (str.length <= 15) {
      return [{ value: _id, label: content }];
    } else if (str.length <= 50){
      return [{ value: _id, label: str.slice(0, 15) + '...', desc: content }];
    }
      return [{ value: _id, label: str.slice(0, 15) + '...', desc: str.slice(0, 50) + '...' }];

  }

	render () {
    return (
	<view>
		<View>
			{this.state.listItems.map((x, index) => (
				<AtSwipeAction
					customStyle={{ border:'none' }}
					isOpened={x._id === this.state.activeId}
					key={index.toString()}
					autoClose
					options={[
                    {
                      text: '编辑',
                      style: {
                        backgroundColor: '#1890ff',
                      },
                    },
                    {
                      text: '删除',
                      style: {
                        backgroundColor: '#FF4949',
                      },
                    },
                  ]}
					        onOpened={() => {
                    this.setState({ activeId: x._id });
                  }}
					        onClosed={() => {
                    this.setState({ activeId: '' });
                  }}
					        onClick={(e) => this.onSwipeClick(x, e)}
				>
					<AtCheckbox
						options={this.onFormatCheckboxData(x)}
						selectedList={x.completed ? [ x._id ] : []}
						onChange={(checkList) => this.onHandleChange(checkList, x._id)}
					/>
				</AtSwipeAction>
              ))}
		</View>
		<KFab onClick={() => { this.openModal(true); }} />
		{this.state.showModal && (
		<KEditor
			data={this.state.data}
			onSubmit={this.onSubmit}
			onClose={() => {this.openModal(false)}}
		/>
            )}
		<KTabBar current={1} />
		<AtMessage />
		<AtToast isOpened={this.state.toastOpen} text={this.state.toastMessage} duration={2000} />
	</view>
		)
	}
}

export default Todo

```



![image-20210316172822629](/images/taro_wechat.assets/image-20210316172822629.png)

![image-20210316172833759](/images/taro_wechat.assets/image-20210316172833759.png)

![image-20210316172919931](/images/taro_wechat.assets/image-20210316172919931.png)

![image-20210316172932064](/images/taro_wechat.assets/image-20210316172932064.png)