<template>
	<div class="inquiry">
		<div class="back"><i class="iconfont" @click="back">&#xe615;</i></div>
		<div class="head">
			<h3>Send My Inquiry</h3>
			<p>If you have questions or needs about any specific tour, we have professional consultants to answer your questions 
on a 1-1 basis.</p>
		</div>
		<div class="fillin ">
			<div class="inputItem">
				<p>Name<span>*</span></p>
				<input v-model="name" :class="{err:nameError}" @focus="namefocus" class="inputin" />
			</div>
			<div class="inputItem">
				<p>Email Address<span>*</span></p>
				<input v-model="email" :class="{err:emailErr}" @focus="emailfocus" class="inputin"/>
			</div>
			<div class="inputItem">
				<p>Date of Arrival</p>
				<input id="js_changetime" class="inputin" placeholder="Select Date" onfocus="this.blur()" v-model="dateTime" readonly type="text">
			</div>
			<div class="inputItem">
				<p>Number of People<span>*</span></p>
				<div class="peopleN">
					<div class="peopleshow" :class="peopleNub==0?'color8':''"  @click.stop="showchoose">
						<span v-if="peopleNub==0">Select People</span>
						<span v-if="peopleNub==1">1 Person</span>
						<span v-if="peopleNub>1">{{peopleNub}} People</span>
						<i class="iconfont icon-down" v-if="!isshowchoose">&#xe60f;</i>
						<i class="iconfont icon-down" v-else>&#xe63f;</i>
					</div>
					<div class="choosePeople" v-if="isshowchoose">
						<b>People</b>
						<div class="operation">
							<em class="iconfont" @click.stop="del(0)">&#xe64d;</em>
							<div>{{peopleNub}}</div>
							<em class="iconfont" @click.stop="add(0)">&#xe64b;</em>
						</div>
					</div>
				</div>
			</div>
			<div class="inputItem">
				<p>Message<span>*</span></p>
				<textarea v-model="textInfo" @focus="textInfofocus" :class="{err:textInfoErr}"></textarea>
			</div>
		</div>
		
		<div class="btn">
			<button @click.stop="submit">Submit</button>
		</div>
		<Dialog @setIsShowAlert="setShowAlert" 
				:isShowAlert="isShowAlert"
				:alertTitle="alertTitle"
				:alertMessage="alertMessage"
				></Dialog>
		<transition name="fade">
			<div class="win_bg" @click="showWinBg = false" v-if="showWinBg"></div>
		</transition>
	</div>
</template>

<script>
	if(process.browser) {
		
		require('~/assets/js/plugin/flexible.js')
	}
	import Dialog from "~/components/pageComponents/inquiry/Dialog"
	import { regExp, GetDateStr, addmulMonth } from '~/assets/js/plugin/utils'
	import Flatpickr from 'flatpickr';
	export default {
	
    name: 'inquiry',
    data () {
    	let id=this.$route.query.objectId
    	console.log(id)
        return {
          	name:'',
          	nameError:false,
          	email: '',
			emailErr: false,
			textInfo: '',
			textInfoErr: false,
			
      		dateTime:'',
          	options:{},
          	showWinBg:false,
          	isshowchoose:false,
          	peopleNub:0,
          	flatPickr:null,
          	objectType:"ACTIVITY",
          	isclick:false,
          	
          	
          	isShowAlert:false,
          	alertTitle:"",
          	alertMessage:"",
          	objectId:id
        }
    },
    components: {
        Dialog
    },
    methods: {
       setShowAlert(val){
       	this.isShowAlert=val
       },
       back(){
       	 history.back()
       },
       namefocus() {
			this.nameError = false
		},
		emailfocus() {
			this.emailErr = false
		},
		textInfofocus() {
			this.textInfoErr = false
		},
       submit(){
       		let that = this
			if(that.name == '' || regExp.isNub(that.name) || regExp.isCode(that.name)) {
				that.nameError = true
			} else if(!regExp.isEmail(that.email)) {
				that.emailErr = true
			}else if(that.peopleNub==0){
				console.log(111)
				that.isshowchoose=true
				console.log(that.isshowchoose)
			}else if(that.textInfo == '') {
				that.textInfoErr = true
			}else{
				var obj = {
					objectType: that.objectType,
					userName: that.name,
					emailAddress: that.email,
					message: that.textInfo,
					//phoneNumber:that.phone?that.phone:null,
					travelDate: that.dateTime ? that.dateTime : null,
					participants: that.peopleNub,
					objectId: that.objectId ? that.objectId : null,
//					destinations: that.destination ? that.destination : null,
					"utcOffset": new Date().getTimezoneOffset() / 60 * -1,
				}
				
				if(that.isclick==false){
					that.isclick=true
					that.axios.put("https://api.localpanda.com/api/user/feedback", JSON.stringify(obj), {
						headers: {
							'Content-Type': 'application/json; charset=UTF-8'
						}
					}).then(function(response) {
						
						
						if(response.data.succeed) {
							
							that.isShowAlert=true
				       		that.alertTitle="Submission completed!"
				       		that.alertMessage="Thank you for your feedback. We will get back to you within 1 day."	
							
						} else {
						
						}

					}, function(response) {

					})
				}
				
			}
       },
       showchoose(){
       	 this.isshowchoose=true
       },
       add() {
			this.peopleNub++
			
		},
		del() {
			if(this.peopleNub == 0) {
				this.peopleNub = 0
			} else {
				this.peopleNub--
			}	
		},
    },
  	created:function(){
  		let that=this
  		if(process.browser){
  			this.options = {
				minDate: GetDateStr(1),
				maxDate: addmulMonth(GetDateStr(1), 12),
				disableMobile: true,
				onOpen : function(selectedDates, dateStr, instance){
					
					that.showWinBg = true;
				},
				onChange(){
					that.showWinBg = false;
				}
			}
  		}
      	
  	},
    mounted: function() {
      	let that=this
      	that.flatPickr = new Flatpickr('#js_changetime',this.options);
      	document.getElementsByTagName("body")[0].addEventListener('click', function() {
				that.isshowchoose = false
				
		})
      	document.getElementsByTagName("body")[0].addEventListener('touchstart', function() {
				that.showWinBg=false
				
		})
    },
    watch:{
    	"flatPickr.isOpen":function(val,oldVal){
    		if(val){
    			this.isshowchoose = false
    			document.getElementsByTagName("html")[0].style.overflow="hidden"
    		}else{
    			
    			document.getElementsByTagName("html")[0].style.overflow="inherit"
    		}
    	}
    }
}
</script>
<style lang="scss">
	@import "~/assets/scss/G-ui/flatpickr.min.css";
	//@import '~/assets/scss/_main.scss';
	//@import '~/assets/font/iconfont.css';
	.inquiry{
		input::-webkit-input-placeholder, textarea::-webkit-input-placeholder {   
		/* WebKit browsers */   
		color: #878e95;   
		}   
		input:-moz-placeholder, textarea:-moz-placeholder {   
		/* Mozilla Firefox 4 to 18 */   
		color: #878e95   
		}   
		input::-moz-placeholder, textarea::-moz-placeholder {   
		/* Mozilla Firefox 19+ */   
		color: #878e95
		}   
		input:-ms-input-placeholder, textarea:-ms-input-placeholder {   
		/* Internet Explorer 10+ */   
		color: #878e95  
		}
		.flatpickr-calendar{
				position: fixed!important;
				min-width: 346px;
				min-height: 316px;
				left: 50%!important;
				top: 50%!important;
				transform: translate(-50%,-50%);
			}
			.flatpickr-calendar.animate.open{
				animation:all 0ms cubic-bezier(.23,1,.32,1)!important
			}
		.inputItem {
			
			.flatpickr-input {
				width:calc(100% - 0.24rem)!important;
				height: 1.146666rem;
				border:1px solid #dde0e0;
				border-radius: 0.08rem;
				padding-left: 0.24rem!important;
				font-size: 0.48rem!important;
			}
		}
	}
</style> 
<style lang="scss" scoped>
	.inquiry{
		padding:0 0.586666rem;
		.back {
			padding: 0.426666rem 0 0.56rem;
			i{
				font-size: 0.4rem;
			}
		}
		.head {
			margin-top: 0.106666rem;
			h3 {
				font-size: 0.8rem;
				font-weight: bold;
			}
			p {
				font-size:0.346666rem;

			}
		}
		.fillin{
			margin-top: 0.3rem;
			
			.inputItem{
				margin-top:0.44rem;
				/*border-bottom: 2px solid #ebebeb;*/
				p{
					font-size:0.48rem;
					span{
						color: red;
					}
				}
				.peopleN{
					position: relative;
					.peopleshow{
						width:calc(100% - 0.24rem);
						height: 1.146666rem;
						border:1px solid #dde0e0;
						border-radius: 0.08rem;
						padding-left: 0.24rem;
						line-height: 1.146666rem;
						font-size: 0.48rem;
						margin-top: 0.133333rem;
						i{
							position: absolute;
							right: 0.533333rem;
							
							font-size: 0.24rem;
							color: #878e95;
							
						}
						
					}
					.choosePeople{
						position: absolute;
						width:calc(100% - 0.613333rem);
						top:1.2rem;
						left: 0.026666rem;
						line-height:1.76rem;
						padding: 0 0.306666rem;
						background: #fff;
						box-shadow: 0px 0px 30px 0px rgba(0, 0, 0, 0.15);
						z-index:2;
		
						b{
							font-size: 0.56rem;
							float: left;
						}
						.operation{
							float:right;
							em{
								font-weight: bold;
								display: inline-block;
								width: 0.773333rem;
								height: 0.773333rem;
								border:1px solid #1bbc9d;
								border-radius: 50%;
								font-size: 0.186666rem;
								text-align:center;
								line-height:0.773333rem;
								color:#1bbc9d;
								
							}
							div{
								display: inline-block;
								font-size: 0.586666rem;
								margin: 0 0.626666rem;
								vertical-align: middle;
								border:none;
							}
						}
					}
				}
				.inputin{
					width:calc(100% - 0.24rem);
					height: 1.146666rem;
					border:1px solid #dde0e0;
					border-radius: 0.08rem;
					padding-left: 0.24rem;
					font-size: 0.48rem;
					margin-top: 0.133333rem;
					&:-webkit-placeholder { /* Mozilla Firefox 4 to 18 */
						color: #878e95; 
					}
					
				}
				textarea{
						height:1.893333rem;
						border:1px solid #dde0e0;
						border-radius: 0.08rem;
						width: calc(100% - 0.24rem);
						padding-left: 0.24rem;
						padding-top: 0.293333rem;
						margin-top: 0.346666rem;
						font-size: 0.346666rem;
						&:-webkit-placeholder { /* Mozilla Firefox 4 to 18 */
    						color: #dde0e0; 
						}
					}
			}
		}
		.btn{
			padding: 0.8rem 0;
			
			button{
				width: 100%;
				height: 1.146666rem;
				line-height:1.146666rem;
				text-align: center;
				background-image: linear-gradient(270deg,#009efd 0%,#1bbc9d 100%);
				font-size: 0.346666rem;
				color: #fff;
				font-weight: bold; 
				border-radius:0.573333rem;
			};
		}
		.win_bg{ width:100%;height: 100%; background-color:rgba(0,0,0,0.5); position:fixed; left:0; top:0;}
		.fade-enter-active, .fade-leave-active {
		transition: opacity .5s;
		}
		.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
		opacity: 0;
		}
		.err {
			border-color: red!important;
			color: red!important;
		}
		.color8{
			color: #878e95;
		}
	}
</style> 