
title: SQL翻译项目(React、Antd、TypeScript)
category: 技能
date: 2021-03-16
index_img: /images/sql_translate.assets/image-20210316161752758.png

---

跟公司小伙伴参加了京东科技黑客马拉松大赛，参赛项目就是SQL的转换，我在其中担任前端开发、测试、美工宣传以及打酱油的角色~

<!--more-->

## SQL翻译项目(React、Antd、TypeScript)

跟公司小伙伴参加了京东科技黑客马拉松大赛，参赛项目就是SQL的转换，我在其中担任前端开发、测试、美工宣传以及打酱油的角色~
整个项目中前端的工作量不是很大，只要负责基础的展示和交互就行。

这个项目业务代码就两个文件，So基本上代码都在一个文件内，没有进行拆分~

![image-20210316161752758](/images/sql_translate.assets/image-20210316161752758.png)

![image-20210316161824121](/images/sql_translate.assets/image-20210316161824121.png)

```js
import React, { createRef } from 'react';
import {
	Form,
	Select,
	Tooltip,
	Collapse,
	Table,
	Space,
	Switch,
	Alert,
	message,
	Spin,
	Button,
} from 'antd';
import {
	QuestionCircleOutlined,
	SettingOutlined,
	DeleteOutlined,
	PlusOutlined,
	EditOutlined,
	CopyOutlined,
	// DownloadOutlined,
	HistoryOutlined,
} from '@ant-design/icons';
import copy from 'copy-to-clipboard';
// import sqlFormatter from 'sql-formatter';
import { UnControlled as CodeMirror } from 'react-codemirror2';
import { v4 as uuidV4 } from 'uuid';
import { translate } from './apis';
import { dialectDataSource, themeDataSource, strategyDataSource } from '../../utils/dic';
import { setData2LocalStorage, getDataFromLocalStorage } from '../../utils/functions';
import { formatDate } from '../../utils/format';
import { IDefaultDataItem, IDefaultDataItemInt } from '../../utils/interface';
import { IDetailData } from './interface';
import ConfigDetail from '../sql-config/components/config-detail/config-detail';
import Index from './components/sql-history';
// 编辑器引用插件和主题
import 'codemirror/lib/codemirror.css';
import 'codemirror/theme/3024-day.css';
import 'codemirror/theme/3024-night.css';
import 'codemirror/theme/monokai.css';
import 'codemirror/theme/oceanic-next.css';
import 'codemirror/theme/ayu-mirage.css';
import 'codemirror/lib/codemirror.js';
import 'codemirror/mode/sql/sql.js';
import 'codemirror/addon/fold/foldcode.js';
import 'codemirror/addon/fold/foldgutter.js';
import 'codemirror/addon/fold/xml-fold.js';
import 'codemirror/addon/fold/indent-fold.js';
import 'codemirror/addon/fold/markdown-fold.js';
import 'codemirror/addon/fold/comment-fold.js';
import 'codemirror/addon/selection/active-line';
import 'codemirror/addon/edit/closeBrackets';
import 'codemirror/addon/edit/matchBrackets';
import 'codemirror/addon/hint/show-hint.css';
import 'codemirror/addon/hint/show-hint';
import 'codemirror/addon/hint/sql-hint';
import s from './index.module.less';
import { IDataItem } from '../sql-config/interface';

const desc = '编辑框内单击‘Ctrl’自动补全代码';

const initialValues = {
	sourceType: 'MySQL',
	targetType: 'Oracle',
	strategy: -1,
	translateTableName: true,
	translateFieldName: true,
};

const defaultCodemirrorOption = {
	height: '100%',
	lineNumbers: true,
	autofocus: true,
	smartIndent: false,
	styleActiveLine: true,
	lineWrapping: true,
	foldGutter: true,
	matchBrackets: true,
	autocapitalize: true, // 自动大写
	spellcheck: true, // 拼写检查
	autocorrect: true, // 自动更正
	gutters: ['CodeMirror-linenumbers', 'CodeMirror-foldgutter'],
	extraKeys: { Ctrl: 'autocomplete' },
	mode: 'text/x-sql',
	autoCloseBrackets: true,
};

const customStyle = { display: 'inline-block', padding: '0 10px', marginBottom: '10px' };

const { Option } = Select;
const { Panel } = Collapse;

interface IProps {
	onCodeChange?: (code: string) => void;
}

interface IState {
	code: string;
	formatCode: string;
	theme: string;
	sourceSqlType: string;
	targetSqlType: string;
	defaultActiveKey: Array<number>;
	visible: boolean;
	historyVisible: boolean;
	historyDataSource?: Array<{ dateTime: string; sql: string }>;
	detailData?: IDataItem | object;
	tableMappings: Array<IDetailData>;
	operateType: string;
	errorMessage: string;
	messageHidden: boolean;
	spinning: boolean;
}

class SqlEditor extends React.Component<IProps, IState> {
	$form: React.RefObject<any> = createRef();
	translateTimer: any;

	constructor(props: IProps) {
		super(props);
		this.translateTimer = null;

		this.state = {
			visible: false,
			historyVisible: false,
			historyDataSource: [],
			code: '',
			formatCode: '',
			theme: 'oceanic-next',
			sourceSqlType: 'MySQL',
			targetSqlType: 'Oracle',
			defaultActiveKey: [1],
			detailData: undefined,
			tableMappings: [],
			operateType: 'add',
			errorMessage: '',
			messageHidden: true,
			spinning: false,
		};
	}

	/**
	 * 查询并格式化sql脚本
	 * @createTime 2020-09-05 16:44:33
	 * @param {string} code 编辑器内的脚本内容
	 * @param {string} sourceType 源数据库类型
	 * @param {string} targetType 目标数据库类型
	 */
	formatSql = (code: string, sourceType?: string, targetType?: string) => {
		if (!code) {
			// message.warning('请输入脚本SQL!');
			this.setState({
				formatCode: '',
			});
			return;
		}
		const { sourceSqlType, targetSqlType, tableMappings } = this.state;
		const params = {
			sourceType: sourceType || sourceSqlType,
			targetType: targetType || targetSqlType,
			transSql: code,
			tableMappings,
		};
		const { formatCode: oldCode } = this.state;
		this.$form.current
			.validateFields()
			.then((p: any) => {
				this.setState({ spinning: true });
				translate({ ...p, ...params })
					.then((res: any) => {
						const { data, isSuccess, message: Message } = res;
						if (isSuccess === true) {
							const formatCode = data;
							// console.log(sqlFormatter.format(data))
							// 完全相同的记录不计入历史数据
							if (oldCode !== formatCode && formatCode) {
								this.updateHistoryStorage(formatCode);
							}
							this.setState({
								// code: formatCode,
								formatCode,
								errorMessage: '',
								messageHidden: true,
								spinning: false,
							});
						} else {
							this.setState({
								formatCode: '',
								errorMessage: Message,
								messageHidden: false,
								spinning: false,
							});
						}
					})
					.catch(() => {
						this.setState({ spinning: false });
					});
			})
			.catch(() => {
				this.setState({ defaultActiveKey: [1] });
			});
	};

	/**
	 * 每次成功翻译后保存到浏览器本地缓存
	 * @createTime 2020-09-10 16:32:30
	 * @param {string} sql 格式化后的sql语句
	 */
	updateHistoryStorage = (sql: string) => {
		const storage = getDataFromLocalStorage('history');
		let data = [];
		const item = { dateTime: formatDate(new Date(), '', 'YYYY-MM-DD HH:mm:SS'), sql };
		if (storage) {
			data = JSON.parse(storage);
			const d = [];
			// 删除一天前的数据
			data.forEach((x: { dateTime: string }) => {
				const { dateTime } = x;
				if (new Date().getTime() - new Date(dateTime).getTime() <= 86400000) {
					d.push(x);
				}
			});
			d.unshift(item);
			data = d;
		} else {
			data.push(item);
		}
		setData2LocalStorage('history', data);
	};

	/**
	 * 编辑器随时编辑随时请求，设有防抖
	 * @createTime 2020-09-05 16:46:09
	 * @param editor 编辑器类型
	 * @param data 编辑器类型
	 * @param code 当前编辑内容
	 */
	updateCode = (editor: any, data: any, code: string) => {
		this.setState({
			code,
		});
		// 设置防抖
		if (this.translateTimer) clearTimeout(this.translateTimer);
		this.translateTimer = setTimeout(() => {
			this.formatSql(code);
		}, 1000);

		if (this.props.onCodeChange) {
			this.props.onCodeChange(code);
		}
	};

	/**
	 * 更新格式化后的内容到编辑中
	 * @param editor 编辑器类型
	 * @param data 编辑器类型
	 * @param code 格式化后的内容
	 */
	updateTargetCode = (editor: any, data: any, code: string) => {
		this.setState({
			formatCode: code,
		});
	};

	onThemeChange = (v: string) => {
		this.setState({
			theme: v,
		});
	};

	onSourceSqlChange = (v: string) => {
		this.setState({
			sourceSqlType: v,
		});
		const { code } = this.state;
		this.formatSql(code, v);
	};

	onTargetSqlChange = (v: string) => {
		this.setState({
			targetSqlType: v,
		});
		const { code } = this.state;
		this.formatSql(code, '', v);
	};

	/**
	 * 表单发生改变时触发翻译查询
	 * @createTime 2020-09-10 16:31:29
	 */
	onFormChange = () => {
		const { code } = this.state;
		this.formatSql(code, '');
	};

	/**
	 * 添加表字段配置
	 * @createTime 2020-09-05 13:37:09
	 */
	addTableMapping = () => {
		this.setState({ detailData: undefined, visible: true, operateType: 'add' });
	};

	/**
	 * 编辑表字段映射数据
	 * @createTime 2020-09-05 16:48:11
	 * @param {string} detailData 当前行数据
	 */
	editTableMapping = (detailData: IDetailData) => {
		this.setState({ detailData, visible: true, operateType: 'edit' });
	};

	/**
	 * 删除表字段映射数据
	 * @createTime 2020-09-05 16:59:30
	 * @param index 数据index
	 */
	deleteTableMapping = (index: number) => {
		let { tableMappings } = this.state;
		tableMappings = Array.from(tableMappings);
		tableMappings.splice(index, 1);
		this.setState({ tableMappings });
		const { code } = this.state;
		this.formatSql(code);
	};

	/**
	 * 字段映射数据提交的回调
	 * @createTime 2020-09-05 16:58:32
	 * @param data 回调的数据
	 * @param type 操作类型 { add-添加, edit-编辑 }
	 */
	onSubmit = (data: IDetailData, type: string) => {
		let { tableMappings } = this.state;
		tableMappings = Array.from(tableMappings);

		if (type === 'edit') {
			const { key } = data;
			const index = tableMappings.findIndex(x => x.key === key);
			tableMappings.splice(index, 1, data);
		} else if (type === 'add') {
			tableMappings.unshift(data);
		}
		this.setState({ tableMappings });
		const { code } = this.state;
		this.formatSql(code);
	};

	/**
	 * 弹出层关闭事件的回调函数
	 * @createTime 2020-09-10 16:30:09
	 * @param {any} opt visible
	 */
	onCancel = (opt: any) => {
		this.setState(opt);
	};

	/**
	 * 渲染展开的字段映射内容
	 * @createTime 2020-09-05 16:58:01
	 * @param record 当前行数据
	 */
	expandedRowRender = (record: IDetailData) => {
		let { fieldMappings } = record;
		fieldMappings = Array.from(fieldMappings);
		const expandedColumns = [
			{ title: '源字段', dataIndex: 'sourceField', key: 'sourceField' },
			{ title: '目标字段', dataIndex: 'targetField', key: 'targetField' },
		];

		return (
			<Table
				columns={expandedColumns}
				dataSource={fieldMappings}
				pagination={false}
				size="small"
				rowKey={() => uuidV4()}
			/>
		);
	};

	getColumns = () => {
		return [
			{ title: '源表名', dataIndex: 'sourceTableName', key: 'sourceTableName', width: '300px' },
			{ title: '目标表名', dataIndex: 'targetTableName', key: 'targetTableName', width: '300px' },
			{
				title: () => {
					return (
						<Space size="middle">
							<PlusOutlined
								title="新增表字段映射"
								style={{ color: '#1890ff' }}
								onClick={this.addTableMapping}
							/>
						</Space>
					);
				},
				dataIndex: 'operation',
				key: 'operation',
				width: '20px',
				align: 'center',
				render: (text: any, record: any, index: any) => {
					return (
						<Space size="middle">
							<EditOutlined
								title="编辑"
								style={{ color: '#1890ff' }}
								onClick={() => this.editTableMapping(record)}
							/>

							<DeleteOutlined
								title="删除"
								style={{ color: '#1890ff' }}
								onClick={() => this.deleteTableMapping(index)}
							/>
						</Space>
					);
				},
			},
		];
	};

	// 返回设置面板角标
	genExtra = () => {
		return (
			<SettingOutlined
				style={{
					fontSize: '20px',
				}}
			/>
		);
	};

	/**
	 * 将转换后的sql代码赋值到剪贴板
	 * @createTime 2020-09-08 15:12:50
	 */
	copySql2Clipboard = () => {
		const { formatCode } = this.state;
		if (!formatCode) {
			message.warning('无可复制内容!');
			return;
		}
		if (copy(formatCode)) {
			message.success('内容已复制到剪贴板!');
		} else {
			message.error('复制内容到剪贴板失败!');
		}
	};

	/**
	 * 从浏览器本地缓存中读取历史数据
	 * @createTime 2020-09-10 16:28:55
	 */
	showHistory = () => {
		const storage = getDataFromLocalStorage('history');
		if (storage) {
			const data = JSON.parse(storage);
			if (data && data.length) {
				this.setState({ historyVisible: true, historyDataSource: data });
			} else {
				message.warning('暂无历史数据!');
			}
		} else {
			message.warning('暂无历史数据!');
		}
	};

	/**
	 * 渲染表单部分,减少render方法长度
	 * @createTime 2020-09-09 20:55:55
	 */
	renderFormItem = () => {
		return (
			<>
				<Form.Item
					label="源数据库类型"
					name="sourceType"
					rules={[{ required: true, message: '缺失源数据库类型' }]}
					style={customStyle}
				>
					<Select style={{ width: '140px' }} onChange={this.onSourceSqlChange}>
						{dialectDataSource.map((x: IDefaultDataItem, i) => (
							<Option key={i.toString()} value={x.value}>
								{x.text}
							</Option>
						))}
					</Select>
				</Form.Item>

				<Form.Item
					label="目标数据库类型"
					name="targetType"
					rules={[{ required: true, message: '缺失目标数据库类型' }]}
					style={customStyle}
				>
					<Select style={{ width: '140px' }} onChange={this.onTargetSqlChange}>
						{dialectDataSource.map((x: IDefaultDataItem, i) => (
							<Option key={i.toString()} value={x.value}>
								{x.text}
							</Option>
						))}
					</Select>
				</Form.Item>
				<Form.Item label="转换策略" name="strategy" style={customStyle}>
					<Select style={{ width: '140px' }} onChange={this.onFormChange}>
						{strategyDataSource.map((x: IDefaultDataItemInt, i) => (
							<Option key={i.toString()} value={x.value}>
								{x.text}
							</Option>
						))}
					</Select>
				</Form.Item>
				<Form.Item
					label="表名策略转换"
					name="translateTableName"
					style={customStyle}
					valuePropName="checked"
				>
					<Switch
						checkedChildren="开启"
						unCheckedChildren="关闭"
						onChange={this.onFormChange}
						defaultChecked
					/>
				</Form.Item>
				<Form.Item
					label="字段策略转换"
					name="translateFieldName"
					style={customStyle}
					valuePropName="checked"
				>
					<Switch
						checkedChildren="开启"
						unCheckedChildren="关闭"
						defaultChecked
						onChange={this.onFormChange}
					/>
				</Form.Item>
				<Form.Item style={customStyle}>
					<Button type="primary" onClick={this.onFormChange}>
						执行
					</Button>
				</Form.Item>
			</>
		);
	};

	/**
	 * 渲染操作面板
	 * @createTime 2020-09-10 09:26:58
	 */
	renderOperatePanel = () => {
		const operateDataSource = [
			{ title: '复制', icon: <CopyOutlined />, onClick: this.copySql2Clipboard },
			{ title: '历史', icon: <HistoryOutlined />, onClick: this.showHistory },
			// { title: '下载', icon: <DownloadOutlined /> },
		];
		return (
			<div className={s['code-mirror-operate-panel']}>
				{operateDataSource.map((x, i) => {
					const { title, icon, onClick } = x;
					return (
						<div
							key={i.toString()}
							className={s['code-mirror-operate-item']}
							onClick={onClick}
							title={title}
						>
							{icon}
						</div>
					);
				})}
			</div>
		);
	};

	/**
	 * 更改主题设置（目前没有放出来）
	 * @createTime 2020-09-10 09:30:12
	 * @param {string} theme 主题名称
	 */
	renderChangeTheme = (theme: string) => {
		return (
			<div className={s.toolbar} hidden>
				更改主题:
				<Select className={s['theme-select']} onChange={this.onThemeChange} defaultValue={theme}>
					{themeDataSource.map((x: IDefaultDataItem, i) => (
						<Option key={i.toString()} value={x.value}>
							{x.text}
						</Option>
					))}
				</Select>
				<Tooltip className={s['tooltip-desc']} placement="right" title={desc}>
					<QuestionCircleOutlined style={{ color: '#ffc53d', fontSize: '18px' }} />
				</Tooltip>
			</div>
		);
	};

	/**
	 * 渲染弹出层
	 * @createTime 2020-09-10 16:27:33
	 */
	renderModal = () => {
		const { visible, historyVisible, detailData, historyDataSource, operateType } = this.state;
		return (
			<>
				{visible && (
					<ConfigDetail
						onSubmit={this.onSubmit}
						onCancel={() => this.onCancel({ visible: false })}
						detailData={detailData}
						operateType={operateType}
					/>
				)}
				{historyVisible && (
					<Index
						dataSource={historyDataSource}
						onCancel={() => this.onCancel({ historyVisible: false })}
					/>
				)}
			</>
		);
	};

	/**
	 * 渲染配置层
	 * @createTime 2020-09-10 16:27:55
	 */
	renderCollapse = () => {
		const { defaultActiveKey, tableMappings } = this.state;
		const columns = this.getColumns();
		return (
			<Collapse defaultActiveKey={defaultActiveKey}>
				<Panel header="编辑器配置" key="1" forceRender extra={this.genExtra()}>
					<div>
						<Form ref={this.$form} layout="inline" initialValues={initialValues}>
							{this.renderFormItem()}
						</Form>

						<div className={s['fields-grid-container']}>
							<div className={s['fields-grid']}>
								<span>表字段映射: </span>
							</div>
							<Table
								className="components-table-demo-nested"
								// eslint-disable-next-line @typescript-eslint/ban-ts-ignore
								// @ts-ignore
								columns={columns}
								size="small"
								expandable={{
									expandedRowRender: record => {
										return this.expandedRowRender(record);
									},
									rowExpandable: record => record.fieldMappings && !!record.fieldMappings.length,
								}}
								dataSource={tableMappings}
							/>
						</div>
					</div>
				</Panel>
			</Collapse>
		);
	};

	render() {
		const { formatCode, theme, errorMessage, messageHidden, spinning } = this.state;
		const options = { ...defaultCodemirrorOption, ...{ theme } };
		return (
			<div className={s['container-box']}>
				{this.renderCollapse()}
				{this.renderChangeTheme(theme)}
				<div className={s['code-mirror-box']}>
					<Alert
						message={
							<div>
								执行异常: <span style={{ color: '#f5222d' }}>{errorMessage || '--'}</span>
							</div>
						}
						type="warning"
						style={{ display: messageHidden ? 'none' : 'block' }}
					/>
					<div className={s.container}>
						<div className={s['container-div']}>
							<Spin size="large" spinning={spinning}>
								<CodeMirror
									className={s['source-editor']}
									value=""
									onChange={this.updateCode}
									options={options}
								/>
							</Spin>
						</div>
						<div className={`${s['code-mirror-display']} ${s['container-div']}`}>
							<CodeMirror
								className={s['target-editor']}
								value={formatCode}
								onChange={this.updateTargetCode}
								options={{
									...options,
									...{ readOnly: true, autofocus: false, styleActiveLine: false },
								}}
							/>
							{this.renderOperatePanel()}
						</div>
					</div>
				</div>
				{this.renderModal()}
			</div>
		);
	}
}

export default SqlEditor;

```



最后分享一下这次行程拍的照片~

![image-20210316163037841](/images/sql_translate.assets/image-20210316163037841.png)

![image-20210316163045876](/images/sql_translate.assets/image-20210316163045876.png)![image-20210316163050911](/images/sql_translate.assets/image-20210316163050911.png)![image-20210316163055527](/images/sql_translate.assets/image-20210316163055527.png)![image-20210316163100417](/images/sql_translate.assets/image-20210316163100417.png)![image-20210316163105803](/images/sql_translate.assets/image-20210316163105803.png)

![image-20210316163109285](/images/sql_translate.assets/image-20210316163109285.png)