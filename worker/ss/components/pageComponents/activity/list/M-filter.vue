<template>
	<div class="M-filter">
		
		<div class="cont">
			<div class="head">
				<i class="iconfont" @click.stop="close">&#xe629;</i>
				<div class="title">Filter</div>
				<div class="clear" @click.stop="clear" v-if="checkedCategory.length>0||checkedDurations.length>0||checkedTourtype.length>0">Clear</div>
			</div>
			<div class="filter-detail">
				<div class="fliterList">
					<div class="title">CATEGORIES</div>
					<div class="detail">
						<el-checkbox-group v-model="checkedCategory">
							<div class="checkboxlist" v-for="(item,key,index) in category">
								<el-checkbox :label="key" :key="key">{{key}} ({{item}})</el-checkbox>
							</div>
						</el-checkbox-group>
					</div>
				</div>
				<div class="fliterList">
					
					<div class="title">DURATION</div>
					<div class="detail">
						<el-checkbox-group v-model="checkedDurations">
							<div class="checkboxlist" v-for="(i,key,index) in durations">
								<el-checkbox v-if="key==0" :label="key" :key="key">Half Day ({{i}})</el-checkbox>
								<el-checkbox v-if="key==1" :label="key" :key="key">{{key}} Day ({{i}})</el-checkbox>
								<el-checkbox v-if="key>1" :label="key" :key="key">{{key}} Days ({{i}})</el-checkbox>
							</div>
						</el-checkbox-group>
					</div>
				</div>
				<div class="fliterList" >
					<div class="title">THEMES</div>
					<div class="detail">
						<el-checkbox-group v-model="checkedTourtype">
							<div class="checkboxlist" v-for="(i,key,index) in tourtype">
								<el-checkbox :label="key" :key="key">{{key}} ({{i}})</el-checkbox>
							</div>
						</el-checkbox-group>
					</div>
				</div>
				
				
			</div>
		</div>
		<div class="subimtBtn">
			<button @click.stop="submitFilter">See experiences</button>
		</div>
	</div>
</template>

<script>
	
	//import { checklist } from 'vux'
	export default {
		props: [
		"category", 
		"durations",
		"tourtype",
		"sort",
		"loc",
		"checkedCategory",
		"checkedDurations",
		"checkedTourtype"
		],
		name: "M-filter",
		data() {
			return {
				isshowcategory: false,
				//category: ["zhangsna", "lisi"],
				commonList:["shanghai","beijing"],
				isshowdurations: false,
				//durations: ['1', '0'],
				checklist001:[],
				labelPosition:'',
				isshowtourtype: false,
				//tourtype: ['end', 'ending', 'ending', 'ending', 'ending', 'ending', 'ending'],
				
				records: '',
			}
		},
		 components: {
		    //checklist
		 },
		methods: {
			change(){
				console.log(this.checkedCategory)
			},
			clear() {
				location.href="/activity/list/mobile/"+this.loc
			},
			close() {
				this.$emit("callBack", false)
			},
			submitFilter() {
				for(let i = 0; i < this.checkedTourtype.length; i++) {
					this.checkedTourtype[i] = this.checkedTourtype[i].replace(/&/g, "And")
				}
				let opctions =this.delNull({
					category: this.checkedCategory,
					durations: this.checkedDurations,
					tourtype: this.checkedTourtype,
				})
				opctions=JSON.stringify(opctions)
				this.sort=JSON.stringify(this.sort)
				location.href = "/activity/list/mobile/" + this.loc + "?options=" + opctions  + (/SCORE/.test(this.sort)?"":"&sort=" + this.sort);

			},
			delNull(obj){
				var newOpctions = {};
				for(var key in obj){
					if(obj[key].length){
						newOpctions[key] = obj[key];
					}
				}
				return newOpctions;
			}
		},
		mounted:function(){
			console.log(this.category)
		}
	

	}
</script>
<style lang="scss">
.M-filter{

	.el-checkbox__inner {
		width: 0.426666rem;
		height: 0.426666rem;
		border-radius: 0.04rem;
		&:after {
			left: 0.133333rem;
			top: 0.066666rem;
		}
	}
	.el-checkbox{
		width:100%;
	}
	.el-checkbox__label {
		font-size: 0.426666rem;
		color: #353a3f;
	}
	
}
</style>
<style lang="scss" scoped>
	.M-filter {
		position: fixed;
		top: 0;
		left: 0;
		z-index: 9999;
		background: #fff;
		height: 100%;
		.cont {
			position: absolute;
			background: #fff;
			width: 100%;
			height: calc(100% - 3.333333rem);
			overflow-y: auto;
			padding-bottom: 1.946666rem;
			-webkit-overflow-scrolling: touch;
			max-height: 100%;
			.head {
				border-bottom: 1px solid #dde0e0;
				height: 1.386666rem;
				line-height: 1.386666rem;
				text-align: center;
				position: relative;
				font-size: 0.373333rem;
				font-weight: bold;
				position: fixed;
				width: 100%;
				z-index: 888;
				background: #fff;
				i {
					position: absolute;
					left: 0.586666rem;
					font-size: 0.4rem;
				}
				.clear {
					position: absolute;
					right: 0.586666rem;
					top: 0;
					color: #1bbc9d;
				}
			}
			.filter-detail {
				padding-top: 1.386666rem;
				
				.title{
					padding-left: 0.586666rem;
					font-size: 0.32rem;
					line-height: 0.986666rem;
					height: 0.986666rem;
					background: #faf9f8;
					
					
				}
				.detail{
					border-top: 1px solid #dde0e0;
					border-bottom:1px solid #dde0e0;
					.checkboxlist{
						margin:0 0.586666rem;
						line-height: 1.24rem;
						height: 1.24rem;
						border-bottom:1px solid #dde0e0;
						&:last-child{
							border: 0;
						}
					}
				}
				
			}
		}
		.subimtBtn {
			position: fixed;
			bottom: 0;
			left: 0;
			width: 100%;
			padding: 0.373333rem 0.586666rem;
			z-index: 8999;
			background: #fff;
			button {
				width: calc(100% - 1.173333rem);
				height: 1.2rem;
				line-height: 1.2rem;
				background-image: linear-gradient(270deg, #009efd 0%, #1bbc9d 100%);
				text-align: center;
				color: #fff;
				border-radius: 0.6rem;
				font-size: 0.346666rem;
				font-weight: bold;
			}
		}
	}
</style>