<template>
	<q-dialog
		v-model="isOpen"
		:="attrs"
		:allow-focus-outside="true"
		persistent
	>
		<slot></slot>
	</q-dialog>
</template>
<script lang="ts" setup>
import { useAttrs, computed } from 'vue';

const props = defineProps<{
	modelValue: boolean;
}>();

const emit = defineEmits<Emits>();
interface Emits {
	(e: 'update:modelValue', value: boolean): void;
}

const attrs = useAttrs();

const isOpen = computed({
	get() {
		return props.modelValue;
	},
	set(val: boolean) {
		emit('update:modelValue', val);
	},
});
</script>

<style lang="scss" scoped></style>
