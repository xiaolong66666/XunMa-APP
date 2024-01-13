<template>
  <view class="content">
    <!-- <image class="logo" src="@/static/xm.png"></image> -->
    <view style="width: 100%;">
		<view class="box-bg">
			<uni-nav-bar fixed="true">
				<block slot="left">
				</block>
				<view class="input-view">
					<uni-search-bar v-model="queryParams.noticeTitle" class="uni-mt-10" radius="5" placeholder="搜索标题" clearButton="auto" cancelButton="none" @confirm="getList()" />
				</view>
				<block slot="right">
					<view @click="getList()" class="city">
						搜索
					</view>
				</block>
			</uni-nav-bar>
		</view>
		<uni-card v-for="notice in noticeList" :title="notice.noticeTitle" :sub-title="notice.createTime" :extra="notice.noticeType === '1'?'通知':'公告'" padding="10px 0" thumbnail="../static/images/profile.jpg" >
		<rich-text :nodes="formatContent(notice.noticeContent)"></rich-text>
	    </uni-card>
		<uni-fab ref="fab" :pattern="pattern" :content="content" :horizontal="horizontal" :vertical="vertical" :direction="direction" @trigger="trigger" @fabClick="fabClick" />
    </view>
  </view>
</template>

<script>
  import { listNotice } from '@/api/system/notice';
  
  export default {
	  props: {
	  	scrollTop: {
	  		type: Number,
	  		default: 0
	  	},
	  },
	  data () {
	        return {
				//悬浮窗参数
				title: 'uni-fab',
				directionStr: '垂直',
				horizontal: 'right',
				vertical: 'bottom',
				direction: 'horizontal',
				pattern: {
					color: '#7A7E83',
					backgroundColor: '#fff',
					selectedColor: '#007AFF',
					buttonColor: '#007AFF',
					iconColor: '#fff'
				},
				is_color_type: false,
				content: [{
						iconPath: '/static/images/common/top.png',
						selectedIconPath: '/static/images/common/top.png',
						text: '回到顶部',
						active: false
					},
					{
						iconPath: '/static/images/common/message.png',
						selectedIconPath: '/static/images/common/message.png',
						text: '留言',
						active: false
					}
				],
			  // 查询参数
			  queryParams: {
			    pageNum: 1,
			    pageSize: 5,
			   },
			   noticeList:[],
			   range: [
			        { value: 1, text: "通知" },
			        { value: 2, text: "公告" },
			   ],
	        }
	      },
	onBackPress() {
				if (this.$refs.fab.isShow) {
					this.$refs.fab.close()
					return true
				}
				return false
	},
    onLoad: function() {
		// listNotice(this.queryParams);
		this.getList();
		
    },
	methods: {
		//返回top
		goTop(){
			uni.pageScrollTo({
				scrollTop: this.scrollTop,
				duration: 300
			});
		},
		formatContent(html) { // 去掉img标签里的style、width、height属性
			let content_data = html.replace(/<img[^>]*>/gi, function(match, capture) {
				match = match.replace(/style="[^"]+"/gi, '').replace(/style='[^']+'/gi, '');
				match = match.replace(/width="[^"]+"/gi, '').replace(/width='[^']+'/gi, '');
				match = match.replace(/height="[^"]+"/gi, '').replace(/height='[^']+'/gi, '');
				return match;
			}); // 修改所有style里的width属性为max-width:100%
			content_data = content_data.replace(/style="[^"]+"/gi, function(match, capture) {
				match = match.replace(/width:[^;]+;/gi, 'max-width:100%;').replace(/width:[^;]+;/gi, 'max-width:100%;');
				return match;
			}); // 去掉<br/>标签
			content_data = content_data.replace(/<br[^>]*\/>/gi, ''); // img标签添加style属性：max-width:100%;height:auto
			content_data = content_data.replace(/\<img/gi,
				'<img style="max-width:100%;height:auto;display:block;margin:0px auto;"');
			return content_data;
		},
		
		getList() {
			this.queryParams.pageNum=1;
	        listNotice(this.queryParams).then(response => {
			  this.noticeList = response.rows;
			  console.log(response.rows)
			  // uni.stopPullDownRefresh();
	        });
			uni.stopPullDownRefresh();
	    },
		  
	  startPull () {
	    uni.startPullDownRefresh()
	  },
	  onPullDownRefresh () {
		  this.getList()
	  },
	  onReachBottom () {
	    console.log('触底了')
		//分页加载数据并追加到原有数据列表
		this.queryParams.pageNum += 1;
		listNotice(this.queryParams).then(response => {
			if (this.noticeList.length < response.total){
				this.noticeList = this.noticeList.concat(response.rows);
			}
		});
	  },
	  //悬浮窗参数
      trigger(e) {
	  	console.log(e)
	  	this.content[e.index].active = !e.item.active
		this.goTop()
	  	// uni.showModal({
	  	// 	title: '提示',
	  	// 	content: `您${this.content[e.index].active ? '选中了' : '取消了'}${e.item.text}`,
	  	// 	success: function(res) {
	  	// 		if (res.confirm) {
	  	// 			console.log('用户点击确定')
	  	// 		} else if (res.cancel) {
	  	// 			console.log('用户点击取消')
	  	// 		}
	  	// 	}
	  	// })
	  },
	  //用户点击悬浮球
	  fabClick() {
	  	// uni.showToast({
	  	// 	title: '点击了悬浮按钮',
	  	// 	icon: 'none'
	  	// })
	  },
	  switchBtn(hor, ver) {
	  	if (hor === 0) {
	  		this.direction = this.direction === 'horizontal' ? 'vertical' : 'horizontal'
	  		this.directionStr = this.direction === 'horizontal' ? '垂直' : '水平'
	  	} else {
	  		this.horizontal = hor
	  		this.vertical = ver
	  	}
	  	this.$forceUpdate()
	  },
	  switchColor() {
	  	this.is_color_type = !this.is_color_type
	  	if (this.is_color_type) {
	  		this.pattern.iconColor = '#aaa'
	  		this.pattern.buttonColor = '#fff'
	  	} else {
	  		this.pattern.iconColor = '#fff'
	  		this.pattern.buttonColor = '#007AFF'
	  	}
	  }
	}
  }
</script>

<style>
   .warp {
   		padding: 10px;
   	}
   
   	.button {
   		margin-bottom: 10px;
   	}
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
  }

  .title {
    font-size: 36rpx;
    color: #8f8f94;
  }
</style>