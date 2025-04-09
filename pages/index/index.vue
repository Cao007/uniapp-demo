<template>
	<view class="content">
		<text>{{ title }}</text>
	</view>
</template>

<script setup>
import { ref } from 'vue';
const title = ref('ccc');
</script>

<style scoped lang="scss">
.content {
	text {
		color: pink;
	}
}
</style>
