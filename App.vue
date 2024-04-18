<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
		uni-page-head { 
			/* display: none; */
			  position: absolute;
			  top: 0;
			  left: 0;
			  width: 100%;
			  z-index: 100; /* 确保头部在其他内容上方 */
		}
</style>
