<template>
	<div class="feedback">
		<div class="back"><i class="iconfont" @click="back">&#xe615;</i></div>
		<div class="head">
			<h3>Get in touch with us</h3>
			<p>If you have any questions, suggestions, or just want to chat, feel free to contact us using the boxes below. We’ll 
get back to you within 1 day. </p>
		</div>
		<div class="fillin ">
			<div class="inputItem">
				<p>Name</p>
				<input v-model="name" :class="{err:nameError}" @focus="namefocus" class="inputin" />
			</div>
			<div class="inputItem">
				<p>Email Address</p>
				<input v-model="email" :class="{err:emailErr}" @focus="emailfocus" class="inputin"/>
			</div>
			<div class="inputItem">
				<p>Message</p>
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
		
	</div>
</template>

<script>
	if(process.browser) {
		
		require('~/assets/js/plugin/flexible.js')
	}
	import Dialog from "~/components/pageComponents/inquiry/Dialog"
	import { regExp, GetDateStr, addmulMonth } from '~/assets/js/plugin/utils'
	export default {
	
    name: 'inquiry',
    data () {
        return {
          	name:'',
          	nameError:false,
          	email: '',
			emailErr: false,
			textInfo: '',
			textInfoErr: false,
			
          	
          	isclick:false,
          	
          	
          	isShowAlert:false,
          	alertTitle:"",
          	alertMessage:"",
          	
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
			}else if(that.textInfo == '') {
				that.textInfoErr = true
			}else{
				if(window.localStorage.getItem("userid")){
						var obj = {
							userId:window.localStorage.getItem("userid"),
							objectType:"GENERAL",
							userName: that.name,
							emailAddress: that.email,
							message: that.textInfo,
							"utcOffset": new Date().getTimezoneOffset() / 60 * -1
							
						}
					}else{
						var obj = {
							objectType:"GENERAL",
							userName: that.name,
							emailAddress: that.email,
							message: that.textInfo,
							"utcOffset": new Date().getTimezoneOffset() / 60 * -1
							
						}
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
							that.isShowAlert=true
							that.alertMessage="Failed!"
						}

					}, function(response) {

					})
				}
				
			}
       },
    
    },
  	created:function(){
  
      	
  	},
    mounted: function() {
      
    },
    watch:{
    	
    }
}
</script>
<style lang="scss">
	//@import '~/assets/scss/_main.scss';
	//@import '~/assets/font/iconfont.css';
	
	
</style> 
<style lang="scss" scoped>
	.feedback{
		padding:0 0.586666rem;

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
			
				.inputin{
					width: 100%;
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
						width: 100%;
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