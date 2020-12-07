<template>
    <div class="flex">
      <button @click="prevPage"><img src="../../public/image/left.svg" alt="">Previous</button>
      <div class="content">Current page: <span> {{ this.currentPage }} </span></div>
      <button @click="nextPage" >Next <img src="../../public/image/right.svg" alt=""></button>
    </div>
</template>

<script>

export default {
  name: 'Pagination',

  data() {
    return {
      pageNumber: 1,
    }
  },

  props: {
    currentPage: {
      type: Number,
      default: 1
    },
    total: {
      type: Number,
    },
    onPageClick: {
      type: Function,
    },
    users: {
      type: Array,
    },
    size: {
      type: Number,
    }
  },

  computed: {
    pageItem() {
      const count = Math.ceil(this.total / 10);
      let items = [];
      for (let i = 1; i <= count; i++) {
        items.push({
          label: i,
          id: i,
        });
      }
      return items;
    },

    pageCount(){
      let l = this.users.length,
          s = this.size;
      return Math.ceil(l/s);
    },

    paginatedData(){
      const start = this.pageNumber * this.size,
          end = start + this.size;
      return this.users.slice(start, end);
    }

  },

  methods: {
    updateSelectedUser(userId) {
      this.selectedUser = this.users.find(el => {
        if(el.id === userId){
          return this.selectedUser = el;
        }
      })
    },
    onPageClickInner(p) {
      this.$emit("on-page-click", p);
    },
    nextPage(){
      this.currentPage++;
    },
    prevPage(){
      this.currentPage--;
    }
  }
}
</script>

<style lang="scss" scoped>

  .flex{
    display: flex;
    justify-content: space-between;
    margin-top: 10px;
  }

  .content {
    padding-top: 10px;
  }

  button {
    border: 2px solid green;
    background: limegreen;
    height: 30px;
    width: 90px;
  }

</style>
