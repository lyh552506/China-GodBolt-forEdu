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

//字体大小选项
const sizeoptions = [
	8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27,
	28, 29, 30,
]
let fontsize = ref(16)
const changeFontsize = (sizeoption: any) => {
	fontsize.value = sizeoption
}

//添加框选项
const addoptions = ['Compiler', 'Excution Only', 'Source Editor']

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
			<!-- 最顶一层 -->
			<div
				class="nav_row cursor-default bg-gray-200 w-full h-6 flex justify-between overflow-hidden"
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
			<div class="bg-gray-100 optionchoose h-11 w-full flex justify-between">
				<!-- 各种按钮区域 -->
				<div class="flex">
					<Optionchoose>
						<template #up>
							<div
								class="i-ri:font-family text-black w-5 h-5 inline-block ml--5 mt-1.7"
							></div>

							<svg
								xmlns="http://www.w3.org/2000/svg"
								class="h-4 w-4 mt--14 ml-5 inline"
								viewBox="0 0 20 20"
								fill="currentColor"
							>
								<path
									fill-rule="evenodd"
									d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
									clip-rule="evenodd"
								/>
							</svg>
						</template>
						<template #down>
							<div class="overflow-y-scroll overflow-x-hidden h-111">
								<span
									v-for="sizeoption of sizeoptions"
									:key="sizeoption"
									:class="
										fontsize === sizeoption
											? 'bg-gray-100 text-gray-800 dark:bg-gray-400'
											: ''
									"
									class="text-center block cursor-pointer text-lg border-b-2 text-gray-500 hover:text-gray-90 hover:bg-gray-100"
									dark="text-light-500 hover:text-light-900 hover:bg-gray-400"
									@click="changeFontsize(sizeoption)"
								>
									{{ sizeoption }}
								</span>
							</div>
						</template></Optionchoose
					>
					<button
						ref="target"
						class="rounded-md w-12 h-11 cursor-pointer border-0 bg-gray-100 p-2 text-gray-600 hover:bg-gray-400 hover:text-gray-700"
						dark="bg-transparent hover:bg-gray-500"
					>
						<span class="i-bxs:save h-5 w-5 text-black inline-block mt-1.7">
						</span>
					</button>
					<Optionchoose>
						<template #up>
							<div
								class="i-ic:outline-add-circle-outline text-black w-5 h-5 inline-block ml--5 mt-1.7"
							></div>

							<svg
								xmlns="http://www.w3.org/2000/svg"
								class="h-4 w-4 mt--14 ml-5 inline"
								viewBox="0 0 20 20"
								fill="currentColor"
							>
								<path
									fill-rule="evenodd"
									d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
									clip-rule="evenodd"
								/>
							</svg>
						</template>
						<template #down>
							<div class="overflow-y-scroll overflow-x-hidden h-111">
								<span
									v-for="option of addoptions"
									:key="option"
									class="bg-gray-100 text-center block cursor-pointer text-lg border-b-2 text-gray-500 hover:text-gray-90 hover:bg-gray-300"
									dark="text-light-500 hover:text-light-900 hover:bg-gray-400"
									@click="changeFontsize(sizeoption)"
								>
									{{ option }}
								</span>
							</div>
						</template></Optionchoose
					>
					<button
						ref="target"
						class="rounded-md w-12 h-11 cursor-pointer border-0 bg-color-#f8f9fa p-2 text-gray-600 hover:bg-gray-200 hover:text-gray-700"
						dark="bg-transparent hover:bg-gray-500"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							width="1.6rem"
							height="1.6rem"
							viewBox="0 0 32 32"
							class="mt-1"
						>
							<defs>
								<clipPath id="vscodeIconsFileTypeVim0">
									<path fill="none" d="M2 2h28v28H2z" clip-rule="evenodd" />
								</clipPath>
							</defs>
							<path
								fill="#231f20"
								fill-rule="evenodd"
								d="M29.989 15.856L15.856 2.011L2.011 16.136l13.845 13.853z"
							/>
							<g clip-path="url(#vscodeIconsFileTypeVim0)">
								<path
									fill="none"
									stroke="#231f20"
									stroke-miterlimit="10"
									stroke-width=".216"
									d="M29.989 15.856L15.856 2.011L2.011 16.136l13.845 13.853z"
								/>
							</g>
							<path
								fill="#29695d"
								fill-rule="evenodd"
								d="M28.575 15.856h.847L15.856 29.422v-.847z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="M28.575 15.856h.847L15.856 29.422v-.847z"
							/>
							<path
								fill="#317367"
								fill-rule="evenodd"
								d="M2.578 16.136h.847l12.431 12.439v.847z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="M2.578 16.136h.847l12.431 12.439v.847z"
							/>
							<path
								fill="#60c2ac"
								fill-rule="evenodd"
								d="M15.856 3.418v-.84L2.578 16.136h.847z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="M15.856 3.418v-.84L2.578 16.136h.847z"
							/>
							<path
								fill="#43b54a"
								fill-rule="evenodd"
								d="M15.856 2.578v.84l12.719 12.438h.847z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="M15.856 2.578v.84l12.719 12.438h.847z"
							/>
							<path
								fill="#3c8376"
								fill-rule="evenodd"
								d="m15.856 28.575l12.719-12.719L15.856 3.418L3.425 16.136z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m15.856 28.575l12.719-12.719L15.856 3.418L3.425 16.136z"
							/>
							<path
								fill="#231f20"
								fill-rule="evenodd"
								d="m18.498 6.246l.847.854l-5.843 5.928V7.1h.567l.847-.854V3.992l-.847-.854H4.652l-.847.854v2.254l.847.854h.66v19.214l1.034.847h2.921L29.516 6.246V3.992l-.847-.854h-9.231l-.94.854z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m18.498 6.246l.847.854l-5.843 5.928V7.1h.567l.847-.854V3.992l-.847-.854H4.652l-.847.854v2.254l.847.854h.66v19.214l1.034.847h2.921L29.516 6.246V3.992l-.847-.854h-9.231l-.94.854z"
							/>
							<path
								fill="#fff"
								fill-rule="evenodd"
								d="m4.932 6.533l-.567-.567V4.272l.567-.567l8.857-.007l.56.574l-.56.272l-.287-.272l-8.57 1.407z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m4.932 6.533l-.567-.567V4.272l.567-.567l8.857-.007l.56.574l-.56.272l-.287-.272l-8.57 1.407z"
							/>
							<path
								fill="#fff"
								fill-rule="evenodd"
								d="m6.626 26.594l-.66-.567V6.526l.66-.56z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m6.626 26.594l-.66-.567V6.526l.66-.56z"
							/>
							<path
								fill="#fff"
								fill-rule="evenodd"
								d="m20.759 6.533l.567-.567V7.1l-9.424 9.603l1.04-2.261z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m20.759 6.533l.567-.567V7.1l-9.424 9.603l1.04-2.261z"
							/>
							<path
								fill="#929497"
								fill-rule="evenodd"
								d="m6.82 5.686l-.194.28l-.66.567H4.932V5.399z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m6.82 5.686l-.194.28l-.66.567H4.932V5.399z"
							/>
							<path
								fill="#929497"
								fill-rule="evenodd"
								d="M12.942 6.533v7.909l-1.04 2.254V5.958h1.6l.287-.28l-.287-1.406h.847v1.694l-.56.567z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="M12.942 6.533v7.909l-1.04 2.254V5.958h1.6l.287-.28l-.287-1.406h.847v1.694l-.56.567z"
							/>
							<path
								fill="#fff"
								fill-rule="evenodd"
								d="m19.625 6.533l-.56-.567V4.272l.653-.567h8.577l.661.567l-.948.847l-8.383.56z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m19.625 6.533l-.56-.567V4.272l.653-.567h8.577l.661.567l-.948.847l-8.383.56z"
							/>
							<path
								fill="#929497"
								fill-rule="evenodd"
								d="M28.956 5.966L9.074 26.594H6.626v-.847H8.42L28.295 5.399l-.287-1.127h.948z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="M28.956 5.966L9.074 26.594H6.626v-.847H8.42L28.295 5.399l-.287-1.127h.948z"
							/>
							<path
								fill="#929497"
								fill-rule="evenodd"
								d="m21.512 5.686l-.193.28l-.56.567h-1.134V5.399z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m21.512 5.686l-.193.28l-.56.567h-1.134V5.399z"
							/>
							<path
								fill="#d0d2d3"
								fill-rule="evenodd"
								d="M11.902 16.696V5.958h1.6l.287-.279V4.544l-.287-.279h-8.29l-.28.279v1.135l.28.279h1.414v19.789l.366.28h1.615L28.389 5.399v-.811l-.287-.323h-8.19l-.287.279v1.142l.287.28h1.414V7.1z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="M11.902 16.696V5.958h1.6l.287-.279V4.544l-.287-.279h-8.29l-.28.279v1.135l.28.279h1.414v19.789l.366.28h1.615L28.389 5.399v-.811l-.287-.323h-8.19l-.287.279v1.142l.287.28h1.414V7.1z"
							/>
							<path
								fill="#231f20"
								fill-rule="evenodd"
								d="m17.041 16.711l.66-.567h1.694l.474.567l-.567 1.694l-.653.567h-1.694l-.481-.567z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m17.041 16.711l.66-.567h1.694l.474.567l-.567 1.694l-.653.567h-1.694l-.481-.567z"
							/>
							<path
								fill="#231f20"
								fill-rule="evenodd"
								d="m17.608 26.034l1.701-5.081h-.567l.567-1.702h2.534l.567.568h.38l.56-.568h1.888l.567.568h.373l.567-.568h2.067l.754 1.135l-1.235 4.019h.56l-.545 1.629h-3.395l1.327-3.955h-.847l-.782 2.312h.56l-.531 1.643h-3.395l1.32-3.955h-.847l-.789 2.326h.567l-.531 1.629z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m17.608 26.034l1.701-5.081h-.567l.567-1.702h2.534l.567.568h.38l.56-.568h1.888l.567.568h.373l.567-.568h2.067l.754 1.135l-1.235 4.019h.56l-.545 1.629h-3.395l1.327-3.955h-.847l-.782 2.312h.56l-.531 1.643h-3.395l1.32-3.955h-.847l-.789 2.326h.567l-.531 1.629z"
							/>
							<path
								fill="#d0d2d3"
								fill-rule="evenodd"
								d="m28.554 19.819l.438.624l-1.393 4.464h.56l-.187.56h-2.261l1.321-3.955h-1.981l-1.127 3.395h.56l-.187.56h-2.261l1.321-3.955h-1.981l-1.127 3.395h.567l-.194.56h-2.261l1.702-5.081h-.568l.187-.567h2.074l.567.567h.56l.567-.567h1.694l.567.567h.567l.567-.567z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m28.554 19.819l.438.624l-1.393 4.464h.56l-.187.56h-2.261l1.321-3.955h-1.981l-1.127 3.395h.56l-.187.56h-2.261l1.321-3.955h-1.981l-1.127 3.395h.567l-.194.56h-2.261l1.702-5.081h-.568l.187-.567h2.074l.567.567h.56l.567-.567h1.694l.567.567h.567l.567-.567z"
							/>
							<path
								fill="#231f20"
								fill-rule="evenodd"
								d="m19.022 19.251l-1.73 5.149h.589l-.56 1.637h-3.388l1.694-5.082h-.567l3.962-1.7Zm-3.962 1.7l.567-1.7h3.4l-3.962 1.7Z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m19.022 19.251l-1.73 5.149h.589l-.56 1.637h-3.388l1.694-5.082h-.567l3.962-1.7Zm-3.962 1.7l.567-1.7h3.4l-3.962 1.7Z"
							/>
							<path
								fill="#d0d2d3"
								fill-rule="evenodd"
								d="m16.947 25.467l.187-.56h-.567l1.701-5.088h-2.354l-.194.567h.66l-1.694 5.081z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m16.947 25.467l.187-.56h-.567l1.701-5.088h-2.354l-.194.567h.66l-1.694 5.081z"
							/>
							<path
								fill="#d0d2d3"
								fill-rule="evenodd"
								d="m18.828 18.125l.38-1.134l-.186-.28h-1.134l-.374.28l-.38 1.134l.194.28h1.134z"
							/>
							<path
								fill="none"
								stroke="#231f20"
								stroke-miterlimit="10"
								stroke-width=".216"
								d="m18.828 18.125l.38-1.134l-.186-.28h-1.134l-.374.28l-.38 1.134l.194.28h1.134z"
							/>
						</svg>
					</button>
					<a
						href="https://cppinsights.io/"
						target="_blank"
						class="inline-block h-full w-12"
						><button
							ref="target"
							class="rounded-md w-12 h-11 cursor-pointer border-0 bg-color-#f8f9fa p-2 text-gray-600 hover:bg-gray-200 hover:text-gray-700"
							dark="bg-transparent hover:bg-gray-500"
						>
							<svg height="1.4rem" width="1.4rem" class="ml-1">
								<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 210 210">
									<path
										class="cppi-logo-sec"
										fill="#000"
										d="M86.7 138.22l-64.953 64.953c-2.5698 2.5698-6.7075 2.5698-9.2782 0l-4.6391-4.6401c-2.5698-2.5698-2.5698-6.7075 0-9.2782l64.953-64.953c4.6344 4.6438 13.489 13.489 13.917 13.917zM112.97 91.765V81.087H91.709v-6.3311h21.261v-21.356h6.3303l.024 10.654.024 10.654 21.308.04804v6.3295l-21.308.04804-.024 10.654-.024 10.654H112.97zM163.3 91.765V81.087h-21.261v-6.3311H163.3v-21.356h6.3303l.024 10.654.024 10.654 21.308.04804v6.3295l-21.308.04804-.024 10.654-.024 10.654H163.3z"
									></path>
									<path
										class="cppi-logo-pri"
										fill="none"
										stroke="#2980b9"
										stroke-linecap="square"
										stroke-width="19"
										d="M191.21 108.07a63.508 63.508 0 0 1-70.527 31.65 63.508 63.508 0 0 1-48.848-59.914 63.508 63.508 0 0 1 45.205-62.708 63.508 63.508 0 0 1 72.282 27.407"
									></path>
								</svg>
							</svg></button
					></a>
					<a
						href="https://quick-bench.com/"
						target="_blank"
						class="inline-block h-full w-12"
						><button
							ref="target"
							class="rounded-md w-12 h-11 cursor-pointer border-0 bg-color-#f8f9fa p-2 text-gray-600 hover:bg-gray-200 hover:text-gray-700"
							dark="bg-transparent hover:bg-gray-500"
						>
							<svg height="1.4rem" width="1.4rem" class="ml-1">
								<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1000 1000">
									<path
										class="qb-logo-sec"
										fill="#fff"
										d="M682.4 405.4a89.6 89.6 0 00-77.9 49.1c-15.3 31.2-12.5 66.7-17.3 98.6a3.5 3.5 0 000 .5c-2 14.3-4.8 28.4-8.5 42.3a203.5 203.5 0 01-30.1 69c-3.7 5-7.7 9.9-12 14.3-4.3 4.6-9.2 9.3-12.7 15.4-3 5.3-4.4 11.4-2.7 17.7 1 3.5 3 7.4 6.9 9.6a3.5 3.5 0 001.7.5c7.4 0 14.8-.1 22.3-.8a426 426 0 0034.7-4.7c16.6-2.8 33.5-6 50-6a3.5 3.5 0 00.6-.1c27.2-4.3 54.6-6.8 82-7.5a3.5 3.5 0 001.7.4l5.4-.1h.3a3.5 3.5 0 001.4.3c13.3.2 26.6 0 39.4 1.7 6.7.8 13 2.4 18.4 5.5l3.5 2.4c.8.8 1 1.3 1 2l-.2.6a3.5 3.5 0 00-.2 2.3 61.9 61.9 0 0012.2 25c2.8 3.5 5.8 6.7 9 9.8 3.6 3.5 7.4 5.7 11.2 8a201.2 201.2 0 0125.9 19.7 3.5 3.5 0 005.6-4c-1.5-3.6-2.8-7.6-4.6-11.6-2.2-5-4.7-9.6-6.8-14.4 2.3 1 4.5 2.2 6.6 3.6a3.5 3.5 0 001.2.5c5.8 1.3 12.3 5.3 18 8.7 3.6 2 7.1 4.3 10.6 6.7a3.5 3.5 0 004.7-5.2 335.5 335.5 0 00-29.8-30.4c-8.2-7.2-16.7-14.1-25.5-20.5-4.4-3.2-8.8-6.2-13.2-9.2-2.1-1.4-4.5-2.7-6.6-4a3.5 3.5 0 00-.7-.4c-4.3-1.6-8.6-3.5-13-5.2l-5.6-2a3.5 3.5 0 00-.6-.2l-8.8-1.7-23.1-4-22-3.8a3.5 3.5 0 00-.3 0l-22.3-2.1-15-1.4c-5.1-.5-10.4-.5-14.5-1.7a3.5 3.5 0 00-.7 0 298.6 298.6 0 00-110.6 13.9s-.2 0-.5-.6l-.2-.4 52.7-40.7 46.2-35.5c12.5-9.6 25-19.5 36.7-30.1A222.2 222.2 0 00738 551a3.5 3.5 0 000-.1l.6-.8a3.5 3.5 0 00.5-1.3c.6-2.5 1.5-4.8 2.7-6.9 2.9-5 5.5-10 7.8-15.1a3.5 3.5 0 000-.2c4-9.8 6.7-20 8.5-30.3a3.5 3.5 0 000-.2c1-9 1-18.1 0-27.1a3.5 3.5 0 000-.3c-1.4-8.1-3.6-16-6.5-23.7a3.5 3.5 0 00-.1-.2c-2.2-4.6-4.6-9-7.2-13.3a3.5 3.5 0 00-.6-.7 87 87 0 00-61.1-25.5zM441.2 629.7c-4.3 0-8.6.5-12.8 1.4a3.5 3.5 0 00-.7.2l-3.6 1.4c-10.8 4.5-21.3 10-31.2 16-9.5 5.8-18.1 12.5-27 18.2a69 69 0 01-17.8 8.7 3.5 3.5 0 00-.6.2c-7.4 3.6-15 6.8-22.8 9.4-9.3 3.3-19.4 5.8-28.9 10.4-10 4.7-18 12.2-21 23.6l-.7 3.7a3.5 3.5 0 000 1 194.3 194.3 0 01-42 147.7 3.5 3.5 0 00-.5.8 62.6 62.6 0 01-17.7 23 3.5 3.5 0 00-1.6.2c-1 .2-2.5-.3-5.4-1-3.2-.8-6.3-1.3-9.5-1.9l-5.2-.6a3.5 3.5 0 00-1 0 91 91 0 00-8.3 2c-10.7 3-21 7.2-31.1 11.2-10.6 4.1-21 8.7-31.3 13.5l-10 4.7a3.5 3.5 0 001.8 6.7l37.3-4.4a3.5 3.5 0 00.4 0 3.5 3.5 0 00.1 0c.6 0 1.1.2 1.6.3a3.5 3.5 0 00.3 0c2 .5 4.1.8 6.3.8l.2.3c-6 8-13 15-21 21.2a3.5 3.5 0 003.1 6.2c20.6-6.3 41.7-12.8 60.8-23.7a3.5 3.5 0 001.4-1.5c0 .2.2 0 0 0 4.8-3.4 10.3-6.3 15.6-10.1 4.8-3.6 9.5-7.4 13.8-11.6 8.3-8 15.4-17 22.4-25.7a3.5 3.5 0 00.3-.5l3.6-6.5a358.2 358.2 0 0029.3-71.6c3.5-12.3 6.5-24.8 8.7-37.4.7-3.3 1.1-8.8 2.4-13.4.6-2.3 1.5-4.3 2.3-5.5.9-1.1 1.4-1.4 2.3-1.4a3.5 3.5 0 001.2-.3h.3v-.1c13.4-5.2 27-10.2 40.4-15.8 23.6-9.7 46.7-20.2 69.6-31.6 11.4-5.6 22.7-11.5 34-17.4l18.7-9.9a3.5 3.5 0 00.9-.6 38 38 0 006.6-8.2 22.4 22.4 0 002.2-18.5 3.5 3.5 0 000-.1l-.7-1.8a3.5 3.5 0 00-1-1.5 46 46 0 00-21.6-9.1c-4.3-.8-8.6-1.1-12.9-1zM744.8 48.9c-4.1 0-7.8 2.8-10.2 6.8a29 29 0 00-3.7 14.6 29 29 0 003.7 14.7c2.4 4 6 6.7 10.2 6.7 4 0 7.8-2.7 10.2-6.7a29 29 0 003.7-14.7 29 29 0 00-3.7-14.6c-2.4-4-6.1-6.8-10.2-6.8z"
									></path>
									<path
										class="qb-logo-pri"
										fill="#000"
										d="M907.2 478.8a177.8 177.8 0 0028.5-52.3c6.7-20.2 9-41.6 6.7-62.7-4.5-43.1-25.5-83-56.3-113.2a273.4 273.4 0 00-109-63c-18.5-5.9-40.8-11.8-53.2-28.1-8.7-11.5-11.5-30.1-2.8-42.5 1.1-1.5 2.5-3 3.9-4.1 17 .3 34-.5 50.9-2.8 17.9-2.3 35.7-6.7 52-14.6 9-4.4 18.2-9.6 25.4-16.7 1.4-1.3.5-3.4-1-4a2.5 2.5 0 00-2.6-.9 75.5 75.5 0 01-54.3-6.8l-3.2-1.9a28.8 28.8 0 00-7.4-15.1 71.5 71.5 0 00-120.3 17.7c-2.8 5.2-5 10.9-6.6 16.7-2.2 8.8-3 18-2.4 27.1-.2 11 .6 22 2.2 32.9 2.1 15.7 6.2 31.3 12 46a141 141 0 0023.8 40.6c11.2 13 25 21.8 40.2 29.2 15.3 7.3 32.4 14.5 42 29.2 3 4.4 5 9.4 5.6 14.7 0 2.6-.2 5.3-.5 7.9-1.2 5.5-4.1 11-8.6 14.4a17.2 17.2 0 01-5.6 2.8c-.2-.4-.4-.8-.8-1.1a258.5 258.5 0 00-152.4-62.4 264.4 264.4 0 00-215.6 89c-4.5-1.1-9.5-1.2-14-1.7a325 325 0 01-41.7-7.5c-17.4-4.5-33.8-12-48-23a2 2 0 00-.7-1.1A17 17 0 00280 319a3 3 0 00-2 2c-6.8 26.4-4.2 55.2 4.2 80.8a167.2 167.2 0 0061 83.2c3.8 2.8 7.7 5.3 11.8 7.7 4 31.4 20.2 60.4 41.8 83.3 3.9 4 7.8 8 12 11.5a226.3 226.3 0 01-43.6 57.1 193.7 193.7 0 01-78.1 43.9c-5.7 1.6-11.7 3-16.7 6.4-11.6 7.7-11.2 20.9-9.5 33.2a177.8 177.8 0 01-21.7 113.6c-6.8 12-15.3 22.5-24.4 32.8-.3-.4-.5-.7-1-1-17.5-12.3-40.5 1.1-56 10.5a424.6 424.6 0 01-98 43.3c-2.9.8-4.2 4.7-1.9 6.8 4.8 4.4 11 5.4 17.3 5 8-.4 16-1.7 23.9-2.6l37.8-4.5a114.5 114.5 0 01-32.4 18.4A355 355 0 0160 964.1c-3.6 1-4 7.1 0 7.8a208.7 208.7 0 00192.4-68.7c6.9-6.5 11.6-15.5 16-23.7 2.6-5 5-10 7.4-15a389 389 0 0044.7-116.4 619 619 0 00193.8-84.8l6-4 4-2.7c5-3.2 9.6-6.7 14.2-10.3l1.9.4c-5.4 7.6-11 15-16.8 22.3-5.8 7.2-12 14.3-16.8 22.3-8 13.3-10.9 31.8 1 43.9a23 23 0 009 5.6c.6.2 1.2.3 1.8.2a18.9 18.9 0 007.4.5c5-.1 9.7-1.7 14.4-3l19.7-6c3.7-1.1 7.6-2 11.4-3.3 50-8.5 100.2-14.2 150.8-16.1 15.3-.6 31.8-2 46.7 2.6a46.2 46.2 0 0023.2 46c.1 0 .3 0 .5.2 4.8 3 10.1 5.6 15.2 8.5a175.7 175.7 0 0175 84.8c1.6 4 6.6 3.3 7.2-1 2.4-19.5.4-40-6.7-58.6.7 0 1.5-.2 2.2-.5l1.3 1.6a234 234 0 0130.5 46.9c1.4 2.9 6.6 1.6 6.3-1.7-1.6-14-5.1-27.8-10.4-40.9l-.3-.6c-14.4-35.2-41-65-71.9-87.7-12.3-9-25.2-17.2-38.7-24.5-.8-.5-1.8-.6-2.7-.3-10.5-4.6-22-7.7-33-11A483 483 0 00639.3 658l-5.9.2 11.2-8.8c9 .3 18.4-1.9 27-3.6 9.5-1.7 18.8-4 28-6.7 11.4-3.4 23.5-8 34.2-14.3a196.9 196.9 0 0063.8-40c5.6-5.4 11.1-11.2 15.7-17.6a15 15 0 002.6-8.8c33.5-22.4 66.5-47.5 91.3-79.6zM745 51.5c6.2 0 11.2 8.3 11.2 18.7 0 10.4-5 18.8-11.2 18.8-6.2 0-11.3-8.4-11.3-18.8s5-18.7 11.3-18.7zM472 659.9a34.5 34.5 0 01-6 7.4c-6.3 3.2-12.5 6.6-18.8 10A1098.2 1098.2 0 01344 726.1c-13.3 5.4-26.9 10.4-40.4 15.7l-.3.1c-8.4.2-9.2 17.5-10.3 23.2a357.6 357.6 0 01-37.7 108l-3.5 6.5c-7 8.8-14 17.6-22.1 25.4a135 135 0 01-13.4 11.2c-5 3.6-10.6 6.5-15.6 10.1-.5.4-.8.8-1 1.3-18.7 10.7-39.6 17-60.2 23.4a114 114 0 0022-22.3c1.9-2.3-.6-5.5-3.1-5.5a35.3 35.3 0 01-7.7-1.1c-.5-.2-1-.2-1.5 0l-37.4 4.4 10.1-4.7c10.2-4.8 20.6-9.3 31.1-13.5 10.1-3.9 20.3-8 30.8-11 2.6-.8 5.3-1.4 8-2l5 .7c3 .5 6.1 1 9.2 1.8 2.5.7 4.9 1.7 7.3.9.7.3 1.6.3 2.2-.2a65.7 65.7 0 0019.3-24.8 199.1 199.1 0 0042.7-150.4l.7-3.4a32.2 32.2 0 0119-21.4c9.1-4.3 19.1-6.8 28.6-10.2 7.9-2.7 15.6-6 23.2-9.5a71.3 71.3 0 0018.7-9.2c9.1-5.7 17.7-12.4 27-18 9.8-6 20.1-11.4 30.7-15.8l3.6-1.5c8-1.6 16.3-1.7 24.4-.3a42.3 42.3 0 0120 8.4l.6 1.6c1.6 5.3 1 11-2 15.8zm282.3-164.2a148 148 0 01-8.2 29.7c-2.3 5-4.8 10-7.6 14.8a28.7 28.7 0 00-3.1 8l-.6.7c-9 12.4-19.7 23.4-31 33.6a619.9 619.9 0 01-36.6 30l-46.1 35.4-52.8 40.9c-3.3 2.6.3 8.1 4 7A295 295 0 01681.6 682c4.7 1.4 10.2 1.2 15.2 1.7l14.8 1.4 22.4 2 22 4 23 4 8.8 1.6 5.6 2 13 5.3c2.2 1.5 4.7 2.8 6.5 4l13.1 9.1a325 325 0 0154.7 50.3c-3.5-2.4-7-4.7-10.7-6.9-5.8-3.3-12.4-7.5-19-9-3-2-6.3-3.7-9.8-5l-.3-.2h-.5l-.7-.3c-1.8-.5-3.3 1.9-2 3.2l.7.4c-.1.5 0 1 .1 1.5l7.4 15.6c1.7 3.7 3 7.7 4.6 11.6a204.7 204.7 0 00-26.4-20c-3.7-2.5-7.3-4.6-10.6-7.7-3.1-3-6-6.1-8.7-9.5-5.6-7-9.4-15-11.5-23.6.3-.6.5-1.4.5-2.1 0-3.6-3.6-5.8-6.3-7.4-6-3.5-12.8-5.1-19.7-6-13-1.7-26.5-1.5-39.8-1.7-.6-.3-1.1-.3-1.7-.3h-5.5a3 3 0 00-1.4-.3c-27.8.7-55.4 3.2-82.8 7.5-17 .1-34 3.3-50.7 6.1-11.4 2-22.8 3.8-34.3 4.7-7.3.7-14.7.9-22 .8a12.8 12.8 0 01-5.2-7.5 18.8 18.8 0 012.3-15c3.2-5.5 7.8-10.1 12.2-14.7 4.5-4.6 8.6-9.5 12.4-14.6a206 206 0 0030.7-70.3 356 356 0 008.6-42.8v-.3c4.8-32.5 2.2-67.5 17-97.6a86 86 0 0174.8-47c22.1-.5 43 9.2 58.6 24.4 2.7 4.2 5 8.5 7 13 2.9 7.4 5 15 6.3 23 1 8.7 1 17.6 0 26.3z"
									></path>
								</svg>
							</svg></button
					></a>
				</div>

				<!-- 语言选择 -->
				<div
					name="languageoption"
					class="rounded-lg bg-light-100 h-full w-42 float-right border-1 cursor-pointer bord text-size-4 text-color-#343a40"
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						viewBox="0 0 32 32"
						class="h-11 w-8 inline-block"
					>
						<path
							fill="#659ad2"
							d="M29 10.232a2.387 2.387 0 0 0-.318-1.244a2.451 2.451 0 0 0-.936-.879q-5.194-2.868-10.393-5.733a2.642 2.642 0 0 0-2.763.024c-1.378.779-8.275 4.565-10.331 5.706A2.287 2.287 0 0 0 3 10.231V21.77a2.4 2.4 0 0 0 .3 1.22a2.434 2.434 0 0 0 .954.9c2.056 1.141 8.954 4.927 10.332 5.706a2.642 2.642 0 0 0 2.763.026q5.19-2.871 10.386-5.733a2.444 2.444 0 0 0 .955-.9a2.4 2.4 0 0 0 .3-1.22V10.232"
						/>
						<path
							fill="#00599c"
							d="M28.549 23.171a2.126 2.126 0 0 0 .147-.182a2.4 2.4 0 0 0 .3-1.22V10.232a2.387 2.387 0 0 0-.318-1.244c-.036-.059-.089-.105-.13-.16L16 16Z"
						/>
						<path
							fill="#004482"
							d="M28.549 23.171L16 16L3.451 23.171a2.435 2.435 0 0 0 .809.72c2.056 1.141 8.954 4.927 10.332 5.706a2.642 2.642 0 0 0 2.763.026q5.19-2.871 10.386-5.733a2.43 2.43 0 0 0 .808-.719"
						/>
						<path
							fill="#fff"
							d="M19.6 18.02a4.121 4.121 0 1 1-.027-4.087l3.615-2.073A8.309 8.309 0 0 0 7.7 16a8.216 8.216 0 0 0 1.1 4.117a8.319 8.319 0 0 0 14.411-.017z"
						/>
						<path
							fill="#fff"
							d="M24.076 15.538h-.926v-.921h-.925v.921h-.926v.923h.926v.92h.925v-.92h.926zm3.473 0h-.926v-.921h-.926v.921h-.926v.923h.926v.92h.926v-.92h.926z"
						/>
					</svg>
					<span class="pl-1 inline-block h-3 translate-y-0.7">C++</span>
					<span
						class="float-right i-fluent:triangle-down-32-filled h-3 w-3 justify-center align-middle mr-2 mt-4 text-black"
					></span>
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
				class="nav_row cursor-default bg-gray-200 w-full h-6 flex justify-between overflow-hidden"
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
