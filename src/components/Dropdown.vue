<script setup lang="ts">
import { vOnClickOutside } from '@vueuse/components'
import { useGlobalState } from 'src/composables/toggle'
const visiable = ref(false)
function dropdownHandler() {
	visiable.value = false
}
// const state: any = useGlobalState()
</script>

<template>
	<div class="relative">
		<div class="inline-flex items-center overflow-hidden rounded-md">
			<button
				class="flex w-19 h-full cursor-pointer border-0 bg-white p-2 text-gray-600 hover:bg-gray-200 hover:text-gray-700"
				dark="bg-transparent hover:bg-gray-500"
				:class="
					visiable
						? 'bg-gray-200 bg-gray-500 dark:bg-gray-500'
						: 'bg-gray-200 bg-gray-500 dark:bg-gray-500'
				"
				@click.stop="visiable = !visiable"
			>
				<slot name="up"></slot>
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
			</button>
		</div>

		<Transition name="fade" mode="out-in">
			<div
				v-if="visiable"
				v-on-click-outside.click="dropdownHandler"
				@click="dropdownHandler()"
				dark="bg-gray-500"
				class="absolute end-0 z-10 mt-2 w-44 rounded-md bg-white shadow-lg divide-y divide-gray-100"
			>
				<!-- 语言切换, slot -->
				<div class="p-2">
					<slot name="down"></slot>
				</div>
			</div>
		</Transition>
	</div>
</template>
