<template>
	<el-card body-style="height: calc(100vh - 170px );">
		<el-form :inline="true" :model="state.queryForm">
			<el-form-item>
				<el-select v-model="state.queryForm.typeId"
						  clearable
						  filterable
						  @change="labelTypeChange"
				          placeholder="标签类型">
				  <el-option v-for="(item,index) in labelTypeList"
				             :key="item.id"
				             :label="`[${item.id}]${item.name}`"
				             :value="item.id">
				  </el-option>
				</el-select>
			</el-form-item>
			<el-form-item>
				<el-select @change="labelIdChange" v-model="state.queryForm.labelId" filterable clearable placeholder="前选择标签" style="width: 200px">
				    <el-option
				      v-for="item in labels"
				      :key="item.id"
				      :label="item.name"
				      :value="item.id"
				    >
				      <div>
				        <!-- <el-tag :color="item.color" style="margin-right: 8px;" size="small" /> -->
				        <span :style="{ color: item.color }">{{ item.name }}</span>
				      </div>
				    </el-option>
				</el-select>
			</el-form-item>
		</el-form>
		<el-empty v-show="!state.queryForm.labelId" description="请选择标签查询" ></el-empty>
		<div v-show="state.queryForm.labelId">
			<el-form :inline="true">
				<el-form-item v-for="(item,index) in state.columns">
					<el-date-picker
						 v-if="item.filedTypeClassName == 'java.sql.Timestamp'"	
						 style="margin-left: 8px;"
						 v-model="item.conditionVal"
						 type="datetime"
						 :placeholder="item.remarks?`${item.fieldName}(${item.remarks})`:`${item.fieldName}`"
						 format="YYYY-MM-DD hh:mm:ss"
						 value-format="YYYY-MM-DD hh:mm:ss"
					 />
					<el-date-picker
					 v-if="item.filedTypeClassName == 'java.sql.Time'"	
					 style="margin-left: 8px;"
					 v-model="item.conditionVal"
					 type="datetime"
					 :placeholder="item.remarks?`${item.fieldName}(${item.remarks})`:`${item.fieldName}`"
					 format="hh:mm:ss"
					 value-format="hh:mm:ss"
				  />
					  <el-date-picker
						 v-if="item.filedTypeClassName == 'java.sql.Date'"
						 style="margin-left: 8px;"
						 v-model="item.conditionVal"
						 type="datetime"
						 :placeholder="item.remarks?`${item.fieldName}(${item.remarks})`:`${item.fieldName}`"
						 format="YYYY-MM-DD"
						 value-format="YYYY-MM-DD"
					  />
					<el-input v-if="item.filedTypeClassName != 'java.sql.Timestamp' && item.filedTypeClassName != 'java.sql.Time' && item.filedTypeClassName != 'java.sql.Date'" v-model="item.conditionVal" :placeholder="item.remarks?`${item.fieldName}(${item.remarks})`:`${item.fieldName}`"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button @click="adHocQuery()" type="primary">即席查询</el-button>
				</el-form-item>
			</el-form>
			<!-- 表格数据 -->
			<el-table v-loading="state.dataListLoading" :data="state.dataList" border style="width: 100%">
				 <el-table-column
					 show-overflow-tooltip
					 :prop="column.fieldName"
					 :label="column.remarks?`${column.fieldName}(${column.remarks})`:`${column.fieldName}`"
					 v-for="(column, index) in state.columns"
					 :key="index"
				 >
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
		</div>
	</el-card>
</template>

<script setup lang="ts" name="Data-governanceLabel-queryIndex">
import { useCrud } from '@/hooks'
import { reactive, ref, onMounted } from 'vue'
import { getLabelTypesApi, getLabelsApi, getColumnsByLabelIdApi} from '@/api/data-governance/label'
import { IHooksOptions } from '@/hooks/interface'

onMounted(()=>{
	getLabelTypesApi().then(res=>{
		labelTypeList.value = res.data
	})
	getLabelsApi(null).then(res=> {
		labels.value = res.data
	})
})

const labelTypeList = ref([])
const labels = ref([])

const labelTypeChange = (typeId) => {
	if(typeId) {
		getLabelsApi(typeId).then(res=> {
			labels.value = res.data
		})
	} else {
		getLabelsApi(null).then(res=> {
			labels.value = res.data
		})
	}
}

const labelIdChange = (labelId) => {
	if(labelId) {
		getColumnsByLabelIdApi(labelId).then(res=>{
			state.queryForm.labelId = labelId
			state.columns = res.data
			adHocQuery()
		})
	}
}

const state: IHooksOptions = reactive({
	requestMethod: 'post',
	createdIsNeed: false,
	dataListUrl: '/data-governance/label/table-data',
	queryForm: {
		typeId: '',
		labelId: '',
		conditionConfigs: [],
	},
	columns: [],
})

const adHocQuery = () => {
	state.queryForm.conditionConfigs = state.columns
	state.dataList = []
	getDataList()
}


const { getDataList, selectionChangeHandle, sizeChangeHandle, currentChangeHandle, deleteBatchHandle } = useCrud(state)

</script>

<style scoped>

</style>