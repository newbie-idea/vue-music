<template>
	<div class="songlist" ref='songlist'>
		<div class="img-wrap">
			<div class="gedan-img" ref='gedanimg'></div>
		</div>
		<div class="gedan-info">
			<img v-lazy='gedanimg' alt="" class="gedan_avator">
			<div class="wenzi">
				<p>{{name}}</p>
				<p>{{creator_name}}<span class="avator" ref='avator'></span></p>
			</div>
			<div class="do">
				<div class="do-item"><i class="icon">&#xe6e0;</i><p>{{recommendCount}}</p></div>
				<div class="do-item"><i class="icon">&#xe7d2;</i><p>{{commentCount}}</p></div>
				<div class="do-item"><i class="icon">&#xe618;</i><p>{{shareCount}}</p></div>
				<div class="do-item"><i class="icon">&#xe627;</i><p>下载</p></div>
			</div>
		</div>
		<div class="header" id='header'>
			<div class="back" v-on:click='back()'>
				<i class="icon">&#xe622;</i>
			</div>
			<p class="title">{{name}}</p>
			<div class="back">
				
			</div>
		</div>
		<div id='zanwei'></div>
		<ul class="body" ref="body">
			<li v-for='(item,index) in songList'>
				<gedansongitem v-bind:song='item' v-bind:index='index' v-on:listenplay='play'></gedansongitem>
			</li>
		</ul>

		<!-- 加载时的loading -->
		<div class="modal-loading" v-if='showModal'>
			<scale-loader :color="color"></scale-loader>
		</div>
	</div>
</template>
<script>
import gedansongitem from '../components/gedansongitem'
import ScaleLoader from 'vue-spinner/src/ScaleLoader'
	export default{
		data:function(){
			return {
				showModal:true,
				color:'#f01414',
				gedanId:this.$route.query.gedanId,
				gedanimg:'',
				creator_name:'',
				name:'',
				recommendCount:0,
				commentCount:0,
				shareCount:0,
				songList:[],
			}
		},
		components:{
			gedansongitem,ScaleLoader
		},
		methods:{
			back:function(){
				window.history.back();
			},
			play(id){
				//console.log(id);
				this.$emit('listenplay',id);
			}
		},
		created:function(){
			var _this = this;
			this.$http.get("http://120.79.167.62:3000/playlist/detail?id="+_this.gedanId)
			.then(res=>{
				
				_this.name = res.data.playlist.name;
				_this.gedanimg = res.data.playlist.coverImgUrl;
				_this.creator_name = res.data.playlist.creator.nickname;
				_this.$refs.gedanimg.style.backgroundImage='url('+res.data.playlist.coverImgUrl+')'
				_this.$refs.avator.style.backgroundImage='url('+res.data.playlist.creator.avatarUrl+')'
				_this.recommendCount = res.data.playlist.subscribedCount;
				_this.commentCount = res.data.playlist.commentCount;
				_this.shareCount = res.data.playlist.shareCount;
				_this.songList = res.data.playlist.tracks.map(function(e){
					return e;
				});
				_this.showModal = false;
				
			},error=>{
				console.log(error);
			})
		},
		mounted:function(){
			// 将页面的scrollTop设置为0；
			console.clear();
			this.$refs.songlist.scrollTop = 0;

			document.getElementById('zanwei').style.height = (250)+'px';
			var header = document.getElementById('header');
			document.addEventListener('scroll',function(){	
				if(window.pageYOffset >= 204){
					if(header.className.indexOf('white')===-1){
						header.className = header.className + ' white';
					}
				}else{
					header.className = header.className.replace(' white','');
				}
			})
			
		}
	}
</script>
<style scoped>
.slide-enter,.slide-leave-to{
	transform: translateX(100%);
}
.slide-enter-to,.slide-leave{
	transform: translateX(0);
}
.slide-enter-active,.slide-leave-active{
	transition: all 0.3s;
}
.songlist{
	position: relative;
	z-index: 20;
	background: #fff;
	min-height: 900px;
}
.img-wrap{
	overflow: hidden;
}
.gedan-img{
	height: 100%;
	background-repeat: no-repeat;
	background-size: 100% auto;
	background-position: center;
	filter: blur(30px);
}
.gedan-info,.img-wrap{
	position: fixed;
	top: 0;
	width: 100%;
	height: 250px;
}
.gedan-info{
	background-color: rgba(0,0,0,0.2);
	padding: 50px 15px 5px 15px;
	z-index: 20;
}
.header{
	height: 46px;
	position: fixed;
	top: 0;
	width: 100%;
	color: #fff;
	z-index: 22;
	transition: all 0.2s;
}
.back{
	width: 46px;
}
.back,.title{
	height: 46px;
	text-align: center;
	line-height: 46px;
	float: left;
}
.title{
	width: calc(100% - 92px);
}
.gedan_avator{
	width: 120px;
	height: 120px;
}
.wenzi{
	height: 120px;
	width: calc(100% - 125px);
	display: inline-block;
	vertical-align: top;
	padding-left: 15px;
	padding-top: 10px;
	color: #fff;
}
.avator{
	height: 20px;
	width: 20px;
	display: inline-block;
	vertical-align: top;
	border-radius: 10px;
	overflow: hidden;
	background-repeat: no-repeat;
	background-size: 100% auto;
	background-position: center;
	margin-left: 6px;
}
.wenzi p:nth-child(2){
	margin-top: 10px;
	font-size: 14px;
}
.do{
	height: 70px;
	display: flex;
	padding-top: 10px;
}
.do .do-item{
	flex: 1;
	color: #fff;
	text-align: center;
}
.do .do-item > i{
	font-size: 28px;
}
.do .do-item > p{
	font-size: 14px;
}
.body{
	position: relative;
	z-index: 21;
	background: #fff;
	padding-bottom: 50px;
}
.white{
	background-color: #ee4000;
	color: #fff;
}
.modal-loading{
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 48px;
	background: #fff;
	z-index: 99;
	display: flex;
	align-items: center;
	justify-content: center;
}
</style>