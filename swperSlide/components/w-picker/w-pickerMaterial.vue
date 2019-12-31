<template>
	<!-- 组件讲解  三级联动选择，但是因为有些数据只有一级 或者有些数据只有两级 -->
	<!-- 这里根据数据的情况进行对比 优化了 不完全三级数据 -->
	<view class="w-picker">
		<view class="mask" :class="{'show':showPicker}" @tap="maskTap" @touchmove.stop.prevent catchtouchmove="true"></view>
		<view class="w-picker-cnt" :class="{'show':showPicker}">
			<view class="w-picker-hd" @touchmove.stop.prevent catchtouchmove="true">
			  <view class="w-picker-btn" @tap="pickerCancel">取消</view>
			  <view class="w-picker-btn" :style="{'color':themeColor}" @tap="pickerConfirm">确定</view>
			</view>
			<view class="w-picker-view" v-if="mode=='linkage'">
				<picker-view :indicator-style="itemHeight" :value="pickVal" @change="bindChange"  @touchstart="touchStart" @touchend="touchEnd">
					<picker-view-column v-for="(col,colIndex) in data" :key="colIndex">
						<view class="w-picker-item" v-for="(item,index) in col" :key="index">{{item.label}}</view>
					</picker-view-column>
				</picker-view>
			</view>	
		</view>
	</view>
</template>

<script>
	
	function oneOf (value, validList) {
		for (let i = 0; i < validList.length; i++) {
		  if (value === validList[i]) {
			return true;
		  }
		}
		throw new Error('mode无效，请选择有效的mode!');
		return false;
	}
	export default {
		name:'vPicker',
		data() {
			return {
				result:[],
				data:{},
				checkArr:[],
				checkValue:[],
				pickVal:[],
				showPicker:false,
				resultStr:"",
				itemHeight:`height: ${uni.upx2px(88)}px;`,
				confirmFlag:true
			};
		},
		computed:{
			
		},
		props:{
			mode:{
				type:String,
				validator(mode){
					let modeList=['half','date', 'dateTime', 'yearMonth','time','region','selector','limit','limitHour','range','linkage'];//过滤无效mode;
					return oneOf(mode,modeList);
				},
				default(){
					return "date"
				}
			},
			themeColor:{
				type:String,
				default(){
					return "#f5a200"
				}
			},
			
			defaultVal:{
				type:Array,
				default(){
					return [0,0,0,0,0,0,0]
				}
			},
		
			selectList:{
				type:Array,
				default(){
					return [];
				}
			},
		
			linkList:{
				type:Array,
				default(){
					return []
				}
			},
			value:{
				type:Array,
				default(){
					return []
				}
			},
			level:{
				type:[Number,String],
				default:2
			},
			timeout:{
				type:Boolean,
				default:false
			}
		},
		watch:{
			mode(){
				this.initData();
			},
			selectList(){
				this.initData();
			},
			linkList(){
				this.initData();
			}
		},
		methods:{
			touchStart(){
				if(this.timeout){
					this.confirmFlag=false;
				}
			},
			touchEnd(){
				if(this.timeout){
					setTimeout(()=>{
						this.confirmFlag=true;
					},500)
				}
			},
			getLinkageVal(value,flag){
				console.log(value)
				let dval=[];
				let arr=value;
				let list=this.linkList;
				let lev=this.level;
				let k=0;
				let checkArr=[];
				let checkValue=[];
				let resultStr="";
				let data=[];
				switch(lev){
					case 2:
						dval=[0,0];
						break;
					case 3:
						dval=[0,0,0];
						break;
				}
				const getData=(obj,key,str)=>{
					if(key<lev){
						data.push(obj);
						if(arr.length==7){
							let item=obj[0];
							checkArr.push(item.label);
							checkValue.push(item.value);
							resultStr+=item.label;
							if(item.children){
								getData(item.children,key+=1);
							}
						}else{
							obj.map((v,j)=>{
								if(flag?v.value==arr[key]:v.label==arr[key]){
									dval[key]=j;
									checkArr.push(v.label);
									checkValue.push(v.value);
									resultStr+=v.label;
									if(v.children){
										getData(v.children,key+=1);
									}
								}
							});
						}
						return {
							data,
							dval,
							checkArr,
							checkValue,
							resultStr
						};
					}else{
						return false;
					}
				};
				return getData(list,k);
			},
			/***
			*操作代码
			***/ 
			maskTap(){
				this.$emit("cancel",{
					checkArr:this.checkArr,
					defaultVal:this.pickVal
				});
				this.showPicker = false;
			},
			show(){
				this.showPicker = true;
			},
			hide(){
				this.showPicker = false;
			},
			pickerCancel(){
				this.$emit("cancel",{
					checkArr:this.checkArr,
					defaultVal:this.pickVal
				});
				this.showPicker = false;
			},
			pickerConfirm(e){
				if(!this.confirmFlag){
					return;
				}
				switch(this.mode){
					
					
					case "linkage":
						this.$emit("confirm",{
							checkArr:this.checkArr,
							checkValue:this.checkValue,
							defaultVal:this.pickVal,
							result:this.resultStr
						});
						break;
					default:
						
						break;
				}
				this.showPicker = false;
			},
			
			/***
			*核心代码模块
			***/ 
			bindChange(val){
				console.log(val)
				let _this=this;
				let arr=val.detail.value;
				let year="",month="",day="",hour="",minute="",second="",note=[],province,city,area;
				let checkArr=_this.checkArr;
				let days=[];
				let months=[];
				let mode=_this.mode;
				let col1,col2,col3,d,a,h,m;
				let xDate=new Date().getTime();
				switch(mode){
					
					case "linkage":
					console.log(mode)
						let c1,c2,c3;
						let list=this.linkList;
						// console.log(list)
						console.log(_this.data)
						c1=_this.data[0][arr[0]]||_this.data[0][0];
						c2=_this.data[1][arr[1]]||_this.data[1][0];
						console.log('c0')
						if(this.level==3){
							// c3=_this.data[2][arr[2]]||_this.data[2][0];
							console.log(_this.data)
							// c3=_this.data[2][arr[2]];
							if(_this.data.length > 2){
								c3=_this.data[2][arr[2]]||_this.data[2][0];
							}
							if(c1.label!=checkArr[0]){
								arr[1] = 0;
								arr[2] = 0;
								console.log('s')
								if(list[arr[0]].children && list[arr[0]].children.length !== 0){
									_this.data[1]=list[arr[0]].children;
									_this.data[2]=list[arr[0]].children[arr[1]].children;
									c2=_this.data[1][arr[1]]||_this.data[1][0];
									c3=_this.data[2][arr[2]]||_this.data[2][0];
								}else{
									_this.data[1]=[];
									_this.data[2]=[list[arr[0]].children];
									c2=[];
									c3=[];
								}
								
							};
							if(c2.label!=checkArr[1]){
								console.log('w')
								arr[2] = 0;
								// 如果有二级存在
								if(list[arr[0]].children && list[arr[0]].children.length !== 0){
									// 如果有三级存在
									if(list[arr[0]].children[arr[1]].children && list[arr[0]].children[arr[1]].children.length !== 0){
										console.log('w1')
										_this.data[2]=list[arr[0]].children[arr[1]].children;
										c3=_this.data[2][arr[2]]||_this.data[2][0];
									}else{
										console.log('w2')
										_this.data[2]=[];
										c3=[];
									}
									console.log('w3')
								}else{
									// 如果有一级存在
									console.log('cwww')
								}
								
								
							};
							
							
							
							if(list[arr[0]].children && list[arr[0]].children.length !== 0){
								console.log('q1')
								_this.checkArr=[c1.label,c2.label,];
								_this.checkValue=[
									_this.data[0][arr[0]]?_this.data[0][arr[0]].value:_this.data[0][0].value,
									_this.data[1][arr[1]]?_this.data[1][arr[1]].value:_this.data[1][0].value,
								];
								_this.resultStr=c1.label+c2.label;
								if(list[arr[0]].children[arr[1]].children && list[arr[0]].children[arr[1]].children.length !== 0){
									console.log('q2')
									_this.checkArr=[c1.label,c2.label,c3.label];
									_this.checkValue=[
										_this.data[0][arr[0]]?_this.data[0][arr[0]].value:_this.data[0][0].value,
										_this.data[1][arr[1]]?_this.data[1][arr[1]].value:_this.data[1][0].value,
										_this.data[2][arr[2]]?_this.data[2][arr[2]].value:_this.data[2][0].value
									];
									_this.resultStr=c1.label+c2.label+c3.label;
								}
							}else{
								console.log('q3')
								console.log(c1.label)
								_this.checkArr=[c1.label];
								console.log(_this.data[0][0].value)
								console.log('q31')
								_this.checkValue=[
									// _this.data[0][arr[0]]?_this.data[0][arr[0]].value:_this.data[0][0].value,
									c1.value,
									
								];
								console.log('q32')
								_this.resultStr=c1.label;
								console.log('q33')
							}
							
						}else{
							if(c1.label!=checkArr[0]){
								if(list[arr[0]].children && list[arr[0]].children.length !== 0){
									_this.data[1]=list[arr[0]].children;
									arr[1] = 0;
									c2=_this.data[1][arr[1]]||_this.data[1][0];
								}else{
									_this.data[1]=[];
									arr[1] = 0;
									c2=[];
								}
								
							};
							if(list[arr[0]].children && list[arr[0]].children.length !== 0){
								_this.checkArr=[c1.label,c2.label];
								_this.checkValue=[
									_this.data[0][arr[0]]?_this.data[0][arr[0]].value:_this.data[0][0].value,
									_this.data[1][arr[1]]?_this.data[1][arr[1]].value:_this.data[1][0].value
								];
								_this.resultStr=c1.label+c2.label;
							}else{
								_this.checkArr=[c1.label];
								_this.checkValue=[
									_this.data[0][arr[0]]?_this.data[0][arr[0]].value:_this.data[0][0].value,
								];
								_this.resultStr=c1.label;
							}
							
						}
						break;
					
				}
				_this.$nextTick(()=>{
					_this.pickVal=arr;
				})
			},
			initData(){
				let _this=this;
				let data={};
				let mode=_this.mode;
				let year,month,day,hour,minute,second,province,city,area;
				let col1,col2,col3;
				let dVal=[];
				switch(mode){
					case "linkage":
						let init;
						if(_this.value){
							init=_this.getLinkageVal(_this.value,true);
						}else{
							init=_this.getLinkageVal(_this.defaultVal);
						}
						dVal=init.dval;
						data=init.data;
						_this.checkArr=init.checkArr;
						_this.checkValue=init.checkValue;
						_this.resultStr=init.resultStr;
						break;
					
				}
				_this.data=data;
				
				_this.$nextTick(()=>{
					_this.pickVal=dVal;
				})
			}
		},
		mounted() {
			this.initData();
		}
	}
</script>

<style lang="scss">
	.w-picker{
		position: relative;
		z-index: 888;
		.mask {
		  position: fixed;
		  z-index: 1000;
		  top: 0;
		  right: 0;
		  left: 0;
		  bottom: 0;
		  background: rgba(0, 0, 0, 0.6);
		  visibility: hidden;
		  opacity: 0;
		  transition: all 0.3s ease;
		}
		.mask.show{
			visibility: visible;
			opacity: 1;
		}
		.w-picker-cnt {
		  position: fixed;
		  bottom: 0;
		  left: 0;
		  width: 100%;
		  transition: all 0.3s ease;
		  transform: translateY(100%);
		  z-index: 3000;
		}
		.w-picker-cnt.show {
		  transform: translateY(0);
		}
		.w-picker-hd {
		  display: flex;
		  align-items: center;
		  padding: 0 30upx;
		  height: 88upx;
		  background-color: #fff;
		  position: relative;
		  text-align: center;
		  font-size: 32upx;
		  justify-content: space-between;
		  .w-picker-btn{
		  	font-size: 30upx;
		  }
		}
		
		.w-picker-hd:after {
		  content: ' ';
		  position: absolute;
		  left: 0;
		  bottom: 0;
		  right: 0;
		  height: 1px;
		  border-bottom: 1px solid #e5e5e5;
		  color: #e5e5e5;
		  transform-origin: 0 100%;
		  transform: scaleY(0.5);
		}
		.w-picker-item {
		  text-align: center;
		  width: 100%;
		  height: 88upx;
		  line-height: 88upx;
		  text-overflow: ellipsis;
		  white-space: nowrap;
		  font-size: 30upx;
		}
		.w-picker-view {
		  width: 100%;
		  height: 476upx;
		  overflow: hidden;
		  background-color: rgba(255, 255, 255, 1);
		  z-index: 666;
		}
		picker-view{
			height: 100%;
		}
	}

</style>
