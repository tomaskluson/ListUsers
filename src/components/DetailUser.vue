<template>
  <div id="flex">
    <img src="" alt="" class="imgCenter">
    <div>
      <p>First Name:</p>
      <p>{{ selectedUser.firstName }}</p>
      <p>Last Name:</p>
      <p>{{ selectedUser.lastName }}</p>
      <p>Gender:</p>
      <p>{{ selectedUser.gender }}</p>
    </div>
    <div>
      <p>Age:</p>
      <p>{{ selectedUser.age }}</p>
      <p>Status</p>
      <p>{{ selectedUser.status }}</p>
      <p>Birth date:</p>
      <p>{{ selectedUser.birthdate }}</p>
    </div>
    <div>
      <p>Address:</p>
      <p>
        {{ selectedUser.address.state }} <br>
        {{ selectedUser.address.country }} <br>
        {{ selectedUser.address.streetAddress }} <br>
        {{ selectedUser.address.city }}
        {{ selectedUser.address.zipCode }}
      </p>
      <p>Phone:</p>
      <p>{{ selectedUser.phone }}</p>
      <p>Email:</p>
      <p>{{ selectedUser.email }}</p>
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
