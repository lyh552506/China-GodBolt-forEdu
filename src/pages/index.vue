<script setup lang="ts">

import { useElementBounding, useDraggable } from '@vueuse/core'
import { kMaxLength } from 'buffer'
const visiable = ref(false)
function dropdownHandler() {
	visiable.value = false
}
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
			<div style="background-color: #f8f9fa" class="optionchoose h-11 w-full flex justify-between">
				<!-- 各种按钮区域 -->
				<Optionchoose
				><template #up><button
				class=" w-10 h-10 cursor-pointer border-0 bg-white p-2 text-gray-600 hover:bg-gray-200 hover:text-gray-700"
				dark="bg-transparent hover:bg-gray-500"
				:class="
					visiable
						? 'bg-gray-200 bg-gray-500 dark:bg-gray-500'
						: 'bg-gray-200 bg-gray-500 dark:bg-gray-500'
				"
				@click.stop="visiable = !visiable"
			>
			<span class="i-ri:font-family text-black w-5 h-5 inline-block ml--4 items-center flex"></span>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-4 w-4 mt-1 ml-4"
					viewBox="0 0 20 20"
					fill="currentColor"
				>
					<path
						fill-rule="evenodd"
						d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
						clip-rule="evenodd"
					/>
				</svg>
			</button></template>
				<template #down>
					<span
						v-for="option of options"
						:key="option"
						class="block cursor-pointer rounded-lg px-4 py-2 text-sm text-gray-500 hover:text-gray-90 hover:bg-gray-100"
						dark="text-light-500 hover:text-light-900 hover:bg-gray-400"
					>
						<span
							:class="
								option == 'Source Editor'
									? 'i-ic:baseline-edit-calendar'
									: 'i-octicon:git-compare-16'
							"
							class="h-5 w-5 justify-center align-middle mr-2"
							style="display: inline-block"
						></span>
						{{ option }}
					</span></template
				></Optionchoose
			>
				<div
					name="languageoption"
					class="rounded-lg bg-white h-full w-42 float-right border-1 cursor-pointer bord  text-size-4 text-color-#343a40"
				>
			<img src="../assets/img/C++.png" alt="languangeoption" class="h-full inline-block ml-2">
		<span class="pl-1 inline-block h-3 translate-y-0.7">C++</span>
		<span class="float-right i-fluent:triangle-down-32-filled h-3 w-3 justify-center align-middle mr-2 mt-4 text-black"></span>
	</div>
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

<style scoped>
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
