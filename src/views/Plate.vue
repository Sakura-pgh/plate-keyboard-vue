<template>
	<article class="query-owe-content">
		<section class="plate-cell">
			<div class="car-type-tip">
				<div class="type-left">
					<span>{{plateInfo.typeName}}</span>
				</div>
				<div
					class="type-right"
					@click="_changePlateType"
				>
					<i class="change-tip"></i>
					<span>{{plateInfo.changeName}}</span>
				</div>
			</div>
			<div class="input-cell">
				<div
					class="input-item"
					v-for="(item,index) in plateLen"
					:key="index"
					:class="{'active' : plateKeyBoardShow && currentIndex===index}"
					@click="showKeyBoard(index)"
				>{{plateInfo.list[index]}}</div>
			</div>
		</section>

		<section
			class="query-btn-cell"
			@click="_commitInfo"
		>
			<p
				class="btn"
				:class="{'active' : canSubmit}"
			>确定</p>
		</section>

		<!-- 车牌键盘 -->
		<PlateKeyBoard
			ref="plateKeyBoard"
			:plateLen="plateLen"
			:plateKeyBoardShow="plateKeyBoardShow"
			:plateNo.sync="plateNo"
			:currentIndex.sync="currentIndex"
			:isUpdate="isUpdate"
			@hiddenKeyBoard="hiddenKeyBoard"
			@plateInput="plateInput"
			@deleteInput="deleteInput"
			@confirmDo="_commitInfo"
		></PlateKeyBoard>
	</article>
</template>

<script>
import PlateKeyBoard from "../components/PlateKeyBoard/PlateKeyBoard";
export default {
	name: "",
	components: {
		PlateKeyBoard
	},
	data() {
		return {
			plateNo: [],
			plateInfo: {
				typeName: "普通车牌",
				changeName: "新能源牌",
				isOrdinaryPlate: true, //true为普通车牌，false为新能源
				list: []
			},
			plateLen: 7, //车牌长度
			plateKeyBoardShow: false, //是否显示车辆键盘
			currentIndex: 0 //当前输入的位数
		};
	},
	created() {},
	computed: {
		isUpdate: function() {
			let isUpdate = false;
			if (
				this.plateInfo.list.length === this.plateLen ||
				this.currentIndex <= this.plateInfo.list.length
			) {
				//当车牌为最大位数时 || 当前选中的位数小于数组的长度，为更新状态
				isUpdate = true;
			}
			return isUpdate;
		},
		effectiveLen: function() {
			return this.plateInfo.list.filter(Boolean).length;
		},
		canSubmit: function() {
			// 车牌号必须 其他信息选填，有则必须符合校验规则
			return this.effectiveLen === this.plateLen ? true : false;
		}
	},
	methods: {
		//唤起车牌键盘
		showKeyBoard(index) {
			console.log(index);
			if (this.isUpdate && index <= this.plateInfo.list.length) {
				this.$refs.plateKeyBoard.setkeyBoardType(index);
				this.currentIndex = index;
			} else {
				console.log("否分支");
				this.currentIndex = this.plateInfo.list.length;
				this.$refs.plateKeyBoard.setkeyBoardType(this.currentIndex);
			}
			console.log(this.currentIndex);
			console.log(this.isUpdate);
			this.plateKeyBoardShow = true;
		},
		//隐藏车牌键盘
		hiddenKeyBoard() {
			this.plateKeyBoardShow = false;
		},
		//输入车牌
		plateInput: function(e) {
			console.log("输入车牌");
			let data = e.plateNoArr;
			this.plateInfo.list = data;
			if (e.currentIndex === this.plateLen - 1) {
				//输入完了
				this.currentIndex = e.currentIndex;
			} else {
				this.currentIndex = e.currentIndex + 1;
			}
			this.$refs.plateKeyBoard.setkeyBoardType(this.currentIndex);
		},
		// 更新状态下删除字段
		deleteInput: function(e) {
			let data = e.plateNoArr;
			console.log(data);
			console.log(this.plateInfo);
			this.$set(this.plateInfo, "list", data);
			this.currentIndex = e.currentIndex;
			this.$refs.plateKeyBoard.setkeyBoardType(this.currentIndex);
			console.log("更新删除");
		},

		//改变车牌类型
		_changePlateType() {
			let overFlag = false; // 是否输入完成标志
			[this.plateInfo.typeName, this.plateInfo.changeName] = [
				this.plateInfo.changeName,
				this.plateInfo.typeName
			]; // 交换变量
			if (this.currentIndex === this.plateLen - 1) {
				overFlag = true; //输入完了
			}

			this.plateInfo.isOrdinaryPlate = !this.plateInfo.isOrdinaryPlate;
			this.plateLen = this.plateInfo.isOrdinaryPlate ? 7 : 8;
			this.plateInfo.list = this.plateInfo.list.slice(0, this.plateLen);

			this.$refs.plateKeyBoard.slicePlateNoArr(this.plateLen);

			if (overFlag && this.effectiveLen === 7) {
				// 普通车牌输入满了时
				this.currentIndex = this.plateLen - 1; //车牌可输入长度变了
			}
		},

		// 提交信息
		_commitInfo() {
			if (!this.canSubmit) {
				return;
			}
			this.plateKeyBoardShow = false;
			alert(this.plateInfo.list.join(""));
		}
	}
};
</script>

<style lang="scss" scoped>
$mainColor: #3296fa; //主颜色
$borderColor: #dbdbdb; //边框颜色

.query-owe-content {
	background-color: #fff;
	.plate-cell {
		margin: 20px 25px;
		height: 100px;
		.car-type-tip {
			display: flex;
			justify-content: space-between;
			.type-left {
				font-size: 14px;
				color: #333;
				font-weight: bold;
			}
			.type-right {
				position: relative;
				z-index: 11; //层级大于键盘蒙层
				display: inline-block;
				font-size: 12px;
				color: $mainColor;
				.change-tip {
					display: inline-block;
					height: 10px;
					width: 14px;
					background: url("../assets/icon_car_change_switch.png")
						no-repeat;
					background-size: 100% 100%;
					margin-right: 4px;
				}
			}
		}
		.input-cell {
			position: relative;
			z-index: 11; //层级大于键盘蒙层
			display: flex;
			margin-top: 16px;
			.input-item {
				flex: 1;
				height: 54px;
				line-height: 54px;
				border-right: 1px solid $borderColor;
				border-bottom: 1px solid $borderColor;
				border-top: 1px solid $borderColor;
				font-size: 22px;
				color: #333;
				text-align: center;
				position: relative;
				background: #fff;
				&:first-child,
				&:nth-child(3) {
					border-left: 1px solid $borderColor;
				}
				&.active {
					border: 2px solid #3eabe9;
					margin-top: -1px;
					&:nth-child(1) {
						margin-left: -2px;
					}
					&:nth-child(2) {
						margin-right: 8px;
					}
				}
				&:nth-child(2) {
					margin-right: 10px;
				}
				&:last-child {
					margin-left: 0;
					margin-right: 0;
				}
			}
		}
	}
	.query-btn-cell {
		margin-top: 68px;
		padding: 0px 25px;
		.btn {
			position: relative;
			z-index: 12;
			width: 100%;
			height: 44px;
			line-height: 44px;
			color: #fff;
			font-size: 16px;
			text-align: center;
			border-radius: 4px;
			background: #d9d9d9;
			&.active {
				background: $mainColor;
			}
		}
	}
}
</style>
