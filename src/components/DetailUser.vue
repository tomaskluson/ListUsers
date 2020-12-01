<template>
  <div id="flex">
    <img src="" alt="" class="imgCenter">
    <div>
      <p>First Name:</p>
      <p>{{ user.firstName }}</p>
      <p>Last Name:</p>
      <p>{{ user.lastName }}</p>
      <p>Gender:</p>
      <p>{{ user.gender }}</p>
    </div>
    <div>
      <p>Age:</p>
      <p>{{user.age }}</p>
      <p>Status</p>
      <p>{{user.status }}</p>
      <p>Birth date:</p>
      <p>{{ user.birthdate }}</p>
    </div>
    <div>
      <p>Address:</p>
      <p>
        {{user.address.state }} <br>
        {{user.address.country }} <br>
        {{user.address.streetAddress }} <br>
        {{user.address.city }}
        {{user.address.zipCode }}
      </p>
      <p>Phone:</p>
      <p>{{user.phone}}</p>
      <p>Email:</p>
      <p>{{user.email}}</p>
    </div>
  </div>
</template>

<script>


export default {
  props: {
    user: {
      type: Object
    }
  },

  name: 'DetailUser',
  data() {
    return {

    };
  },

  methods: {

  }
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
