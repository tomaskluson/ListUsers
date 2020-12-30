<template>
  <div class="flex">
    <button :disabled="currentPage === 1" @click="prevPage()"><img src="../../public/image/left.svg" alt="" >Previous</button>
    <div class="content">Current page: <span> {{ this.currentPage }} </span></div>
    <button :disabled="currentPage > countActualPage() - 1" @click="nextPage()">Next<img src="../../public/image/right.svg" alt="" ></button>
  </div>
</template>

<script>

export default {
  name: 'Pagination',

  data() {
    return {
      currentPage: 1,
    }
  },

  mounted() {
    this.$root.$on("new-current-page", data => (this.currentPage = data));
  },

  props: {
    usersCount: {
      type: Number
    },
    size: {
      type: Number
    },
    sortDataTable: {
      type: Function
    },
  },

  methods: {
    prevPage(){
      this.$emit("current-count-page", --this.currentPage);
    },
    nextPage(){
      this.$emit("current-count-page", ++this.currentPage);
      this.sortDataTable();
    },
    countActualPage() {
      return Math.ceil(this.usersCount / this.size);
    },
  }
}
</script>

<style lang="scss" scoped>

  .flex{
    display: flex;
    justify-content: space-between;
    margin-top: 10px;
  }

  .arrows {
    height: 28px;
    width: 17px;
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

  span {
    font-weight: bold;
  }

</style>
