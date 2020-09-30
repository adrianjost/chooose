<template>
	<svg :viewBox="viewBox">
		<path :d="line" stroke="black" fill="transparent" />
	</svg>
</template>

<script>
export default {
	props: {
		value: {
			type: [Array],
			default: () => [5, 15, 25, 5],
		},
		options: {
			type: Object,
			default: () => ({}),
		},
	},
	computed: {
		width() {
			return 500;
		},
		height() {
			return Math.max(...this.value);
		},
		viewBox() {
			return `0 0 ${this.width} ${this.height}`;
		},
		handleCoordinated() {
			return this.value.map((y, i) => ({
				x: i * this.xSpace,
				y,
			}));
		},
		line() {
			return `M 0,0 ${handleCoordinated
				.map(({ x, y }) => {
					return `L ${x} ${y}`;
				})
				.join(" ")}`;
		},
		xSpace() {
			return this.width / (this.value.length - 1);
		},
	},
};
</script>

<style lang="scss" scoped>
$height: 32px;
$borderWidth: 2px;

.tone-picker {
	position: relative;
	width: 100%;
	padding-bottom: 100%;
	border-radius: 4px;
	&:after {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		content: "";
		background: linear-gradient(to bottom, transparent 0%, #000 100%);
		border-radius: 4px;
	}
}

.marker {
	position: absolute;
	bottom: 0;
	left: 0;
	z-index: 2;
	display: inline-block;
	border: $borderWidth solid var(--color-border-i, #fff);
	border-radius: 50%;

	&.active {
		box-shadow: 0 0 0 2px var(--color-border, #333),
			0 0 0 2px var(--color-border, #333) inset;
	}
	&:focus {
		&.active,
		&.was-dragged {
			outline: none;
		}
		&:not(.active):not(.was-dragged) {
			outline-style: dashed;
			outline-offset: 0.5rem;
			outline-width: 3px;
			box-shadow: 0 0 0 2px var(--color-border, #333),
				0 0 0 2px var(--color-border, #333) inset;
		}
	}
}
</style>
