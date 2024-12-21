<template>
	<BaseDialog
		v-model="isOpen"
		:="attrs"
		@close="close"
	>
	123
	</BaseDialog>
</template>
<script lang="ts" setup>
import { computed, useAttrs, useSlots } from 'vue';

const props = withDefaults(defineProps<{
	modelValue: boolean;
	width?: string | number;
	title?: string;
	titleIcon?: string;
	auth?: string | boolean;
}>(),
{width: '600'}
);

const emit = defineEmits<Emits>();
interface Emits {
	(e: 'update:modelValue', value: boolean): void;
	(e: 'close'): void;
}

const attrs = useAttrs();
const slots = useSlots();
const isOpen = computed({
	get() {
		return props.modelValue;
	},
	set(val: boolean) {
		emit('update:modelValue', val);
	},
});

const close = () => {
	emit('close');
};

</script>

<style lang="scss" scoped></style>
