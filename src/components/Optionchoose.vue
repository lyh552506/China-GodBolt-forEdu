<script setup lang="ts">
import { vOnClickOutside } from '@vueuse/components'

// const state: any = useGlobalState()
</script>

<template>
	<div class="relative">
		<div class="  overflow-hidden rounded-md">
			<slot name="up">	</slot>

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
