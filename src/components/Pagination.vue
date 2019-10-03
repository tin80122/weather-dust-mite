<script>
export default {
		name: 'pagination',
		props: {
			filteredRowsLength: Number,
			OnePageRow: Number,
			initialCurrPage: Number,
			pageStart: Number
		},
    data () {
      return {
				currPage: this.initialCurrPage
      }
    },
    methods: {
			setPage (newPage) {
					this.currPage = newPage;
					this.$emit('update:update-currPage', newPage);
			}
    },
    computed: {
			disabledPrevBtn: function () {
					return this.currPage === this.pageStart ? 'disabled' : '';
			},
			disabledNextBtn: function () {
					return this.currPage === this.totalPage ? 'disabled' : '';
			},
			totalPage: function () {
				return Math.ceil(this.filteredRowsLength	/	this.OnePageRow);
			}
		},
		watch: {
			totalPage: function () {
      	console.log("child TCL: totalPage", this.totalPage);
      	console.log("child TCL: currPage", this.currPage);
      	console.log("child TCL: pageStart", this.pageStart);
			}
		}
}
</script>
<template>
    <ul v-show="filteredRowsLength !== 0" class="pagination">
        <li class="page-item" :class="disabledPrevBtn">
            <a href="#"
                class="page-link"
                role="button"
                @click.prevent="setPage(currPage-1)">
                <span aria-hidden="true">&laquo;</span>
                <span class="sr-only">Previous</span>
            </a>
        </li>
        <li class="page-item"
            v-for="n in totalPage"
            :key="'p'+n"
            :class="{'active': (currPage === (n-1))}"
        >
            <a href="#"
                class="page-link"
                role="button"
                @click.prevent="setPage(n-1)">
                {{n}}
            </a>
        </li>
        <li class="page-item" :class="disabledNextBtn">
            <a href="#"
                class="page-link"
                role="button"
                @click.prevent="setPage(currPage+1)">
                <span aria-hidden="true">&raquo;</span>
                <span class="sr-only">Next</span>
            </a>
        </li>
    </ul>
</template>