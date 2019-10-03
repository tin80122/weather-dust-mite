<script>
export default {
		name: 'pagination',
		props: {
			filteredRowsLength: Number,
			OnePageRow: Number
		},
    data () {
      return {
        currPage: 1,
        pageStart: 1
      }
    },
    methods: {
        setPage (newPage) {
						this.currPage = newPage;
						this.$emit('update-currPage', newPage);
            console.log("TCL: setPage -> currPage", this.currPage);
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
					return Math.ceil(this.filteredRowsLength / this.OnePageRow);
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
            :class="{'active': (currPage === (n))}"
        >
            <a href="#"
                class="page-link"
                role="button"
                @click.prevent="setPage(n)">
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