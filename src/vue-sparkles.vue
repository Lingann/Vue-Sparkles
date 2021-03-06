<template>
  <div id="sparkleWrapper" ref="wrapper" class="vue-sparkles">
    <div class="sparkleChildWrapper">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import SparkleInstance from "./sparkle-instance.vue";

export default {
	name: "VueSparkles",
	data () {
		return {
			instances: []
		};
	},
	props: {
		path: [String, Array],
		color: [String, Array]
	},
	methods: {
		generateSparkle () {
			return {
				id: String(this.random(10000, 99999)),
				createdAt: Date.now(),
				color: this.color,
				size: this.random(10, 20),
				style: {
					// Pick a random spot in the available space
					top: this.random(0, 100) + "%",
					left: this.random(0, 100) + "%",
					// Float sparkles above sibling content
					zIndex: this.random(1, 3)
				}
			};
		},
		random (min, max) {
			if (min === 0 && max === 1) {
				return Math.round(Math.random())
			}
			return Math.floor(Math.random() * (max - min)) + min;
		},
		addSparkle () {
			// Setting Path Values
			let sparklePath = "M80 0C80 0 84.2846 41.2925 101.496 58.504C118.707 75.7154 160 80 160 80C160 80 118.707 84.2846 101.496 101.496C84.2846 118.707 80 160 80 160C80 160 75.7154 118.707 58.504 101.496C41.2925 84.2846 0 80 0 80C0 80 41.2925 75.7154 58.504 58.504C75.7154 41.2925 80 0 80 0Z";
			if (this.customPath && this.multiplePaths) {
				sparklePath = this.path[this.random(0, this.path.length - 1)];
			} else if (this.customPath) {
				sparklePath = this.path;
			}

			// Setting Color Values
			let sparkleColor = "hsl(50deg, 100%, 50%)";
			if (this.customColor && this.multipleColors) {
				sparkleColor = "hsl(" + this.random(parseInt(this.color[0].h), parseInt(this.color[1].h)) + "deg, " + this.random(parseInt(this.color[0].s), parseInt(this.color[1].s)) + "%, " + this.random(parseInt(this.color[0].l), parseInt(this.color[1].l)) + "%)";
			} else if (this.customColor) {
				sparkleColor = this.color;
			}

			const parent = this.$refs.wrapper;
			const sparkle = this.generateSparkle();
			var SparkleClass = Vue.extend(SparkleInstance);
			var instance = new SparkleClass({
				propsData: {
					color: sparkleColor,
					size: sparkle.size,
					appliedStyle: sparkle.style,
					key: sparkle.appliedKey,
					createdAt: sparkle.createdAt,
					path: sparklePath
				}
			});
			instance.$mount();
			parent.appendChild(instance.$el);
			this.instances.push(instance);
		},
		tick () {
			this.addSparkle();
			window.setTimeout(this.tick, this.random(50, 500));
		}
	},
	mounted () {
		this.tick();
	},
	computed: {
		customPath () {
			return (this.path != null);
		},
		multiplePaths () {
			return Array.isArray(this.path);
		},
		customColor () {
			return (this.color != null);
		},
		multipleColors () {
			return Array.isArray(this.color);
		}
	},
};
</script>

<style scoped>
#sparkleWrapper{
  position: relative;
  display: inline-block;
}
.sparkleChildWrapper{
  position: relative;
  z-index: 2;
  font-weight: bold;
}
</style>
