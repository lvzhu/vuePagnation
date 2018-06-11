<template>
    <div>
        <div class="pag-msg">
            共{{total}}条数据  每页显示{{pageSize}}条
        </div>
        <ul class="mo-paging">
            <!-- 首页 -->
            <li :class="['paging-item', 'paging-item--first', {'paging-item--disabled' : index === 1}]" @click="first">首页</li>
            <!-- 上一页 -->
            <li :class="['paging-item', 'paging-item--prev', {'paging-item--disabled' : index === 1}]" @click="prev">上一页</li>

            <li :class="['paging-item', 'paging-item--more']" v-if="showPrevMore">...</li>

            <li :class="['paging-item', {'paging-item--current' : index === pager}]" v-for="(pager,index1) in pagers" @click="go(pager)" :key="index1">{{ pager }}</li>

            <li :class="['paging-item', 'paging-item--more']" v-if="showNextMore">...</li>

            <!-- 下一页 -->
            <li :class="['paging-item', 'paging-item--next', {'paging-item--disabled' : index === pages}]" @click="next">下一页</li>
            <!-- 尾页 -->
            <li :class="['paging-item', 'paging-item--last', {'paging-item--disabled' : index === pages}]" @click="last">尾页</li>
        </ul>
        <div class="pag-jump" v-if="jumpShow">
            跳转
            <input class="pag-jump--input" type="text" v-model="jumpPag" @blur="go(parseInt(jumpPag))" @keyup.enter="go(parseInt(jumpPag))" /> 页
        </div>
    </div>
</template>

<script type="text/javascript">
export default {
    name: 'MoPaging',
    //通过props来接受从父组件传递过来的值
    props: {
        //页面中的可见页码，其他的以...替代, 必须是奇数
        perPages: {
            type: Number,
            default: 5
        },

        //当前页码
        pageIndex: {
            type: Number,
            default: 1
        },

        //每页显示条数
        pageSize: {
            type: Number,
            default: 10
        },

        //总记录数
        total: {
            type: Number,
            default: 1
        },
        //是否显示跳页
        jumpShow: {
             type: Boolean,
            default: true
        }
        

    },
    data() {
        return {
            jumpPag: '',  //跳页数据
            index: this.pageIndex, //当前页码
            limit: this.pageSize, //每页显示条数
            size: this.total || 1, //总记录数
            showPrevMore: false,
            showNextMore: false
        }
    },
    methods: {
        // 上一页
        prev() {
            if (this.index > 1) {
                this.go(this.index - 1)
            }
        },
        // 下一页
        next() {
            if (this.index < this.pages) {
                this.go(this.index + 1)
            }
        },
        // 首页
        first() {
            if (this.index !== 1) {
                this.go(1)
            }
        },
        // 尾页
        last() {
            if (this.index != this.pages) {
                this.go(this.pages)
            }
        },
        //跳转到指定页数
        go(page) {
            // 校验跳页输入的值是否为(正整数)合理值
            var match = /^\+?[1-9][0-9]*$/;
            if (match.test(page)) {
                page = page > this.pagers.length ? this.pagers.length:page;  //输入的页数大于总页数 默认跳转到最后一页
                if (this.index !== page) {
                    this.index = page
                    //父组件通过change方法来接受当前的页码
                    this.$emit('change', this.index)
                }
            } else {
                this.jumpPag = '';
            }
        }
    },
    computed: {
        //计算总页码
        pages() {
            return Math.ceil(this.size / this.limit)
        },
        //计算页码，当count等变化时自动计算
        pagers() {
            const array = []
            const perPages = this.perPages
            const pageCount = this.pages
            let current = this.index
            const _offset = (perPages - 1) / 2
            const offset = {
                start: current - _offset,
                end: current + _offset
            }
            //-1, 3
            if (offset.start < 1) {
                offset.end = offset.end + (1 - offset.start)
                offset.start = 1
            }
            if (offset.end > pageCount) {
                offset.start = offset.start - (offset.end - pageCount)
                offset.end = pageCount
            }
            if (offset.start < 1) offset.start = 1

            this.showPrevMore = (offset.start > 1)
            this.showNextMore = (offset.end < pageCount)

            for (let i = offset.start; i <= offset.end; i++) {
                array.push(i)
            }
            console.log(array);
            return array
        }
    },
    watch: {
        pageIndex(val) {
            this.index = val || 1
        },
        pageSize(val) {
            this.limit = val || 10
        },
        total(val) {
            this.size = val || 1
        }
    }
}
</script>

<style scoped>
.mo-paging {
    display: inline-block;
    padding: 0;
    margin: 30px 0;
    font-size: 0;
    list-style: none;
    user-select: none;
}

.paging-item {
    display: inline;
    margin-left: 5px;
    font-size: 14px;
    position: relative;
    padding: 6px 12px;
    line-height: 1.42857143;
    text-decoration: none;
    border: 1px solid #4ea8f8;
    border-radius: 3px;
    cursor: pointer;
    color: #fff;
}

.paging-item :first-child {
    margin-left: 0;
}

.paging-item:hover {
    background-color: #49a4e8;
    color: #fff;
}

.paging-item.paging-item--disabled,
.paging-item.paging-item--more {
    background-color: #fff;
    color: #505050;
}



/* 禁用 */

.paging-item.paging-item--disabled {
    cursor: not-allowed;
    opacity: .75;
}

.paging-item.paging-item--more,
.paging-item.paging-item--current {
    cursor: default;
}



/* 选中 */

.paging-item.paging-item--current {
    background-color: #49a4e8;
    color: #fff;
    position: relative;
    z-index: 1;
    border-color: #0275d8;
}



/* 跳转样式S */
.pag-jump {
    margin-left: 10px;
    display: inline-block;
    color: #ddd;
}

.pag-jump--input {
    width: 40px;
    font-size: 14px;
    padding: 6px 4px;
    line-height: 1.42857143;
    color: #fff;
    border: 1px solid #4ea8f8;
    border-radius: 3px;
    background: none;
    text-align: center;
}
/* 跳转样式E */
/* 数据信息 */
.pag-msg {
    margin-right: 10px;
    display: inline-block;
    color: #ddd;
}
</style>
