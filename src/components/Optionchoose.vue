<script setup lang="ts">
import { vOnClickOutside } from '@vueuse/components'
const visiable = ref(false)

const target = ref(null)

const { isOutside } = useMouseInElement(target)
function dropdownHandler() {
	if (isOutside.value == true) {
		visiable.value = false
	}
}
</script>

<template>
	<div class="relative">
		<div class="overflow-hidden rounded-md">
			<button
				ref="target"
				class="w-12 h-11 cursor-pointer border-0 bg-white p-2 text-gray-600 hover:bg-gray-200 hover:text-gray-700"
				dark="bg-transparent hover:bg-gray-500"
				:class="
					visiable
						? 'bg-gray-200 bg-gray-500 dark:bg-gray-500'
						: 'bg-gray-200 bg-gray-500 dark:bg-gray-500'
				"
				@click.stop="visiable = !visiable"
			>
				<slot name="up"></slot>
			</button>
		</div>

		<Transition name="fade" mode="out-in">
			<div
				v-if="visiable"
				v-on-click-outside.click="dropdownHandler"
				dark="bg-gray-500"
				class="absolute start-1 z-10 mt-2 w-11  bg-white shadow-lg divide-y divide-gray-100"
			>
				<div>
					<slot name="down"></slot>
				</div>
			</div>
		</Transition>
	</div>
</template>
