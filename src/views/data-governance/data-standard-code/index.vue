<template>
	<el-dialog v-model="visible" title="码表数据" :close-on-click-modal="false">
		<el-form :inline="true" :model="state.queryForm" @keyup.enter="getDataList()">
			<el-form-item>
			  <el-input v-model="state.queryForm.dataId" placeholder="id"></el-input>
			</el-form-item>
			<el-form-item>
			  <el-input v-model="state.queryForm.dataName" placeholder="name"></el-input>
			</el-form-item>
			<el-form-item>
				<el-button @click="getDataList()">查询</el-button>
			</el-form-item>
			<el-form-item>
				<el-button type="primary" @click="addOrUpdateHandle()">新增</el-button>
			</el-form-item>
			<el-form-item>
				<el-button type="danger" @click="deleteBatchHandle()">删除</el-button>
			</el-form-item>
		</el-form>
		<el-table v-loading="state.dataListLoading" :data="state.dataList" border style="width: 100%" @selection-change="selectionChangeHandle">
			<el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
			<!-- <el-table-column prop="id" label="主键id" header-align="center" align="center"></el-table-column> -->
			<!-- <el-table-column prop="standardId" label="标准码表id" header-align="center" align="center"></el-table-column> -->
			<el-table-column prop="dataId" label="标准编号" header-align="center" align="center"></el-table-column>
			<el-table-column prop="dataName" label="标准名称" header-align="center" align="center"></el-table-column>
			<fast-creator-column prop="creator" label="创建者" header-align="center" align="center"></fast-creator-column>
			<el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
			<el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
				<template #default="scope">
					<el-button type="primary" link @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
					<el-button type="primary" link @click="deleteBatchHandle(scope.row.id)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>
		<el-pagination
			:current-page="state.page"
			:page-sizes="state.pageSizes"
			:page-size="state.limit"
			:total="state.total"
			layout="total, sizes, prev, pager, next, jumper"
			@size-change="sizeChangeHandle"
			@current-change="currentChangeHandle"
		>
		</el-pagination>

		<!-- 弹窗, 新增 / 修改 -->
		<add-or-update ref="addOrUpdateRef" @refreshDataList="getDataList"></add-or-update>
	</el-dialog>
</template>

<script setup lang="ts">
import { useCrud } from '@/hooks'
import { reactive, ref } from 'vue'
import AddOrUpdate from './add-or-update.vue'
import { IHooksOptions } from '@/hooks/interface'

const visible = ref(false)
const state: IHooksOptions = reactive({
	createdIsNeed: false,
	dataListUrl: '/data-governance/standard-code/page',
	deleteUrl: '/data-governance/standard-code',
	queryForm: {
	}
})

const init = (standardId: any) => {
	visible.value = true
	state.queryForm.standardId = standardId
	getDataList()
}

const addOrUpdateRef = ref()
const addOrUpdateHandle = (id?: number) => {
	addOrUpdateRef.value.init(id, state.queryForm.standardId)
}

defineExpose({
	init
})

const { getDataList, selectionChangeHandle, sizeChangeHandle, currentChangeHandle, deleteBatchHandle } = useCrud(state)
</script>
