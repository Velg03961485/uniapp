<template>
	<view class="content">
		
		<view class="text-area">
			<view class=""  @click="chooseServise">
				请选择
			</view>
			<text class="title">{{text}}</text>
		</view>
		
		<vPicker
			mode="linkage"
			:level="3"
			:value="defultMaterialKey"
			:defaultVal="defultMaterialName"
			@confirm="onConfirmService"
			ref="linkage"
			:linkList="serviceList"
			themeColor="#f00"
		></vPicker>
		
	</view>
</template>

<script>
	import vPicker from "../../components/w-picker/w-pickerMaterial.vue";
	export default {
		components:{
			vPicker
		},
		data() {
			return {
				text: '花港观鱼',
				defultMaterialKey:['13','131','1311'],
				defultMaterialName:['花港观鱼','御龙岸','金鲢鱼'],
				serviceList:[
					{label:'断桥残雪',value:'11',children:[]},
					{label:'平湖秋月',value:'12',children:[
						{label:'瘦西湖',value:'121',children:[]},
						{label:'望月楼',value:'122',children:[]},
					]},
					{label:'花港观鱼',value:'13',children:[
						{label:'御龙岸',value:'131',children:[
							{label:'野鸳鸯',value:'1311',children:[]},
							{label:'金鲢鱼',value:'1312',children:[]},
						]},
						{label:'香儿坊',value:'132',children:[
							{label:'桃花酒',value:'1321',children:[]},
							{label:'杏花露',value:'1322',children:[]},
							{label:'深龙泉',value:'1333',children:[]},
						]},
					]},
				],
			}
		},
		onLoad() {

		},
		methods: {
			chooseServise() {
				
				this.$refs['linkage'].show();
			},
			onConfirmService(val){
				console.log(val)
				this.text = (val.checkArr.slice(-1)).join(',');
				
			},
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
		margin-top: 120rpx;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
		margin-left: 60rpx;
	}
</style>
