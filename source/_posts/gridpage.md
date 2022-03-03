title: 封装Element的Table和Pagination
category: 技能
date: <文章日期> [YYYY-MM-DD]
index_img: /images/gridpage.assets/image-20210408155516838.png

---

封装Element的Table和Pagination

<!--more-->

# 封装Element的Table和Pagination

## 封装

```js
// grid-page
<template>
	<section>
		<section v-if="showToolbar" class="toolbar">
			<el-button @click="handleQuery()" type="primary" icon="el-icon-search" size="small">查询</el-button>
		</section>
		<el-table
			:data="tableList"
			height="450px"
			stripe
			:size="size"
			:border="isBorder"
			@select="select"
			@select-all="selectAll"
			:defaultSelections="defaultSelections"
			fit
			ref="cesTable"
		>
			<el-table-column v-if="isSelection" type="selection" align="center"></el-table-column>
			<el-table-column v-if="isIndex" label="序号" prop="iid" type="index"></el-table-column>
			<el-table-column
				v-for="item in columns"
				:key="item[columnsKey]"
				:prop="item.prop"
				:label="item.label"
				:width="item.width"
				:align="item.align"
				:fixed="item.fixed"
				:show-overflow-tooltip="item['show-overflow-tooltip']"
				:render-header="item.require ? renderHeader : null"
			>
				<template slot-scope="scope">
					<!-- html -->
					<span v-if="item.type === 'Html'" v-html="item.html(scope.row)"></span>
					<!-- 按钮 -->
					<span v-if="item.type === 'Button'">
						<span v-for="btn in item.btnList" :key="btn.key">
							<el-button
								v-if="!btn.isShow || (btn.isShow && btn.isShow(scope.row))"
								:disabled="btn.isDisabled && btn.isDisabled(scope.row)"
								:type="btn.type"
								:size="btn.size || size"
								:icon="btn.icon"
								@click="btn.handle(scope.row)"
							>
								{{ btn.label }}
							</el-button>
						</span>
					</span>

					<!-- 开关 -->
					<el-switch
						v-if="item.type === 'Switch'"
						v-model="scope.row[item.prop]"
						:active-value="item.activeValue"
						:inactive-value="item.inactiveValue"
						:active-color="item.activeColor"
						:disabled="item.isDisabled && item.isDisabled(scope.row)"
						@change="(v) => item.change && item.change(v, scope.row)"
					></el-switch>

					<!-- 默认 -->
					<span
						v-if="!item.type"
						:style="item.itemStyle && item.itemStyle(scope.row)"
						:class="item.itemClass && item.item.itemClass(scope.row)"
					>
						{{ (item.formatter && item.formatter(scope.row)) || scope.row[item.prop] }}
					</span>
				</template>
			</el-table-column>
		</el-table>
		<el-pagination
			v-if="showPagination"
			@size-change="handleSizeChange"
			@current-change="handleCurrentChange"
			:current-page="currentPage"
			:page-sizes="[30, 50, 100]"
			:page-size="pageSize"
			layout="total, sizes, prev, pager, next, jumper"
			:total="total"
			class="custom-pagination"
		>
		</el-pagination>
	</section>
</template>

<script>
export default {
	name: 'grid-page',
	props: {
		showToolbar: {
			type: Boolean,
			default: true,
		},
		showPagination: {
			type: Boolean,
			default: true,
		},
		isSelection: {
			type: Boolean,
			default: true,
		},
		isIndex: {
			type: Boolean,
			default: true,
		},
		isBorder: {
			type: Boolean,
			default: true,
		},
		size: {
			type: String,
			default: 'mini',
		},
		columnsKey: {
			type: String,
			default: 'id',
		},
		columns: {
			type: Array,
			default() {
				return [];
			},
		},
		tableList: {
			type: Array,
			default() {
				return [];
			},
		},
		defaultSelections: { type: [Array, Object], default: () => null },
		pagination: {
			type: Object,
			default: () => {
				return {
					pageSize: 30,
					currentPage: 1,
					total: 0,
				};
			},
		},
	},
	data() {
		return {
			dataItem: {},
			currentPage: this.pagination.currentPage,
			totalPage:
				this.total % this.pageSize === 0 ? this.total / this.pageSize : this.total / this.pageSize + 1,
			total: this.pagination.total,
			pageSize: this.pagination.pageSize,
		};
	},
	watch: {
		pagination(newV) {
			this.total = newV.total;
			return newV;
		},
		defaultSelections(val) {
			this.$nextTick(function () {
				if (Array.isArray(val)) {
					val.forEach((row) => {
						this.$refs.cesTable.toggleRowSelection(row);
					});
				} else {
					this.$refs.cesTable.toggleRowSelection(val);
				}
			});
		},
	},
	mounted() {},
	methods: {
		handleQuery() {
			this.$emit('onQuery');
		},
		handleSizeChange(val) {
			this.pageSize = val;
			const pagination = {
				pageSize: this.pageSize,
				currentPage: this.currentPage,
				totalPage: this.totalPage,
				total: this.total,
			};
			this.$emit('onSizeChange', pagination);
		},
		handleCurrentChange(val) {
			this.currentPage = val;
			const pagination = {
				pageSize: this.pageSize,
				currentPage: this.currentPage,
				totalPage: this.totalPage,
				total: this.total,
			};
			this.$emit('onSizeChange', pagination);
		},
		// 表格勾选
		select(rows, row) {
			this.$emit('select', rows, row);
		},
		// 全选
		selectAll(rows) {
			this.$emit('select', rows);
		},
		renderHeader(h, obj) {
			return h('span', { class: 'ces-table-require' }, obj.column.label);
		},
	},
};
</script>

<style scoped></style>

```



## 使用

```js
// 应用
<template>
	<div>
		<div>
			<el-button @click="fnQuery()" type="primary" icon="el-icon-search" size="small">查询</el-button>
			<el-button
				@click="handleClearCache()"
				type="primary"
				icon="el-icon-refresh"
				size="small"
				style="float: right; margin-bottom: 10px"
				>刷新缓存</el-button
			>
		</div>
		<grid-page
			:tableList="tableList"
			:columns="columns"
			:pagination="pagination"
			:is-selection="false"
			is-index
			:show-toolbar="false"
			columnsKey="iid"
			@onSizeChange="handleSizeChange"
		></grid-page>
		<button-detail
			:visible="show"
			:dataItem="dataItem"
			title="按钮权限配置"
			@onSubmit="
				() => {
					this.show = false;
					this.fnQuery();
				}
			"
			@onClose="show = false"
		></button-detail>
	</div>
</template>

<script>
import ButtonDetail from './button-detail.vue';
import GridPage from '../../../components/grid-page/index.vue';
import { GetRuleConfigs, ButtonUpdateStatus, ClearCache } from '../api';

export default {
	name: 'button-config',
	components: {
		ButtonDetail,
		GridPage,
	},
	data() {
		return {
			show: false,
			dataItem: {},
			columns: [
				{ label: '使用场景', prop: 'sceneType' },
				{ label: '操作名称', prop: 'operationName' },
				{ label: '操作码', prop: 'operationCode' },
				{ label: '业务类型', prop: 'dealType' },
				{ label: '交易方式', prop: 'dealMethod' },
				{ label: '表达式', prop: 'expression', 'show-overflow-tooltip': true },
				{ label: '备注', prop: 'remark' },
				{
					label: '状态',
					prop: 'status',
					type: 'Switch',
					align: 'center',
					activeValue: 0,
					inactiveValue: 1,
					activeColor: '#13ce66',
					change: this.handleOnSwitchChange,
				},
				{
					label: '操作',
					type: 'Button',
					align: 'center',
					btnList: [{ type: 'text', label: '编辑', handle: this.editAccount }],
				},
			],
			tableList: [],
			pagination: {
				currentPage: 1,
				totalPage: 1,
				total: 0,
				pageSize: 30,
			},
			context: {
				trdPdCode: 'common', // 产品码
				trdRole: 'order_list', // 参与角色
				opSource: null, // 操作源
				opSourceNo: null, // 操作源编号
			},
		};
	},
	mounted() {
		this.fnQuery();
	},
	methods: {
		fnQuery(params) {
			this.handleGetRuleConfigs(params);
		},
		handleGetRuleConfigs(v = {}) {
			const { currentPage, pageSize } = v;
			const params = {
				extendInfo: null,
				filters: {},
				page: currentPage,
				pageSize,
				skip: currentPage * (pageSize - 1),
				sorts: null,
				take: pageSize,
			};

			GetRuleConfigs(params).then((res) => {
				const { Data, Total } = res;
				this.pagination = {
					...this.pagination,
					total: Total,
				};
				this.tableList = Data;
				// this.currentPage = Start % PageSize === 0 ? Start / PageSize : Start / PageSize + 1;
				// this.totalPage = Total % PageSize === 0 ? Total / PageSize : Total / PageSize + 1;
				// this.total = Total;
				// this.pageSize = PageSize;
			});
		},

		handleOnSwitchChange(status, { iid }) {
			ButtonUpdateStatus({ iid, status }).then((res) => {
				if (res) {
					this.fnQuery();
					this.$message.success(status ? '【禁用】成功' : '【启用】成功');
				} else {
					this.$message.warning('操作失败');
				}
			});
		},
		editAccount(row) {
			this.dataItem = row;
			this.show = true;
		},
		handleSizeChange(pagination) {
			this.pagination = pagination;
			this.fnQuery(pagination);
		},
		handleClearCache() {
			ClearCache({ cacheKey: 'rule' }).then((res) => {
				if (res) {
					this.$message.success('缓存刷新成功!');
					this.fnQuery();
				} else {
					this.$message.warning('缓存刷新失败!');
				}
			});
		},
	},
};
</script>

<style scoped></style>

```



## 效果

![image-20210408155516838](/images/gridpage.assets/image-20210408155516838.png)