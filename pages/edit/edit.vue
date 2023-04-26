<template>
	<view class="edit">
		<form @submit="onSubmit">
			<view class="item">
				<input v-model="formValue.title" type="text" name="title" placeholder="请输入标题"></input>
			</view>
			<view class="item">
				<input v-model="formValue.author" type="text" name="author" placeholder="作者"></input>
			</view>
			<view class="item" >
				<textarea v-model="formValue.content"  name="content" placeholder="请输入内容"></textarea>
			</view>
			<!-- 上传文件 -->
			<view class="item">
				<uni-file-picker 
					v-model="imageValue" 
					fileMediatype="image" 
					mode="grid" 
					@success="uploadSuccess" 
				/>
			</view>
			<view class="item">
				<button form-type="reset">重置</button>
				<button form-type="submit" type="primary" :disabled="inDisabled(formValue)">提交</button>
			</view>
		</form>
	</view>
</template>

<script>
	let id;
	export default {
		data() {
			return {
				picurls:[],
				imageValue:[],
				formValue:{
					title:"",
					author:"",
					content:""
				}
			}
		},
		onLoad(e) {
			id=e.id
			
		},
		onShow() {
			this.getDetail()
		},
		methods:{
			uploadSuccess(e){
				// console.log(e);
				this.picurls=e.tempFilePaths
			
			},
			getDetail(){
				uniCloud.callFunction({
					name:"art_get_row",
					data:{
						id
					}
				}).then(res=>{
					this.formValue=res.result.data[0]
					if(!this.formValue.picurls) return;
					let urls=this.formValue.picurls.map(item=>{
						return {url:item}
					})
					this.imageValue=urls
				})
			},
			inDisabled(obj){
				for(let key in obj){
					if(!obj[key]){
						return true
					}
				}
			},
			onSubmit(){
				let _picurls=this.imageValue.map(item=>{
					return item.url
				})
				uniCloud.callFunction({
					name:"art_update_row",
					data:{
						detail:this.formValue,
						picurls:_picurls
					}
				}).then(res=>{
					uni.showToast({
						title:"修改成功"
					})
					setTimeout(()=>{
						uni.navigateBack()
					},800)
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
.edit{
	padding:30rpx;
	.item{
		padding-bottom: 20rpx;
		input,textarea{
			border: 1rpx solid #eee;
			height: 80rpx;
			padding: 0 20rpx;
		}
		textarea{
			height: 200rpx;
			width: 100%;
			box-sizing: border-box;
		}
		button{
			margin-bottom: 20rpx;
		}
	}
}
</style>
