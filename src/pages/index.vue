<script setup lang="ts">
import { useElementBounding, useDraggable } from '@vueuse/core'
import { kMaxLength } from 'buffer'

const editbox = ref(null)
const gooder = ref(null)

// 重命名
const rename = () => {
	ElMessageBox.prompt('Please enter new pane name', 'Rename pane', {
		confirmButtonText: 'Save',
		cancelButtonText: 'Cancel',
		// inputPattern:
		// 	/[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/,
		// inputErrorMessage: 'Invalid name',
	})
		.then(({ value }) => {
			ElMessage({
				type: 'success',
				message: `New name is:${value}`,
			})
		})
		.catch(() => {
			ElMessage({
				type: 'info',
				message: 'Rename canceled',
			})
		})
}

// 关闭输入框
const close = () => {}

//最大化
let blocksize = ref(true)
let blockicon = ref(true)
const max = () => {
	blocksize.value = !blocksize.value
	blockicon.value = !blockicon.value
}

//拖拽改变大小
let sizecontrol = ref<HTMLElement | null>(null)
let {
	width: boundingwidth,
	height,
	x: boundingx,
	y: boundingy,
	right: boundingright,
} = useElementBounding(editbox)
let {
	x: dragx,
	y: dragy,
	style,
} = useDraggable(sizecontrol, {
	initialValue: { x: 658, y: -1000 },
})

const onMouseUp = () => {
	// console.log(dragx.value, boundingx.value)
	let newWidth = dragx.value - boundingx.value

	editbox.value.style.width = newWidth + 'px'
	gooder.value.style.width =
		window.innerWidth - newWidth - sizecontrol.value.style.width + 'px'
	dragy.value = -1000
	dragx.value = boundingwidth
	//这里就得报这个错才能正常运行,后续检查原因
}
</script>

<template>
	<div class="flex max-h-153 display_row w-screen overflow-hidden relative">
		<div
			style="background-color: #fffffe"
			ref="editbox"
			class="h-full overflow-hidden mr-1.6 min-w-10"
			:class="blocksize ? 'w-49.75%' : 'w-screen absolute'"
		>
			<div
				style="background-color: #f2f2f2"
				class="nav_row cursor-default bg-red-400 w-full h-6 flex justify-between overflow-hidden"
			>
				<div
					style="background-color: #e1e1e1"
					class="pl-1 nav_drag cursor-pointer bg-green-300 w-38 align-middle text-gray-600"
				>
					C++ source #1
					<span
						class="w-4.5 align-middle item-center inline-block h-4.5 i-material-symbols:edit-outline"
						@click="rename"
					></span>
					<span
						@click="close"
						class="w-6 text-black align-middle item-center inline-block h-6 i-material-symbols:close-small"
					></span>
				</div>
				<div>
					<span
						@click="max"
						:class="
							blockicon ? 'i-mdi:fullscreen ' : 'i-ic:sharp-fullscreen-exit '
						"
						class="cursor-pointer w-5 align-middle item-center inline-block h-5 mr-0"
					></span>
					<span
						@click="close"
						class="cursor-pointer w-6 align-middle item-center inline-block h-6 mr-0 i-material-symbols:close-small"
					></span>
				</div>
			</div>
			<div style="background-color: #f8f9fa" class="optionchoose h-11 w-full">
				<div
					name="languageoption"
					class="bg-white h-full w-42 float-right rounded-md cursor-pointer flex justify-center items-center"
				></div>
			</div>
			dfgdfsgsdgfsdfasdfsdafsdfsdfsdafsd
		</div>

		<!-- 有hover效果的长条 -->
		<div
			class="w-1.6 h-1000% myresize overflow-hidden absolute z-30 bg-transparent"
			ref="sizecontrol"
			:style="style"
			style="touch-action: none"
			@mouseup="onMouseUp"
		></div>
		<div
			style="background-color: #fffffe"
			ref="gooder"
			class="h-full bg-sky-500 overflow-hidden"
			:class="blocksize ? 'w-49.75%' : 'w-screen absolute'"
		>
			<div
				style="background-color: #f2f2f2"
				class="nav_row cursor-default bg-red-400 w-full h-6 flex justify-between overflow-hidden"
			>
				<div
					style="background-color: #e1e1e1"
					class="nav_drag cursor-pointer bg-green-300 w-65 align-middle text-gray-600 pl-1"
				>
					x86-64 clang 12.0.0 (Editor #1)
					<span
						class="w-4.5 align-middle item-center inline-block h-4.5 i-material-symbols:edit-outline"
						@click="rename"
					></span>
					<span
						@click="close"
						class="w-6 text-black align-middle item-center inline-block h-6 i-material-symbols:close-small"
					></span>
				</div>
				<div>
					<span
						@click="max"
						:class="
							blockicon ? 'i-mdi:fullscreen ' : 'i-ic:sharp-fullscreen-exit '
						"
						class="cursor-pointer w-5 align-middle item-center inline-block h-5 mr-0"
					></span>
					<span
						@click="close"
						class="cursor-pointer w-6 align-middle item-center inline-block h-6 mr-0 i-material-symbols:close-small"
					></span>
				</div>
			</div>
			dfgdfsgsdgfsdfasdfsdafsdfsdfsdafsd
		</div>
	</div>
</template>

<style>
.logo {
	width: 10em;
	height: 10em;
	padding: 1.5rem;
	will-change: filter;
	transition: filter 300ms;
}
.logo:hover {
	filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
	filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
