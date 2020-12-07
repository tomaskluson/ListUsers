<template>
  <div id="flex">
    <img src="" alt="" class="imgCenter">
    <div>
      <p>First Name:</p>
      <p v-if="selectedUser != null">{{ selectedUser.firstName }}</p>
      <p>Last Name:</p>
      <p v-if="selectedUser != null">{{ selectedUser.lastName }}</p>
      <p>Gender:</p>
      <p v-if="selectedUser != null">{{ selectedUser.gender }}</p>
    </div>
    <div>
      <p>Age:</p>
      <p v-if="selectedUser != null">{{ selectedUser.age }}</p>
      <p>Status</p>
      <p v-if="selectedUser != null">{{ selectedUser.status }}</p>
      <p>Birth date:</p>
      <p v-if="selectedUser != null">{{ selectedUser.birthdate }}</p>
    </div>
    <div>
      <p>Address:</p>
      <p v-if="selectedUser != null">
        {{ selectedUser.address.state }} <br>
        {{ selectedUser.address.country }} <br>
        {{ selectedUser.address.streetAddress }} <br>
        {{ selectedUser.address.city }}
        {{ selectedUser.address.zipCode }}
      </p>
      <p>Phone:</p>
      <p v-if="selectedUser != null">{{ selectedUser.phone }}</p>
      <p>Email:</p>
      <p v-if="selectedUser != null">{{ selectedUser.email }}</p>
    </div>
  </div>
</template>

<script>


export default {
  props: {
    selectedUser: {
      type: Object
    },
  },

  name: 'DetailUser',
  data() {
    return {
    };
  },


}
</script>

<style lang="scss" scoped>
  #flex {
    display: flex;
    justify-content: space-around;
    margin-top: 10px;
    border: 2px solid black;
  }

  .imgCenter {
    align-items: center;
  }

</style>
