<template>
    <div class="flex">
      <button :disabled="currentPage === 1" @click="prevPage()"><img src="../../public/image/left.svg" alt="">Previous</button>
      <div class="content">Current page: <span> {{ this.currentPage }} </span></div>
      <button :disabled="currentPage > countActualPage() - 1" @click="nextPage()">Next <img src="../../public/image/right.svg" alt=""></button>
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

  props: {
    pageCount: {
      type: Number,
    },
    actualPage: {
      type: Number,
    },
    usersCount: {
      type: Number,
    },
    size: {
      type: Number,
    },
  },

  methods: {
    nextPage(){
      this.$emit("current-count-page", ++this.currentPage);
    },
    prevPage(){
      this.$emit("current-count-page", --this.currentPage);
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
