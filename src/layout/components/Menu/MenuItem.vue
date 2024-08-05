<template>
	<el-sub-menu v-if="menu.children.length > 0" :key="menu.path" :index="menu.path">
		<template #title>
			<svg-icon v-if="showIcon" :icon="menu.meta.icon"></svg-icon>
			<span>{{ menu.meta.title }}</span>
		</template>
		<menu-item v-for="sub in menu.children" :key="sub.path" :menu="sub"></menu-item>
	</el-sub-menu>
	<el-menu-item v-else :key="menu.path" :index="menu.path" @click="handleClickMenu(menu)">
		<svg-icon v-if="showIcon" :icon="menu.meta.icon"></svg-icon>
		<template #title>
			{{ menu.meta.title }}
		</template>
	</el-menu-item>
</template>

<script setup lang="ts">
import { computed, PropType, reactive, ref } from 'vue'
import { useRouter } from 'vue-router'
import { userStore } from '@/store/modules/user'
import store from '@/store'
import axios from 'axios'
import cache from '@/utils/cache'
import { getCurrentInstance } from 'vue'
import { SessionStorage, Storage } from '@/utils/storage'
import { isExternalLink, replaceLinkParam } from '@/utils/tool'
import CacheKey from '@/utils/cache/key'

// 显示icon图标
const showIcon = computed(() => {
	return store.appStore.theme.layout !== 'columns'
})

defineProps({
	menu: {
		type: Object as PropType<any>,
		required: true
	}
})

const router = useRouter()
const userStore0 = userStore()

const userData = reactive({
	username: userStore0.user.username,
	password: cache.getPassword(),
	/*username: 'liuxian2',
  password: '6xianzi!@#',*/
	/*username: 'shibowen0',
	password: 'Timshijie2015;',*/
	remember_me: true
})

// 菜单点击事件
const handleClickMenu = (menu: any) => {
	// 不是新开页面，则直接切换路由
	if (!menu.meta._blank) {
    console.log('3')
		router.push(menu.path)
		return
	}

	if (menu.meta.title === '测试外部') {
    console.log('1')
		axios.post('http://gateway.qitongyun.cn:8089/sys/login', userData).then(res => {
			console.log(res.data.result.token)
			if (res.data.result.token) {
				window.open(menu.meta.url, JSON.stringify(res.data.result))
			}
		})
	}

	// 新开页面逻辑
	/*if (isExternalLink(menu.meta.url) || true) {
		// 外链
		// window.open(replaceLinkParam(menu.meta.url), '_blank')
		window.open(menu.meta.url, JSON.stringify(userData))
		console.log(JSON.stringify(userData))
	} else {
		// 内部组件
		console.log('2')
		window.open('#' + menu.meta.url, '_blank')
	}*/
}
</script>
