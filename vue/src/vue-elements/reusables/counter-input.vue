<template>
	<div class="input-group rop-counter-group">
		<input class="form-input rop-counter" type="text" v-model="inputValueC" :id="id">
		<button class="btn input-group-btn increment-btn up" @mousedown="isPressed('up')" @mouseup="isReleased('up')"><i
				class="fa fa-fw fa-caret-up"></i></button>
		<button class="btn input-group-btn increment-btn down" @mousedown="isPressed('down')"
		        @mouseup="isReleased('down')"><i class="fa fa-fw fa-caret-down"></i></button>
	</div>
</template>

<script>
	let intervalID = null

	module.exports = {
		name: 'counter-input',
		props: {
			id: {
				default: ''
			},
			value: {
				default: 0,
				type: Number
			},
			allowNegative: {
				default: false,
				type: Boolean
			},
			minVal: {
				default: 0,
				type: Number
			},
			maxVal: {
				default: 0,
				type: Number
			}
		},
		data: function () {
			return {
				pressStartTime: null,
				incrementUp: 0,
				incrementDown: 0,
				inputValue: 0
			}
		},
		computed: {
			inputValueC: {
				get: function () {
					return this.value;
				},
				set: function (newValue) {
					this.inputValue = parseFloat(newValue)
					this.$emit('update:value', this.inputValue)
				}

			}
		},
		methods: {
			updateInput() {
				this.inputValue = this.value.toString();
				this.inputValue = parseFloat(this.inputValue);
				let now = new Date()
				let secondsPassed = parseInt((now.getTime() - this.pressStartTime.getTime()) / 1000)
				let increment = secondsPassed
				if (secondsPassed === 0) increment = 1
				increment = parseInt(increment);
				if (this.incrementUp === 1) {
					this.inputValue += increment
					if (this.inputValue > this.maxVal && this.maxVal !== 0) this.inputValue = this.maxVal
				}
				if (this.incrementDown === 1) {
					this.inputValue -= increment
					if (this.inputValue < 0 && this.allowNegative === false) this.inputValue = 0
					if (this.inputValue < this.minVal) this.inputValue = this.minVal
				}
				this.inputValue = parseFloat(this.inputValue.toFixed(1));
				this.$emit('update:value', this.inputValue)
			},
			isPressed(type) {
				if (type === 'up') {
					this.incrementUp = 1
				} else {
					this.incrementDown = 1
				}
				this.pressStartTime = new Date()
				this.updateInput()
				intervalID = setInterval(this.updateInput, 250)
			},
			isReleased(type) {
				if (type === 'up') {
					this.incrementUp = 0
				} else {
					this.incrementDown = 0
				}
				this.pressStartTime = null
				clearInterval(intervalID)
			}
		}
	}
</script>

<style>
	#rop_core .input-group.rop-counter-group {
		position: relative;
		width: 100%;
	}
	
	#rop_core .btn.increment-btn {
		position: absolute;
		right: 0;
		width: 1rem;
		height: 0.85rem;
		padding: 0.025rem 0.010rem;
		line-height: 0.3rem;
		z-index: 2;
		color: #ababab;
		border-color: #ababab;
	}
	
	#rop_core .btn.increment-btn:hover, #rop_core .btn.increment-btn:active, #rop_core .btn.increment-btn:focus {
		background-color: #00a6e3;
		color: #fff;
		border-color: #00a6e3;
	}
	
	#rop_core .btn.increment-btn.up {
		top: 0;
	}
	
	#rop_core .btn.increment-btn.down {
		bottom: 0;
	}
	
	input.rop-counter::-webkit-inner-spin-button {
		display: none;
	}
</style>