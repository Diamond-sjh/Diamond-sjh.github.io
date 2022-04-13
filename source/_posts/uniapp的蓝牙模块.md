---
title: unipush的蓝牙模块
date: 2022-4-13 10:30:55
tags: app
categories: 踩坑经验
typora-root-url: unipush的蓝牙模块
---

<meta name="referrer" content="no-referrer"/>

# unipush的蓝牙模块

## 一、蓝牙模块初始化

这里主要目的就是检测一下手机蓝牙是否打开

- 方法一

  ```javascript
  // 蓝牙初始化
  getBluetoot(){
      // HTML5+ API打开初始化蓝牙
      plus.bluetooth.openBluetoothAdapter({
  		success:(res)=> { //蓝牙已打开
              uni.getBluetoothAdapterState({//蓝牙的匹配状态
                  success:(res1)=>{
                      console.log(res1,'本机设备的蓝牙已打开')
                      // 获取已配对蓝牙列表
                      this.bluetooth_list()
                  },
                  fail(error) {
                     uni.showToast({icon:'none',title: '查看手机蓝牙是否打开'})
                  }
              })
          },
          fail:err=>{ // 蓝牙未打开 
  			console.log('open failed: '+JSON.stringify(e));
  		}
      })
  },
  ```

- 方法二

  ```javascript
  uni.openBluetoothAdapter({
  	success:(res)=> { //已打开
  		uni.getBluetoothAdapterState({//蓝牙的匹配状态
  			success:(res1)=>{
  				console.log(res1,'“本机设备的蓝牙已打开”')	
  				// 开始搜索蓝牙设备
  				this.startBluetoothDeviceDiscovery()
  			},
  			fail(error) {
  				uni.showToast({icon:'none',title: '查看手机蓝牙是否打开'	
  			}
  		});
  		
  	},
  	fail:err=>{ //未打开 
  		uni.showToast({icon:'none',title: '查看手机蓝牙是否打开'});
  	}
  })
  ```

  

## 二、打开蓝牙

参考链接地址：https://www.jianshu.com/p/0b2f01a576fa

```javascript
openPhoneBluetooth(){
		let main, BluetoothAdapter, BAdapter;
		switch(uni.getSystemInfoSync().platform){
			case 'android':
				main  = plus.android.runtimeMainActivity();
				BluetoothAdapter = plus.android.importClass("android.bluetooth.BluetoothAdapter");
				BAdapter = BluetoothAdapter.getDefaultAdapter();
				if(!BAdapter.isEnabled()) {  
					BAdapter.enable();  
				}
				break;
			case 'ios':
				console.log('运行在ios上')
				break;
			default:
				console.log('运行在开发者工具上')
				break;
		}
	},
```

## 三、获取蓝牙列表

##### 	重点就是获取到了 **deviceId** 这是连接蓝牙的重要ID，存起来后面我们会用到

##### 	把获取到的蓝牙列表存起来，方便页面展示，选择蓝牙连接

1. 获取已经配对的蓝牙列表

   ```javascript
   // 获取已配对蓝牙列表
   bluetooth_list() {  
       var main = plus.android.runtimeMainActivity();  
       var BluetoothAdapter = plus.android.importClass("android.bluetooth.BluetoothAdapter");  
       var BAdapter = BluetoothAdapter.getDefaultAdapter();  
       var Context = plus.android.importClass("android.content.Context");  
       var lists = BAdapter.getBondedDevices();  
       plus.android.importClass(lists);  
       var len = lists.size();  
       var iterator = lists.iterator();  
       plus.android.importClass(iterator);  
       let bluetoothList = []
       while (iterator.hasNext()) {  
           var d = iterator.next();  
           plus.android.importClass(d);
           // 获取的已配对蓝牙名称和地址 存起来
           bluetoothList.push({name:d.getName(),deviceId:d.getAddress(),connect:false})
       }  
   },
   ```

2. 获取附近蓝牙设备

   ```javascript
   // 开始搜索蓝牙设备
   startBluetoothDeviceDiscovery(){
   	uni.startBluetoothDevicesDiscovery({
   		success: (res) => {
   			console.log('startBluetoothDevicesDiscovery success', res)
   			// 发现外围设备
               // 监听搜索到新设备事件
   			uni.onBluetoothDeviceFound((res) => {
                   // ["name", "deviceId"]
                   // 把搜索到的设备存储起来，方便我们在页面上展示
                   if(this.list.indexOf(res.devices[0].deviceId)==-1){
                       this.list.push(res.devices[0].deviceId)
                   }
               })
   		},fail:err=>{
   			console.log(err,'错误信息')
   		}
   	})
   }
   ```

## 四、点击选择自己需要连接的蓝牙设备

**把连接蓝牙的 deviceId 存起来后面我们会用到**

- 方法一（连接已配对的蓝牙设备，不需要停止蓝牙搜索，因为不会一直搜索附近蓝牙）

  ```javascript
  // 点击蓝牙连接(选择设备把deviceId传进来)
  connetBlue(item) {
      let that = this
      //data里面建立一个deviceId，存储起来
      this.deviceId = item.deviceId
      uni.createBLEConnection({
          // 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
          deviceId: item.deviceId, //设备id
          success: (res) => { // 蓝牙连接成功
              uni.onBLEConnectionStateChange(function (res) {
                  // 该方法回调中可以用于处理连接意外断开等异常情况
                  console.log(res)
              })
              //获取蓝牙设备的所有服务
              //注：这个地方使用了setTimeout等待一秒种再去获取，直接获取我们可能出现获取不到的情况。
              setTimeout(()=>{
                  that.getBLEDeviceServices(that.deviceId);
              },1500)
          },
          fail: (res) => { // 蓝牙连接失败
              console.log(res)
          },
      })
  },
  ```

  

- 方法二

  ```javascript
  //选择设备连接吧deviceId传进来
  createBLEConnection(deviceId){
  	let thit = this
  	//data里面建立一个deviceId，存储起来
  	this.deviceId = deviceId
  	//连接蓝牙
  	uni.createBLEConnection({
  	// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
  	  deviceId:this.deviceId,
  	  success(res) {
  	  	// 蓝牙连接成功停止搜索
  		uni.stopBluetoothDevicesDiscovery({ 
  			success: e => {
  				this.loading = false
  				console.log('停止搜索蓝牙设备:' + e.errMsg); 
  			},
  			fail: e => {
  				console.log('停止搜索蓝牙设备失败，错误码：' + e.errCode);
  			}
  		});	
          // 获取已连接蓝牙的所有服务
          that.getBLEDeviceServices();
  	  },fail(res) {
  		console.log("蓝牙连接失败",res)
  	  }
  	})
  },
  
  
  ```

  

## 五、获取已连接蓝牙的所有服务

**把服务列表和需要用到的服务uuid（serviceId）存储起来，后面我们会用到**

**注：这个地方使用了setTimeout等待一秒种再去获取，直接获取我们可能出现获取不到的情况**

- 方法一(此方法在调用的时候做了延迟，故不需要再次加定时器)

  ```javascript
  // 获取服务列表
  getBLEDeviceServices(deviceId) {
  	uni.getBLEDeviceServices({
  		// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
  		deviceId: deviceId,
  		success: (res) => {
  			if(res.services && res.services.length > 0){
  				this.serverList = res.services  // 所有的服务
  				this.serviceId = res.services[3].uuid  // 存储所需求的服务对应的uuid
  				this.getBLEDeviceCharacteristics() //获取特征值
  			}
  		},
  		fail: function(res) {
  			console.log(res)
  		},
  	})
  },
  ```

  

- 方法二

  ```javascript
  //获取蓝牙的所有服务
  getBLEDeviceServices(){
      // 使用了setTimeout等待一秒种再去获取，直接获取我们可能出现获取不到的情况**
  	setTimeout(()=>{
  		uni.getBLEDeviceServices({
  		  	// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
  		  	deviceId:this.deviceId,
              success:(res)=>{
  				//这里会获取到好多个services  uuid  我们只存储我们需要用到的就行，这个uuid一般硬件厂家会给我们提供
  				if(res.services && res.services.length > 0){
                      this.serverList = res.services  // 所有的服务
                      this.serviceId = res.services[3].uuid  // 存储所需求的服务对应的uuid
                      this.getBLEDeviceCharacteristics() //获取特征值
                  }
  		 	 }
  		})
  	},1000)
  },
  
  
  ```

## 六、获取蓝牙特征值

**这里需要穿2个参数了，就是上面的两个id，分别是deviceId、services**
**这里获取的特征值的uuid才是我们真正需要操作的uuid**

- 方法一

  ```javascript
  // 获取特征值
  			getBLEDeviceCharacteristics() {
  				uni.getBLEDeviceCharacteristics({
  					// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
  					deviceId: this.deviceId,
  					// 这里的 serviceId 需要在上面的 getBLEDeviceServices 接口中获取
  					serviceId: this.serviceId,
  					success: (res) => {
  						console.log(res)
  						if(res.characteristics && res.characteristics.length > 0){
  							this.characteristics = res.characteristics //服务serviceId对应的所有特征值
  							this.notifyUUid = res.characteristics[2].uuid //需要用到读取数据 特征值uuid（根据自己需求）
  							this.writeUUid = res.characteristics[3].uuid //需要用到写入数据 特征值uuid（根据自己需求）
                              this.notifyBLECharacteristicValueChange()// 启用蓝牙特征值变化时的 notify 功能
  						}	
  					},
  					fail: function(res) {
  						console.log(res)
  					},
  				})
  			},
  ```

  

- 方法二

  ```javascript
  //获取蓝牙特征
  getBLEDeviceCharacteristics(){
  	console.log("进入特征");
  	setTimeout(()=>{
  		uni.getBLEDeviceCharacteristics({
  		  // 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
  		  deviceId:this.deviceId,
  		  // 这里的 serviceId 需要在 getBLEDeviceServices 接口中获取
  		  serviceId:this.serviceId,
  		  success:(res)=>{
  			if(res.characteristics && res.characteristics.length > 0){
                  this.characteristics = res.characteristics //服务serviceId对应的所有特征值
                  this.notifyUUid = res.characteristics[2].uuid //需要用到读取数据 特征值uuid（根据自己需求）
                  this.writeUUid = res.characteristics[3].uuid //需要用到写入数据 特征值uuid（根据自己需求）
                  this.notifyBLECharacteristicValueChange() // 启用蓝牙特征值变化时的 notify 功能
              }
  			
  		  },
  		  fail:(res)=>{
  			console.log(res)
  		  }
  		})
  	},1000)
  }
  ```

## 七、启用蓝牙设备特征值变化时的 notify 功能

```javascript
notifyBLECharacteristicValueChange() {
		uni.notifyBLECharacteristicValueChange({
			deviceId:this.deviceId,//设备uuid
			serviceId:this.serviceId,//服务对应的uuid
			characteristicId:this.notifyUUid,//监听的特征值uuid
			state:true,
			success: (res) => {
				console.log("广播开启成功----" + bluetoothInfo.notifyUUid)
                //监听特征值的变化
				this.onBLECharacteristicValueChange();
			},
			fail: (err) => {
				console.error(err)
				uni.showToast({
				    title: '蓝牙连接异常',
					icon: 'error',
				    duration: 2000
				});
			}
		})
	},
```



## 八、监听蓝牙设备的特征值变化

```javascript
// 监听低功耗蓝牙设备的特征值变化（callback是返回触发的方法，根据需求）
	onBLECharacteristicValueChange(callback) {
        let macValue = ''
        let timer = null
		uni.onBLECharacteristicValueChange((res) => {
			macValue = macValue + this.ab2str(res.value);//处理得到的返回值
            //防抖处理，防止同一个数据多次返回（可不要）
			if(timer){
				clearTimeout(timer) 
				timer = null
			}else{
				timer = setTimeout(() => {
					callback(macValue)
					clearTimeout(timer)
					timer = null
				},1000)
			}
		})
	},
```

## 九、写入数据

```javascript
write() {
		clearTimeout(timer1)
		timer1 = null
    	// str2ab 处理字符串为设备可接受的ArrayBuffer对象
		let buffer = this.str2ab('Ed')
		plus.bluetooth.writeBLECharacteristicValue({
			deviceId: this.deviceId,
			serviceId: this.serviceId,
			characteristicId: this.writeUUid, 
			writeType: 'writeNoResponse',
			value: buffer,
			success: (res) => {
				//此时设备已接收到你写入的数据
				console.log("写入成功---" + bluetoothInfo.writeUUid)
				macValue = ''
			},
			fail: (err) => {
				console.log(err)
			}
		})
	},
```

分包发送（是否使用看具体情况，方法待验证）

```javascript
/* 执行演示分包操作 */
			writeDevice(_Buffer){
				let Num = 0;
				let ByteLength = _Buffer.byteLength;
				let i = 1;
				while (ByteLength > 0) {
					i++;
					let TmpBuffer;
					if(ByteLength > 20){
						TmpBuffer = _Buffer.slice(Num, Num + 20);
						Num += 20;
						ByteLength -= 20;
						console.log('执行常规循环')
						uTool.delayed(500).then(()=>{
							uni.writeBLECharacteristicValue({
                                deviceId:self.mDeviceId,
                                serviceId:self.serviceId,
                                characteristicId:self.characteristicId,
                                value: TmpBuffer,
                                success: (res) => {
                                    /* 发送成功 */
                                    console.log('前面的循环成功', res)
                                },
                                fail: (error) => {
                                    /* 发送失败 */
                                    console.log('前面的循环失败',error)
                                } 
                             })
						})
					}else{
						console.log('执行最后一次')
						TmpBuffer = _Buffer.slice(Num, Num + ByteLength)
						Num += ByteLength
						ByteLength -= ByteLength
						/* 当长度不满20的时候执行最后一次发送即可 */
						uni.writeBLECharacteristicValue({
							deviceId:self.mDeviceId,
							serviceId:self.serviceId,
							characteristicId:self.characteristicId,
							value: TmpBuffer,
							success: (res) => {
								/* 发送成功 */
								console.log('最后一次写入成功', res)
							},
							fail: (error) => {
								/* 发送失败 */
								console.log('最后一次写入失败',error)
							}
					  })
					}
				}
			}
        }
```



## 十、方法函数

```java
//arraybuffer 转字符串 参数为ArrayBuffer对象
	ab2str(str) {
		// ArrayBuffer转16进度字符串示例
		function ab2hex(buffer) {
			const hexArr = Array.prototype.map.call(
				new Uint8Array(buffer),
				function(bit) {
					return ('00' + bit.toString(16)).slice(-2)
				}
			)
			return hexArr.join('')
		}
		//16进制
		let systemStr = ab2hex(str)
		function hexCharCodeToStr(hexCharCodeStr) {
			var trimedStr = hexCharCodeStr.trim();
			var rawStr = trimedStr.substr(0, 2).toLowerCase() === "0x" ? trimedStr.substr(2) : trimedStr;
			var len = rawStr.length;
			if (len % 2 !== 0) {
				alert("Illegal Format ASCII Code!");
				return "";
			}
			var curCharCode;
			var resultStr = [];
			for (var i = 0; i < len; i = i + 2) {
				curCharCode = parseInt(rawStr.substr(i, 2), 16); // ASCII Code Value
				let str5 = String.fromCharCode(curCharCode)
				if (str5.startsWith('\n') == false) {
					resultStr.push(String.fromCharCode(curCharCode));
				}
			}
			return resultStr.join("");
	
		}
		return hexCharCodeToStr(systemStr)
	},
	// 字符串转为ArrayBuffer对象，参数为字符串
	str2ab(str) {
		// 创建一个固定长度的二进制缓冲区  二进制ArrayBuffer对象
	    let buffer = new ArrayBuffer(str.length*2); // 在Unicode编码方案中，一个英文字符或一个汉字字符都占用两个字节的空间
		// 操作二进制ArrayBuffer对象
		let dataView = new DataView(buffer)
		for (var i=0, strLen = str.length; i<strLen; i++) {
			// 设置占8-bit的字节数组  一个字节  16进制数据
			dataView.setUint8(i, parseInt((str.charCodeAt(i)).toString(16),16) )
			// dataView.setUint8(i, '0X' + value.substring(i * 2, i * 2 + 2)); 错误写法  ：需要16进制数字类型 不能字符串
		}
	    return buffer;
	},
```

## 十一、完整代码

vue页面代码

```vue
<template>
	<view class="wrap">
		<u-cell-group>
			<u-cell-item 
			v-for="(item,index) in bluetoothList" 
			:key="index"
			:arrow="false"
			:title="item.name">
				<u-button slot="right-icon" type="warning" @click="connetBlue(item)">{{item.connect?'断开连接':'连接'}}</u-button>
			</u-cell-item>
		</u-cell-group>
        <u-button type="success" @click="autoMeasure">自动测量</u-button>
		<!-- 消息提示 -->
		<u-toast ref="uToast" />
	</view>
</template>

<script>
	import { mapActions,mapGetters } from 'vuex'
    import utils from './common/utils.js'
	var that
	export default{
		data() {
			return {
				connectBluetooth:{},
				bluetoothList: [], // 蓝牙设备列表
				deviceId: "", // 要连接蓝牙设备id
				bluetoothName:'',// 要连接蓝牙设备name
				serverList: [], // 连接蓝牙设备的服务数据
				serviceId: "", // 连接蓝牙设备的服务id
				characteristics: [], // 连接蓝牙设备的特征值数据
				notifyUUid:'',
				writeUUid:'',
			};
		},
		onLoad(){
			// 获取eventChannel事件
			const eventChannel = this.getOpenerEventChannel()
			eventChannel.on('toBluetooth', (val) => {
				this.connectBluetooth = Object.assign(this.connectBluetooth,val)
				this.getBluetoot()
			})
			that = this
		},
		computed: {
			...mapGetters(['getBluetoothInfo']),
		},
		methods:{
			...mapActions(['setBluetoothInfo']),
			// 蓝牙初始化
			getBluetoot(){
				plus.bluetooth.openBluetoothAdapter({
					success:(e) => {
						console.log('初始化成功');
						this.bluetooth_list()
					},
					fail:(e) => {
						console.log('open failed: '+JSON.stringify(e));
					}
				});
			},
			// 获取已配对蓝牙列表
			bluetooth_list() {  
			    var main = plus.android.runtimeMainActivity();  
			    var BluetoothAdapter = plus.android.importClass("android.bluetooth.BluetoothAdapter");  
			    var BAdapter = BluetoothAdapter.getDefaultAdapter();  
			    var Context = plus.android.importClass("android.content.Context");  
			    var lists = BAdapter.getBondedDevices();  
			    plus.android.importClass(lists);  
			    var len = lists.size();  
			    var iterator = lists.iterator();  
			    plus.android.importClass(iterator);  
				let bluetoothList = []
			    while (iterator.hasNext()) {  
			        var d = iterator.next();  
			        plus.android.importClass(d);
					bluetoothList.push({name:d.getName(),deviceId:d.getAddress(),connect:false})
			    }  
				bluetoothList.forEach((val) => {
					if(val.name == that.connectBluetooth.blueName && val.deviceId == that.connectBluetooth.deviceId){
						val.connect = true
					}
				})
				that.bluetoothList = bluetoothList
			},
			// 点击蓝牙连接
			connetBlue(item) {
				this.deviceId = item.deviceId
				this.bluetoothName = item.name
				if(this.connectBluetooth && this.connectBluetooth.deviceId){
					if(!item.connect){
						that.$refs.uToast.show({
							title: '请先断开连接',
							type: 'error',
							duration: 800
						})
					}else{
						this.stop(this.connectBluetooth.deviceId,item)
					}
				}else{
					createBLEConnection(item.deviceId)
				}
				function createBLEConnection(deviceId){
					uni.createBLEConnection({
						// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
						deviceId: deviceId, //设备id
						success: (res) => {
							uni.onBLEConnectionStateChange(function (res) {
							  // 该方法回调中可以用于处理连接意外断开等异常情况
							  console.log(res)
							})
							item.connect = true
							//获取蓝牙服务
							setTimeout(()=>{
								that.getBLEDeviceServices(that.deviceId);
							},1500)
						},
						fail: (res) => {
							console.log(res)
						},
					})
				}
			},
			// 获取服务列表
			getBLEDeviceServices(deviceId) {
				uni.getBLEDeviceServices({
					// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
					deviceId: deviceId,
					success: (res) => {
						if(res.services && res.services.length > 0){
							this.serverList = res.services
							this.serviceId = res.services[3].uuid
							this.getBLEDeviceCharacteristics() //6.0
						}
						
					},
					fail: function(res) {
						console.log(res)
					},
				})
			},
			// 获取特征值
			getBLEDeviceCharacteristics() {
				uni.getBLEDeviceCharacteristics({
					// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
					deviceId: this.deviceId,
					// 这里的 serviceId 需要在上面的 getBLEDeviceServices 接口中获取
					serviceId: this.serviceId,
					success: (res) => {
						console.log(res)
						if(res.characteristics && res.characteristics.length > 0){
							this.characteristics = res.characteristics
							this.notifyUUid = res.characteristics[2].uuid
							this.writeUUid = res.characteristics[3].uuid
							this.setBluetoothInfo({
								data:{
									deviceId: this.deviceId,
									bluetoothName: this.bluetoothName,
									serviceId: this.serviceId,
									characteristicId: this.characteristics,
									notifyUUid:this.notifyUUid,
									writeUUid:this.writeUUid
								},
								callback:this.backFunction
							})
						}
						
					},
					fail: function(res) {
						console.log(res)
					},
				})
			},
			// 蓝牙数据保存成功的回调
			backFunction(){
				let a = this.getBluetoothInfo
				console.log(a)
				const eventChannel = this.getOpenerEventChannel();
				eventChannel.emit('bluetoothToIndex');
				this.$refs.uToast.show({
					title: '蓝牙连接成功',
					type: 'success',
					back: true,
					duration: 800
				})
			},
            // 点击测量按钮
			autoMeasure(){
				utils.notifyBLECharacteristicValueChange(this.receiveData)
			},
			// 接收测量数据
			receiveData(res){
				console.log(res.split(','))
				let data = (res.replace(/[\r\n]/g,"")).split(',')
			},
			// 断开连接
			stop(deviceId,connectDevice){
				uni.closeBLEConnection({
				  deviceId:deviceId,
				  success(res) {
				    that.$refs.uToast.show({
				    	title: '断开连接成功',
				    	type: 'success',
				    	duration: 800
				    })
					that.setBluetoothInfo({
						data:{
							deviceId: '',
							bluetoothName: '',
							serviceId: '',
							characteristicId: '',
							notifyUUid:'',
							writeUUid:''
						},
					})
					connectDevice.connect = false
					that.connectBluetooth = {}
				  }
				})
			}
		}
	}
</script>

<style>
</style>

```

utils代码

```javascript
import store from '../store/index.js';
let macValue;
let bluetoothInfo = store.state.bluetooth;
let timer = null
let timer1 = null
export default {
	// 打开蓝牙
	openPhoneBluetooth(){
		let main, BluetoothAdapter, BAdapter;
		switch(uni.getSystemInfoSync().platform){
			case 'android':
				main  = plus.android.runtimeMainActivity();
				BluetoothAdapter = plus.android.importClass("android.bluetooth.BluetoothAdapter");
				BAdapter = BluetoothAdapter.getDefaultAdapter();
				if(!BAdapter.isEnabled()) {  
					BAdapter.enable();  
				}
				break;
			case 'ios':
				console.log('运行在ios上')
				break;
			default:
				console.log('运行在开发者工具上')
				break;
		}
	},
	// 启用 notify 功能
	notifyBLECharacteristicValueChange(callback) {
		uni.notifyBLECharacteristicValueChange({
			deviceId:bluetoothInfo.deviceId,
			serviceId:bluetoothInfo.serviceId,
			characteristicId:bluetoothInfo.notifyUUid,
			state:true,
			success: (res) => {
				console.log("广播开启成功----" + bluetoothInfo.notifyUUid)
				this.onBLECharacteristicValueChange(callback);
			},
			fail: (err) => {
				console.error(err)
				uni.showToast({
				    title: '蓝牙连接异常',
					icon: 'error',
				    duration: 2000
				});
			}
		})
	},
	// 监听低功耗蓝牙设备的特征值变化
	onBLECharacteristicValueChange(callback) {
		console.log('onBLECharacteristicValueChange')
		uni.onBLECharacteristicValueChange((res) => {
			macValue = macValue + this.ab2str(res.value);
			console.log(macValue)
			if(timer){
				clearTimeout(timer) 
				timer = null
			}else{
				timer = setTimeout(() => {
					callback(macValue)
					clearTimeout(timer)
					timer = null
				},1000)
			}
		})
		timer1 = setTimeout(() => {
			this.write()
		},1000)
		
	},
	write() {
		console.log('write')
		clearTimeout(timer1)
		timer1 = null
		let buffer = this.str2ab('Ed')
		plus.bluetooth.writeBLECharacteristicValue({
			deviceId: bluetoothInfo.deviceId,
			serviceId: bluetoothInfo.serviceId,
			characteristicId: bluetoothInfo.writeUUid, 
			writeType: 'writeNoResponse',
			value: buffer,
			success: (res) => {
				//此时设备已接收到你写入的数据
				console.log("写入成功---" + bluetoothInfo.writeUUid)
				macValue = ''
			},
			fail: (err) => {
				console.log(err)
			}
		})
	},
	//arraybuffer 转字符串 参数为ArrayBuffer对象
	ab2str(str) {
		// ArrayBuffer转16进度字符串示例
		function ab2hex(buffer) {
			const hexArr = Array.prototype.map.call(
				new Uint8Array(buffer),
				function(bit) {
					return ('00' + bit.toString(16)).slice(-2)
				}
			)
			return hexArr.join('')
		}
		//16进制
		let systemStr = ab2hex(str)
		function hexCharCodeToStr(hexCharCodeStr) {
			var trimedStr = hexCharCodeStr.trim();
			var rawStr = trimedStr.substr(0, 2).toLowerCase() === "0x" ? trimedStr.substr(2) : trimedStr;
			var len = rawStr.length;
			if (len % 2 !== 0) {
				alert("Illegal Format ASCII Code!");
				return "";
			}
			var curCharCode;
			var resultStr = [];
			for (var i = 0; i < len; i = i + 2) {
				curCharCode = parseInt(rawStr.substr(i, 2), 16); // ASCII Code Value
				let str5 = String.fromCharCode(curCharCode)
				if (str5.startsWith('\n') == false) {
					resultStr.push(String.fromCharCode(curCharCode));
				}
			}
			return resultStr.join("");
	
		}
		return hexCharCodeToStr(systemStr)
	},
	// 字符串转为ArrayBuffer对象，参数为字符串
	str2ab(str) {
		// 创建一个固定长度的二进制缓冲区  二进制ArrayBuffer对象
	    let buffer = new ArrayBuffer(str.length*2); // 在Unicode编码方案中，一个英文字符或一个汉字字符都占用两个字节的空间
		// 操作二进制ArrayBuffer对象
		let dataView = new DataView(buffer)
		for (var i=0, strLen = str.length; i<strLen; i++) {
			// 设置占8-bit的字节数组  一个字节  16进制数据
			dataView.setUint8(i, parseInt((str.charCodeAt(i)).toString(16),16) )
			// dataView.setUint8(i, '0X' + value.substring(i * 2, i * 2 + 2)); 错误写法  ：需要16进制数字类型 不能字符串
		}
	    return buffer;
	},
}
```

store代码

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
	state: {
		bluetooth:{},//连接的蓝牙设备信息
	},
	mutations: {
		// 设置蓝牙连接数据
		SET_BLUETOOTH(state,info){
			state.bluetooth = Object.assign(state.bluetooth,info)
		}
	},
	actions: {
		// 设置蓝牙信息
		setBluetoothInfo({commit,state},{data,callback}){
			commit('SET_BLUETOOTH',data)
			if(callback){callback()}
		}
	},
	getters: {
		// 获取蓝牙设备信息
		getBluetoothInfo: state => state.bluetooth
	}
})

export default store

```

## 十二、问题

整个流程重点是，写入和监听数据。写入数据一定注意格式

- 搜索不到蓝牙设备

  尝试添加权限：

`<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />`
`<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />`

- 连接蓝牙设备之后无法获取到serverUUid和蓝牙特征[uuid]

  加个定时器延迟获取serverID 就可以获取

- 蓝牙设备返回的二进制数据无法打印出来

  将返回的arraybuffer 转字符串       方法参考：https://juejin.cn/post/6846687590783909902#heading-19

  

uniapp权限模块参考地址：https://blog.csdn.net/zhanghuanhuan1/article/details/107520740