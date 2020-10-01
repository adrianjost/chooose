<template>
	<div class="picker-wrapper">
		<div class="picker">
			<svg id="graph" :viewBox="viewBox" class="graph">
				<path :d="line" stroke="black" fill="transparent" />
			</svg>
			<div class="markers">
				<div
					v-for="(v, i) in value"
					:key="i"
					class="marker"
					:style="getMarkerStyles(i)"
				>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import { svgPath, bezierCommand } from "./helpers/svg";

export default {
	props: {
		value: {
			type: [Array],
			required: true,
		},
		options: {
			type: Object,
			default: () => ({}),
		},
	},
	computed: {
		width() {
			return (this.value.length - 1) * 25;
		},
		height() {
			return 100;
			const min = Math.min(...this.value);
			const max = Math.max(...this.value);
			return min >= 0 ? max : max - min;
		},
		zeroYOffset() {
			return 100;
			return Math.max(...this.value);
		},
		viewBox() {
			return `0 0 ${this.width} ${this.height}`;
		},
		chartZero() {
			const max = Math.max(...this.value);
			return { x: 0, y: 100 };
		},
		handleCoordinates() {
			return this.value.map((y, i) => ({
				x: this.posX(i),
				y: y * 100,
			}));
		},
		line() {
			return svgPath(
				this.handleCoordinates.map(({ x, y }) => ({
					x,
					y: this.zeroYOffset - y,
				})),
				bezierCommand
			);
		},
		xSpace() {
			return this.width / (this.value.length - 1);
		},
	},
	methods: {
		markerPosX(i) {
			return `${(i / this.value.length) * 100}%`;
		},
		posX(i) {
			return this.xSpace * i;
		},
		getMarkerStyles(i) {
			const { radius, borderWidth } = {
				radius: 16,
				borderWidth: 2,
			};
			return {
				padding: `${radius - borderWidth}px`,
				"margin-bottom": `${-radius}px`,
				"margin-left": `${-radius}px`,
				"border-width": `${borderWidth}px`,
				left: `${(i / (this.value.length - 1)) * 100}%`,
				bottom: `${this.value[i] * 100}%`,
				"background-color": `rgba(0,0,0, ${1 - this.value[i]})`,
			};
		},
	},
};
</script>

<style lang="scss" scoped>
$height: 32px;
$borderWidth: 2px;

.picker-wrapper {
	padding: 16px;
}
.picker {
	position: relative;
	height: 200px;
}
.graph {
}

.marker {
	position: absolute;
	bottom: 0;
	left: 0;
	height: 0;
	z-index: 2;
	display: inline-block;
	border: $borderWidth solid var(--color-border-i, #333);
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
