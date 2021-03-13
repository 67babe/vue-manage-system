<template>
	<div>
		<div class="crumbs">
			<el-breadcrumb separator="/">
				<el-breadcrumb-item>
					<i class="el-icon-lx-cascades"></i> 日志管理
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
<el-input v-model="query.name" placeholder="请输入" class="handle-input mr10" @keydown.enter.native="handleSearch"></el-input>				<el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>
			</div>
			<el-table :data="tableData" border class="table" ref="multipleTable" header-cell-class-name="table-header"
			 @selection-change="handleSelectionChange">
				<el-table-column type="selection" width="55" align="center"></el-table-column>
				<el-table-column prop="id" label="任务ID" width="100" align="center"></el-table-column>
				<el-table-column prop="description" label="任务描述" align="center"></el-table-column>
				<el-table-column prop="date" label="调度时间" align="center">
				</el-table-column>
				<el-table-column prop="result" label="调度结果" align="center">
				</el-table-column>
				<el-table-column label="状态" align="center">
					<template slot-scope="scope">
						<el-tag :type="scope.row.state==='成功'?'success':(scope.row.state==='失败'?'danger':'')">{{scope.row.state}}</el-tag>
					</template>
				</el-table-column>


				<el-table-column label="操作" width="180" align="center">
					<template slot-scope="scope">
						<el-button type="text" icon="el-icon-edit" @click="handleEdit(scope.$index, scope.row)">查看详情</el-button>

					</template>
				</el-table-column>
			</el-table>
			<div class="pagination">
				<el-pagination background layout="total, prev, pager, next" :current-page="query.pageIndex" :page-size="query.pageSize"
				 :total="pageTotal" @current-change="handlePageChange"></el-pagination>
			</div>
		</div>

		<!-- 编辑弹出框 -->
		<el-dialog title="日志记录" :visible.sync="editVisible" width="30%">
			<el-form ref="form" :model="form" label-width="70px">
				<span>这是一条日志记录</span>
			</el-form>
		</el-dialog>
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
					pageSize: 5
				},
				tableData: [],
				multipleSelection: [],
				delList: [],
				editVisible: false,
				pageTotal: 0,
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
					console.log(res);
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
					//this.getData();
				}
			},
			// 重置操作
			Reset() {
				this.getData();
				this.query.name = '';
			},
			// 编辑操作
			handleEdit(index, row) {
				this.idx = index;
				this.form = row;
				this.editVisible = true;
			},
			// 多选操作
			handleSelectionChange(val) {
				this.multipleSelection = val;
			},
			// 分页导航
			handlePageChange(val) {
				this.$set(this.query, 'pageIndex', val);
				this.getData();
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
