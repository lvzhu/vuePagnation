<template>
	<div class="group-info">
			
      <!-- 分页 -->
			<MoPaging class="myadd-pag" :jumpShow="isShowJump" :pageIndex="currentPage" :total="count" :pageSize="pageSize" @change="pageChange"></MoPaging>

	</div>
</template> 

<script>
import MoPaging from './pageNation'
export default {
	components: {
		MoPaging
	},
	data() {
		return {
	
			//分页组件数据
			pageSize: 10, //每页显示10条数据
			currentPage: 1, //当前页码
			count: 0, //总记录数
			isShowJump:true,  //是否显示跳页
		}
	},
	methods: {
		//从page组件传递过来的当前page
		pageChange(page) {
			this.currentPage = page;
			// this.getList();  //查询数据
		}
	}
}

</script>

<style type="text/css">

</style>
