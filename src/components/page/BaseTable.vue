<template>
	<div>
		<div class="crumbs">
			<el-breadcrumb separator="/">
				<el-breadcrumb-item>
					<i class="el-icon-lx-cascades"></i> 任务管理
				</el-breadcrumb-item>
			</el-breadcrumb>
		</div>
		<div class="container">
			<div class="handle-box">
				<el-button type="primary" icon="el-icon-refresh" class="handle-del mr10" @click="Reset">重置</el-button>
				<el-select v-model="query.category" placeholder="任务" class="handle-select mr10">
					<el-option key="1" label="任务ID" value="任务ID"></el-option>
					<el-option key="2" label="任务描述" value="任务描述"></el-option>
				</el-select>
				<el-input v-model="query.name" placeholder="请输入" class="handle-input mr10" @keydown.enter.native="handleSearch"></el-input>
				<el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>
			</div>
			<el-table :data="tableData" border class="table" ref="multipleTable" header-cell-class-name="table-header"
			 @selection-change="handleSelectionChange">
				<el-table-column type="selection" width="55" align="center"></el-table-column>
				<el-table-column prop="id" label="任务ID" width="100" align="center"></el-table-column>
				<el-table-column prop="description" label="任务描述" align="center"></el-table-column>
				<el-table-column label="操作" width="180" align="center">
					<template slot-scope="scope">
						<el-switch v-model="scope.row.status" :inactive-value="0" active-color="#13ce66" inactive-color="#ff4949"
						 active-text="开启" inactive-text="关闭">
						</el-switch>
					</template>
				</el-table-column>
			</el-table>
			<div class="pagination">
				<el-pagination background layout="total, prev, pager, next" :current-page="query.pageIndex" :page-size="query.pageSize"
				 :total="pageTotal" @current-change="handleCurrentChange"></el-pagination>
			</div>
		</div>

	</div>
</template>

<script>
	import {
		fetchData
	} from '../../api/index';
	export default {
		name: 'basetable',
		data() {
			return {
				query: {
					category: '', //搜索类别
					description: '', //任务描述
					pageIndex: 0, //当前页码
					pageSize: 5,
				},
				tableData: [],
				multipleSelection: [],
				pageTotal: 10,
				form: {},
				idx: -1,
				id: -1
			};
		},
		created() {
			this.getData();
		},
		methods: {
			// 获取 easy-mock 的模拟数据
			getData() {
				fetchData(this.query).then(res => {

					this.tableData = res.list;
					this.pageTotal = res.pageTotal || 50;
				});
			},
			handleSearch() {
				var tmp = []
				if (this.query.category == "任务ID") {

					this.tableData.forEach(item => {
						if (item.id == this.query.name) {
							tmp.push(item)
						}
					})
					this.tableData = tmp

				}
				console.log(tmp)

				if (this.query.category == "任务描述") {
					this.tableData.forEach(item => {
						if (item.description == this.query.name) {
							tmp.push(item)
						}
					})
					this.tableData = tmp
				}
			},

			// 重置操作
			Reset() {
				this.getData();
				this.query.name = '';
			},
			// 多选操作
			handleSelectionChange(val) {
				this.multipleSelection = val;
			},

			// 分页导航
			handleCurrentChange(val) {
				console.log(`当前页: ${val}`);
				const _this = this;
				_this.getUrl(val)
			}
		}
	};
</script>

<style scoped>
	.handle-box {
		margin-bottom: 20px;
	}

	.handle-select {
		width: 120px;
	}

	.handle-input {
		width: 300px;
		display: inline-block;
	}

	.table {
		width: 100%;
		font-size: 14px;
	}

	.red {
		color: #ff0000;
	}

	.mr10 {
		margin-right: 10px;
	}

	.table-td-thumb {
		display: block;
		margin: auto;
		width: 40px;
		height: 40px;
	}
</style>
