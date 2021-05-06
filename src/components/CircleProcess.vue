<template>
	<div class="circle-process-container" :id="canvasId"></div>
</template>
<script lang="ts">
	import { ref, toRef, toRefs, defineComponent, onMounted, watch} from "vue";

	export default defineComponent({
		name: "CircleProcess",
		props: {
			id: {
				type: String,
				required: true,
			},
			radius: {
				type: Number,
				default: 60,
			},
			padding: {
				type: Number,
				default: 20,
			},
			speed: {
				type: Number,
				default: 2,
			},
			circleLine: {
				type: Number,
				default: 12,
			},
			outerColor: {
				type: String,
				default: "#f0f0f0",
			},
			innerColor: {
				type: String,
				default: "#0085ff",
			},
			fontSize: {
				type: String,
				default: "20px",
			},
			fontColor: {
				type: String,
				default: "#F47C7C",
			},
			total: {
				type: Number,
				required: true,
			},
			list: {
				type: Array
			}
		},
		setup: (props, ctx) => {
			// 画布ID  唯一
			const canvasId = props.id;
			// 半径
			const radius = props.radius;
			// 画布内边距
			const padding = props.padding;
			// canvans宽高
			const canvasWidth: number = (radius + padding) * 2;
			const canvasHeight: number = (radius + padding) * 2;
			// 圆心
			const point: number = canvasHeight / 2;
			// 360° / 100
			const percentRadius: number = (2 * Math.PI) / 100;
			// 动画速度
			const speed = props.speed;
			// 环形宽度
			const circleLine = props.circleLine;
			// 外环颜色
			const outerColor = props.outerColor;
			// 内环颜色
			const innerColor = props.innerColor;
			// 进度文本样式
			const fontSize = props.fontSize;
			const fontColor = props.fontColor;
			// 当前进度
			let nowPercent: number = 0;
			// 总进度 需要监听total的变化，通过toref转成ref，保持响应性
			let total = toRef(props, 'total');
			// 动画
			let animation: any;
			//记录每次动画执行结束的时间
			let lastTime: number = 0;
			// 设备dpr
			let dpr: number = 1;
			let canvasCtx: any;

			const getPixelRatio = (canvasCtx: any): number => {
				let backingStore = canvasCtx.backingStorePixelRatio ||
					canvasCtx.webkitBackingStorePixelRatio ||
					canvasCtx.mozBackingStorePixelRatio ||
					canvasCtx.msBackingStorePixelRatio ||
					canvasCtx.oBackingStorePixelRatio ||
					canvasCtx.backingStorePixelRatio || 1;
				return (window.devicePixelRatio || 1) / backingStore;
			}
			const init = (): void => {
				const container = document.querySelector(`#${canvasId}`);
				const canvas = document.createElement("canvas");
				canvasCtx = canvas.getContext("2d");
				dpr = getPixelRatio(canvasCtx);
				canvas.style.width = canvasWidth + 'px';
    			canvas.style.height = canvasHeight + 'px';
				canvas.width = canvasWidth * dpr;
				canvas.height = canvasHeight * dpr;
				drawFrame();
				container && container.appendChild(canvas);
			};
			const drawOuterCircle = (): void => {
				canvasCtx.save();
				canvasCtx.scale(dpr, dpr);
				canvasCtx.beginPath();
				canvasCtx.strokeStyle = outerColor;
				canvasCtx.lineWidth = circleLine;
				canvasCtx.arc(point, point, radius, Math.PI * 2, false);
				canvasCtx.stroke();
				canvasCtx.closePath();
				canvasCtx.restore();
			};

			const drawInnerCircle = (n: number): void => {
				canvasCtx.save();
				canvasCtx.scale(dpr, dpr);
				canvasCtx.beginPath();
				canvasCtx.strokeStyle = innerColor;
				canvasCtx.lineWidth = circleLine;
				canvasCtx.arc(
					point,
					point,
					radius,
					-Math.PI / 2,
					-Math.PI / 2 + n * percentRadius,
					false
				);
				canvasCtx.stroke();
				canvasCtx.restore();
			};

			const drawText = (n: number): void => {
				canvasCtx.save();
				canvasCtx.scale(dpr, dpr);
				canvasCtx.fillStyle = fontColor;
				canvasCtx.font = fontSize + " Arial";
				canvasCtx.textAlign = "center";
				canvasCtx.textBaseline = "middle";
				canvasCtx.fillText(n.toFixed(2) + "%", point, point);
				canvasCtx.restore();
			};

			const drawFrame = (): void => {
				animation = requestAnimationFrame(function fn(t) {
					canvasCtx.clearRect(0, 0, canvasWidth, canvasHeight);
					drawOuterCircle();
					if (nowPercent > total.value) {
						drawText(total.value);
						drawInnerCircle(total.value);
						cancelAnimationFrame(animation);
					} else {
						lastTime = t;
						drawText(nowPercent);
						drawInnerCircle(nowPercent);
						nowPercent += speed;
						animation = requestAnimationFrame(fn);
					}
				});
			};
			onMounted(() => {
				init();
			});

			watch(total, newTotal => {
				drawFrame();
			});

			return {
				canvasId,
			};
		},
	});
</script>