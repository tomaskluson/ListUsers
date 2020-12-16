<template>

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

  }
}
</script>

<style lang="scss" scoped>



</style>
