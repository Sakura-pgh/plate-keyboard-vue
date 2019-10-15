<!-- 
	车牌键盘控件
    支持：普通车牌/新能源车牌；特殊车牌（无牌车、WJ、使、民航、学、警、港、奥、领等）
    支持：默认车牌输入；任意删除修改某一位数；优先显示当前省份简称
	author:Sakura
    -->

<template>
	<transition name="slide">
		<section
			class="plate-key-cell"
			v-if="plateKeyBoardShow"
			@click.prevent.stop="hideKeyBoard"
		>
			<div class="keyboard-cell">
				<div class="close-cell">
					<p
						class="key-item--recommend"
						v-show="currentIndex===0"
						@click.prevent.stop="keyInput"
						:value="plateKeyOfProvince"
					>{{plateKeyOfProvince}}</p>
					<div
						class="close-btn"
						@click.prevent.stop="onClodeBtn"
					>{{(effectiveLen === plateLen) ? '确定' : '关闭'}}</div>
				</div>
				<div
					class="key-type"
					v-if="keyBoardType===1"
					@click.prevent.stop="keyInput"
				>
					<div
						class="key-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="key-item item-sm"
							v-for="(item,index) in abbBoard.rowOne"
							:key="index"
							:value="item"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
					<div
						class="key-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="key-item item-sm"
							v-for="(item,index) in abbBoard.rowTwo"
							:key="index"
							:value="item"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
					<div
						class="key-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="key-item item-sm"
							v-for="(item,index) in abbBoard.rowThree"
							:key="index"
							:value="item"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
					<div
						class="key-row last-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="key-item item-sm"
							:class="[(item==='民航' || item==='WJ')?'item-mm':'', ((item==='民航' || item==='WJ' || item==='使' || item==='无') && currentIndex === 1 && plateNoArr[0] === 'WJ')?'item-disabled':'']"
							v-for="(item,index) in abbBoard.rowFour"
							:key="index"
							:value="item"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
				</div>
				<div
					class="key-type"
					v-if="keyBoardType===2"
					@click.prevent.stop="keyInput"
				>
					<div
						class="key-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="key-item item-sm"
							:class="(plateNoArr.length <= 1 || currentIndex === 1)?'item-disabled':''"
							v-for="(item,index) in letterBoard.rowNum"
							:key="index"
							:value="item"
							data-num="num"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
					<div
						class="key-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="key-item item-sm"
							:class="(item==='O' || item==='I')?'item-disabled':''"
							v-for="(item,index) in letterBoard.rowA"
							:key="index"
							:value="item"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
					<div
						class="key-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="words-cell"
							v-if="wordsShow"
						>
							<img
								class="words-bg"
								src="../../assets/icon_words_cell.png"
							>
							<div class="words-item-cell">
								<div
									class="words-item words-sm"
									v-for="(item,index) in letterBoard.rowD"
									:key="index"
									:value="item"
									@click.prevent.stop="keyInput"
								>{{item}}</div>
							</div>
						</div>
						<div
							class="key-item item-sm"
							:class="(item==='字'&&currentIndex <= plateLen-2)?'item-disabled':''"
							v-for="(item,index) in letterBoard.rowB"
							:key="index"
							:value="item"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
					<div
						class="key-row"
						@click.prevent.stop="keyInput"
					>
						<div
							class="key-item item-sm"
							v-for="(item,index) in letterBoard.rowC"
							:key="index"
							:value="item"
							@click.prevent.stop="keyInput"
						>{{item}}</div>
					</div>
				</div>
				<div
					class="del-cell"
					@click.prevent.stop="keyInput"
					:value="-1"
				>
					<img
						class="del-icon"
						src="../../assets/icon_plate_del.png"
					>
				</div>
			</div>

		</section>
	</transition>
</template>

<script type="text/ecmascript-6">
export default {
	name: "plateKeyBoard",
	components: {},
	props: {
		// 是否展示键盘控件
		plateKeyBoardShow: {
			type: Boolean,
			required: true,
			default: false
		},
		// 键盘最大输入长度 新能源-8，普通-7
		plateLen: {
			type: Number,
			required: true,
			default: 7
		},
		// 默认车牌
		plateNo: {
			type: Array,
			required: false,
			default: () => []
		},
		// 当前编辑的位数
		currentIndex: {
			type: Number,
			required: true,
			default: 0
		},
		// 是否处于编辑状态 (当车牌为最大位数时 || 当前选中的位数小于数组的长度，为编辑状态)
		isUpdate: {
			type: Boolean,
			required: true,
			default: false
		},
		// 改变车牌类型时是否清空车牌 默认不清空
		ifChangePlateLenEmpty: {
			type: Boolean,
			required: false,
			default: false
		}
	},
	data() {
		return {
			keyBoardType: 1, // 1-简称，2-数字/字母
			abbBoard: {
				// 简称
				rowOne: [
					"京",
					"津",
					"沪",
					"渝",
					"冀",
					"豫",
					"云",
					"辽",
					"黑",
					"湘"
				],
				rowTwo: ["皖", "鲁", "新", "苏", "浙", "赣", "鄂", "桂", "甘"],
				rowThree: [
					"晋",
					"蒙",
					"陕",
					"吉",
					"闽",
					"贵",
					"粤",
					"青",
					"藏"
				],
				rowFour: ["川", "宁", "琼", "使", "WJ", "民航", "无"]
			},
			letterBoard: {
				// 字母
				rowNum: ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0"],
				rowA: ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"],
				rowB: ["A", "S", "D", "F", "G", "H", "J", "K", "L", "字"],
				rowC: ["Z", "X", "C", "V", "B", "N", "M"],
				rowD: ["领", "警", "港", "澳", "学"]
			},
			plateNoArr: [],
			wordsShow: false //是否展示‘字’包含的内容
		};
	},
	computed: {
		// 当前有效输入长度
		effectiveLen: function() {
			return this.plateNoArr.filter(Boolean).length;
		}
	},
	watch: {
		plateLen: function() {
			if (this.ifChangePlateLenEmpty) {
				this.plateNoArr = [];
				this.keyBoardType = 1;
			}
		},
		plateNo: {
			handler: function(newVal) {
				if (newVal && newVal.length > 0) {
					console.log("存在默认车牌", newVal);
					this.plateNoArr = [...newVal];
					console.log(this.plateNoArr);
				}
			},
			immediate: true
		}
	},
	created() {
		this.plateKeyOfProvince = "粤";
		console.log("初始化键盘 " + this.plateKeyOfProvince);
	},
	methods: {
		// 点击确定或关闭按钮
		onClodeBtn() {
			this.hideKeyBoard();
			if (this.effectiveLen === this.plateLen) {
				console.log("确定");
				this.$emit("confirmDo");
			}
		},

		// 隐藏键盘
		hideKeyBoard() {
			this.slicePlateNoArr();
			let detail = {
				plateNoArr: this.plateNoArr
			};
			this.$emit("plateInput", detail);
			this.$emit("hiddenKeyBoard");
		},
		// 隐藏字输入
		hideWords() {
			this.wordsShow = false;
		},
		// 设置键盘格式
		setkeyBoardType(currentIndex) {
			// 第一个选择了WJ之后，第二位可以继续输入省份简称
			if (currentIndex === 1 && this.plateNoArr[0] === "WJ") {
				this.keyBoardType = 1;
			} else if (currentIndex > 0) {
				this.keyBoardType = 2;
			} else {
				this.keyBoardType = 1;
			}
		},
		// 截取车牌长度
		slicePlateNoArr(plateLen = this.plateLen) {
			// 当车牌长度满足时，针对车牌类型截取数组，避免bug
			if (this.plateNoArr.length > plateLen - 1) {
				this.plateNoArr = this.plateNoArr.slice(0, plateLen);
				console.log("截取后", this.plateNoArr);
			}
		},
		// 键盘输入
		keyInput(e) {
			this.hideWords();
			console.log(e.currentTarget);
			let val = e.currentTarget.attributes[1].value;
			let index = this.currentIndex;
			// 避免空 || 乱码 || 不符合要求 的输入
			if (
				!val ||
				val.indexOf("key") > -1 ||
				e.currentTarget.className.indexOf("disabled") > -1
			) {
				return;
			}
			// 第二位之后才允许输入数字
			if (this.plateNoArr.length <= 1 && e.currentTarget.dataset.num) {
				return;
			}

			// 删除
			if (val === "-1") {
				if (this.plateNoArr[0] === "民" && this.currentIndex <= 1) {
					// 删除民航时
					this.$set(this.plateNoArr, 0, ""); //深层更新数组的内容
					this.$set(this.plateNoArr, 1, ""); //深层更新数组的内容
					index = 0;
				} else {
					// 其它情况
					console.log(this.plateNoArr);
					if (!this.plateNoArr[this.currentIndex]) {
						// 如果为空 则光标向前一位
						if (this.currentIndex > 0) {
							index = this.currentIndex - 1;
						} else {
							index = 0; //到0就不能减啦 否则变-1了
						}
						this.$set(this.plateNoArr, index, ""); //深层更新数组的内容
						//如果后面没值了 则数组长度--
						if (
							this.plateNoArr
								.slice(index, this.plateNoArr.length)
								.filter(Boolean).length === 0
						) {
							this.plateNoArr.splice(
								index,
								this.plateNoArr.length
							);
						}
						console.log(this.plateNoArr);
					} else {
						// 如果不为空 则将该位置为空，光标不向前移动
						this.$set(this.plateNoArr, this.currentIndex, ""); //深层更新数组的内容
					}
				}
			} else {
				// 其他输入
				if (val === "O" || val === "I" || val === "字") {
					console.log("字");
					if (this.currentIndex > this.plateLen - 2 && val == "字") {
						this.wordsShow = true;
						return;
					}
				} else if (val === "民航") {
					// 前两个分别为民 航
					this.$set(
						this.plateNoArr,
						this.currentIndex,
						val.substring(0, 1)
					); //深层更新数组的内容
					this.$set(
						this.plateNoArr,
						this.currentIndex + 1,
						val.substring(1)
					); //深层更新数组的内容
					index = 1;
				} else {
					console.log("更新输入# " + val);
					this.$set(this.plateNoArr, this.currentIndex, val); //深层更新数组的内容
				}
			}

			this.slicePlateNoArr(); // 当车牌长度满足时，针对车牌类型截取数组，避免bug

			if (
				this.effectiveLen > this.plateLen - 1 &&
				index >= this.plateLen - 1
			) {
				console.log("位数已满", index);
				index = this.plateLen - 1;
			}
			let detail = {
				plateNoArr: this.plateNoArr,
				currentIndex: index
			};
			console.log(detail);
			if (this.isUpdate && val === "-1") {
				// 删除后-更新输入
				console.log("deleteInput");
				this.$emit("deleteInput", detail);
			} else {
				console.log("plateInput");
				this.$emit("plateInput", detail);
			}
		}
	}
};
</script>

<style scoped lang="scss">
$bgKeyboard: #cfd4db; //键盘背景色
$keyDisabled: #cacbd3; //不可点击件字体颜色
$borderColor: #d9d9d9; //边框颜色
$mainColor: #3296fa; //主颜色

// 可以设置不同的进入和离开动画
.slide-enter-active {
	transition: all 0.3s ease;
}
.slide-leave-active {
	transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-enter,
.slide-leave-to {
	transform: translateY(100px);
	opacity: 0;
}

// 居中盒子
@mixin flex-box-center {
	display: flex;
	justify-content: center;
	align-items: center;
}

// 两端对齐盒子
@mixin flex-box-between {
	display: flex;
	justify-content: space-between;
	align-items: center;
}

.plate-key-cell {
	position: fixed;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	width: 100%;
	z-index: 9;
	.keyboard-cell {
		background: $bgKeyboard;
		position: absolute;
		bottom: 0;
		width: 100%;
		border-top: 1px solid $borderColor;
	}
	.close-cell {
		width: 100%;
		background: #fff;
		text-align: right;
		position: relative;
		.close-btn {
			display: inline-block;
			width: 60px;
			height: 35px;
			font-size: 14px;
			color: $mainColor;
			padding-right: 16px;
			line-height: 35px;
		}
		.key-item--recommend {
			position: absolute;
			width: 90px;
			left: 50%;
			transform: translateX(-50%);
			height: 100%;
			background-color: $mainColor;
			color: #fff;
			@include flex-box-center;
			font-size: 20px;
		}
	}
	.key-type {
		position: relative;
		width: 100%;
		.key-row {
			@include flex-box-center;
			position: relative;
			&.last-row {
				margin-right: 32px;
			}
		}
		.words-cell {
			position: absolute;
			width: 195px;
			height: 63px;
			top: -50px;
			right: 2px;
			.words-bg {
				display: block;
				width: 100%;
				height: 100%;
				border: 0;
				position: absolute;
				top: 0;
				right: 0;
				z-index: 1;
			}
			.words-item-cell {
				display: block;
				width: 100%;
				height: 100%;
				@include flex-box-center;
				position: absolute;
				top: 0;
				right: 0;
				z-index: 3;
				.words-item {
					margin-top: -3px;
				}
			}
		}
		.words-item,
		.key-item {
			text-align: center;
			height: 44px;
			font-size: 18px;
			color: #333;
			background: #fff;
			line-height: 44px;
			border-radius: 5px;
			margin: 5px 2px;
			box-shadow: 0px 1px 0px 0px rgba(134, 137, 142, 1);
			@keyframes myfirst {
				0% {
					box-shadow: inset -5px 0px 25px rgba(0, 0, 0, 0.1);
					background: #f4f4f4;
				}
				100% {
					background: #fff;
				}
			}
			&:not(.item-disabled):hover {
				animation: myfirst 0.3s;
			}
		}
		.item-disabled {
			color: $keyDisabled;
		}
		.words-sm,
		.item-sm {
			width: 33px;
		}
		.item-mm {
			width: 48px;
		}
	}
	.del-cell {
		position: absolute;
		bottom: 5px;
		right: 2px;
		width: 51px;
		height: 44px;
		background: #fff;
		border-radius: 5px;
		.del-icon {
			display: block;
			border: 0;
			width: 100%;
			height: 100%;
		}
	}
}
</style>