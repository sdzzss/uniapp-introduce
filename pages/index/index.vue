<template>
	<view class="head">
		<button class="button" type="primary" @click="inputDialogToggle">
			<text class="button-text"></text>
		</button>
		<view class="uni-title">高密市柴沟中心卫生院</view>
		<view class="uni-date">{{ nowDateTime.year }} 年 {{ nowDateTime.month }} 月 {{ nowDateTime.date }} 日</view>
		<view style="right: 5vw;color: white;" class="uni-date">{{ nowDateTime.hour }} ： {{ nowDateTime.minute }}</view>
	</view>
	
	<view class="dialog-box">
		<text class="dialog-text">{{ value }} </text>
	</view>
	<view style="height: 32vh;position: relative;">
		<image class="img" :src="getsrc()"></image>
		<view class="name">
			{{ userdata.username }}
		</view>
		<view class="title">
			{{ userdata.title }}
		</view>
	</view>
	<view class="introduction" v-html="userdata.introduction">
	</view>
	<view class="KSintroduction" v-html="introduction">
	</view>

<view class="footer">开诊时间：上午8:00-11:30；下午14:00-17:00</view>

<view>
	<!-- 输入框示例 -->
	<uni-popup ref="inputDialog" type="dialog">
		<uni-popup-dialog ref="inputClose"  mode="input" title="设置科室" value=""
			placeholder="请输入科室" @confirm="dialogInputConfirm"></uni-popup-dialog>
	</uni-popup>
</view>
</template>


<script>
import { onMounted,getCurrentInstance,ref } from 'vue'
	export default {
		setup() {
			const instance = getCurrentInstance();
			
			onMounted(()=>{
				uni.getStorage({
				key: 'key_Header',
					success: function (res) {
						instance.data.value = res.data;
					}
				});
				
				
				setInterval(() => {
				    try {
				        getWorkState();
				
				    } catch (error) {
				        console.log(error);
				    }
				
				}, 5000)
			});
			
			
			const getWorkState = async () => {
			   const res = await uni.request({
			   				    url: `http://127.0.0.1:3001/webApi/workState/getid/${instance.data.value}`,
			   				    method:'GET'
			   				});
				
			const res2 = await uni.request({
								url: `http://127.0.0.1:3001/webApi/user/list/${res.data.data[res.data.data.length - 1].id}`,
								method:'GET'
							});
							
				instance.data.userdata = res2.data.data[0]
				
			const res3 = await uni.request({
							    url: `http://127.0.0.1:3001/webApi/department/list/${instance.data.value}`,
							    method:'GET'
							});
				instance.data.introduction = res3.data.data[0].introduction
				
				
				var getTime = new Date().getTime(); //获取到当前时间戳
				var time = new Date(getTime); //创建一个日期对象
				function nowDate(time) {
				    var year = time.getFullYear(); // 年
				    var month = (time.getMonth() + 1).toString().padStart(2, '0'); // 月
				    var date = time.getDate().toString().padStart(2, '0'); // 日
				    var hour = time.getHours().toString().padStart(2, '0'); // 时
				    var minute = time.getMinutes().toString().padStart(2, '0'); // 分
				    // var second = time.getSeconds().toString().padStart(2, '0'); // 秒
				    return {
				        year,
				        month,
				        date,
				        hour,
				        minute
				    }
				}
				    instance.data.nowDateTime = nowDate(time)
			}
			
			
		},
		data() {
			return {
				value: '',
				userdata:{},
				introduction:{},
				nowDateTime:{}
			}
		},
		methods: {
			
			inputDialogToggle() {
				this.$refs.inputDialog.open()
			},
			
			dialogInputConfirm(val) {
				uni.showLoading({
					title: '正在添加'
				})

				setTimeout(() => {
					
					uni.setStorageSync('key_Header', val);
					uni.getStorage({
					key: 'key_Header',
						success: function (res) {
							this.val = res.data;
						}
					});
					
					
					
					uni.hideLoading()
					this.value = val
					// 关闭窗口后，恢复默认内容
					this.$refs.inputDialog.close()
				}, 1000)
			},
			getsrc(){
				return 'http://127.0.0.1:3001' + this.userdata.avatar
			}
		}
		
		
	}
</script>


<style>
.button{
	width: 9vw;
	height: 4.5vh;
	background-image:url('../../static/7c80cb249128db288c4a2f7d440d633.jpg');
	background-position: center center;
	background-size: cover;
	margin: 0;
}
.dialog-box{
	width: 100%;
	height: 20vh;
	margin: 0;
}
.uni-title{
    display: inline-block;
    position: absolute;
    top: 0;
    left: 10vw;
    font-size: 1.7vh;
    line-height: 4.4vh;
}
.uni-date{
    display: inline-block;
    position: absolute;
    top: 0;
    right: 26vw;
    font-size: 1.5vh;
    line-height: 4.4vh;
}
.dialog-text{
    line-height: 21vh;
    color: white;
    font-size: 7vh;
    margin: 0 auto;
    text-align: center;
    display: table;
}
.img{
	width: 42.4vw;
    height: 29vh;
    margin: 2vh 5vw;
}
.name{
	float: right;
	width: 40vw;
	text-align: center;
	line-height: 22vh;
	position: relative;
	margin-right: 7vw;
	font-size: 4vh;
}
.title{
	width: 41vw;
    text-align: center;
    line-height: 10vh;
    margin-right: 7vw;
    font-size: 3.5vh;
    float: right;
    position: absolute;
    right: 0;
    bottom: 0;
}
.introduction{
	width: 66vw;
    height: 13.5vh;
    font-size: 1.8vh;
    margin: 1vh 2vw 0 30vw;
	word-wrap:break-word;
	word-break:break-all;
	line-height: 3vh;
}
.KSintroduction{
    width: 66vw;
    height: 22.5vh;
    font-size: 1.8vh;
    margin: 1.3vh 2vw 0 30vw;
    word-wrap: break-word;
    word-break: break-all;
    line-height: 3vh;
}
pre{
	white-space: pre-wrap;
	white-space: -moz-pre-wrap;
	white-space: -pre-wrap;
	white-space: -o-pre-wrap;
	word-wrap: break-word;
}
.footer{
	color: white;
	text-align: center;
	line-height: 6vh;
	font-size: 1.8vh;
}
</style>
