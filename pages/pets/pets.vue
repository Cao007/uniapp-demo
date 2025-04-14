<template>
	<view class="content">
		<view class="tab-bar">
			<!-- <uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" /> -->
			<view class="segmented-control">
				<view v-for="(item, index) in items" :key="index" :class="['seg-item', current === index ? 'active' : '']" @click="onClickItem({ currentIndex: index })">
					{{ item }}
				</view>
			</view>
		</view>

		<view class="img-list">
			<view class="img-box" v-for="(item, index) in petList" :key="item._id">
				<image :src="item.url" mode="widthFix" lazy-load @click="previewImage(index)" />
				<p class="desc">{{ item.content }}</p>
				<p class="author">——{{ item.author }}</p>
			</view>
		</view>

		<view class="bottom-tools">
			<view class="tool refresh" @click="handleRefresh">刷新</view>
			<view class="tool to-top" @click="handleToTop">顶部</view>
		</view>
	</view>
</template>

<script setup>
const petList = ref([]);
const current = ref(0);
const items = ref(['all', 'dog', 'cat']);

// 切换选项卡
function onClickItem(e) {
	current.value = e.currentIndex;
	petList.value = [];
	getPetList(items.value[e.currentIndex]);
}

// 获取宠物列表
async function getPetList(type = items.value[0]) {
	try {
		const res = await uni.request({
			url: 'https://tea.qingnian8.com/tools/petShow',
			data: {
				type: type,
				size: 10
			},
			header: {
				'access-key': 'ccc123'
			}
		});
		petList.value = [...petList.value, ...res.data.data];
	} catch (error) {
		console.log(error);
	}
}
getPetList();

// 图片预览
async function previewImage(index) {
	const urls = petList.value.map((item) => item.url);
	const res = await uni.previewImage({
		urls: urls,
		current: index
	});
}

// 下拉刷新
onPullDownRefresh(() => {
	petList.value = [];
	getPetList(items.value[current.value]);
	uni.stopPullDownRefresh();
});

// 刷新按钮
function handleRefresh() {
	uni.startPullDownRefresh();
}

// 返回顶部按钮
function handleToTop(e) {
	uni.pageScrollTo({
		scrollTop: 0,
		duration: 300 // 设置滚动时间
	});
}

// 触底加载更多
onReachBottom(() => {
	getPetList(items.value[current.value]);
});
</script>

<style scoped lang="scss">
.content {
	padding: 20rpx;
	box-sizing: border-box;
	padding-bottom: env(safe-area-inset-bottom);

	.tab-bar {
		position: sticky;
		top: 0;
		z-index: 10;

		.segmented-control {
			display: flex;
			background-color: #eee;
			border-radius: 20rpx;
			overflow: hidden;

			.seg-item {
				flex: 1;
				text-align: center;
				padding: 20rpx 0;
				color: #666;

				&.active {
					background-color: #007aff;
					color: #fff;
				}
			}
		}
	}

	.img-list {
		.img-box {
			width: 300px;
			margin: 0 auto 20rpx;
			border-radius: 10rpx;
			padding: 10rpx;
			box-shadow: 0 10px 20px rgba(0, 0, 0, 0.25), 0 6px 6px rgba(0, 0, 0, 0.22);

			image {
				width: 100%;
			}

			.author {
				text-align: right;
				color: #999;
				font-size: 12px;
			}
		}
	}

	.bottom-tools {
		position: fixed;
		bottom: 100rpx;
		right: 10rpx;

		.tool {
			width: 100rpx;
			height: 100rpx;
			background-color: #fff;
			border-radius: 50%;
			box-shadow: 0 10px 20px rgba(0, 0, 0, 0.25), 0 6px 6px rgba(0, 0, 0, 0.22);
			text-align: center;
			line-height: 100rpx;
			font-size: 12px;
			color: #999;
			margin-bottom: 20rpx;
		}
	}
}
</style>
