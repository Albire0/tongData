<template>
	<div class="navbar-right">
		<Lang />
		<ComponentSize />
		<a href="https://shqt.qitongyun.cn/" target="_blank">
			<svg-icon icon="icon-earth"></svg-icon>
		</a>
		<Fullscreen />
		<User />
		<ChangeProject />
		<ThemeSettings />
	</div>
</template>

<script setup lang="ts">
import Lang from '@/components/lang/index.vue'
import ComponentSize from './components/ComponentSize.vue'
import Fullscreen from './components/Fullscreen.vue'
import User from './components/User.vue'
import ChangeProject from './components/ChangeProject.vue'
import ThemeSettings from './components/ThemeSettings.vue'
</script>

<style lang="scss" scoped>
.navbar-right {
	display: flex;
	align-items: center;
	justify-content: flex-end;
	&-link {
		height: 100%;
		display: flex;
		align-items: center;
		white-space: nowrap;
		&-photo {
			width: 25px;
			height: 25px;
			border-radius: 100%;
		}
	}
}
</style>
