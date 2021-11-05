<template>
	<button @click="onClick()" :style="{ background: color }" clas="btn">
		{{ text }}
	</button>
</template>
<script>
export default {
	name: "Button",
	props: {
		text: String,
		color: String,
	},
	methods: {
		onClick() {
			this.$emit("btn-click");
		},
	},
};
</script>
<style></style>
