<template>
  <div id="flex">
    <!-- <img v-if="!imgError" :src="`${selectedUser.avatar}`" @click="$emit('onImgError', imgError)" @error="onImgError()">
    <img v-else :src="`../image/noimage.png`"> -->
    <div class="container">
      <img v-if="imgError" :src="`${selectedUser.avatar}`" @error="onImgError()">
      <img v-else :src="`../image/noimage.png`">
    </div>
    <div class="container">
      <div class="box">
        <p class="textBold">First Name:</p>
        <p>{{ selectedUser.firstName }}</p>
      </div>
      <div class="box2">
        <p class="textBold">Last Name:</p>
        <p>{{ selectedUser.lastName }}</p>
      </div>
      <div class="box">
        <p class="textBold">Gender:</p>
        <p>{{ selectedUser.gender }}</p>
      </div>
    </div>
    <div class="container">
      <div class="box">
        <p class="textBold">Age:</p>
        <p>{{ selectedUser.age }}</p>
      </div>
      <div class="box2">
        <p class="textBold">Status</p>
        <p>{{ selectedUser.status }}</p>
      </div>
      <div class="box">
        <p class="textBold">Birth date:</p>
        <p>{{ selectedUser.birthdate }}</p>
      </div>
    </div>
    <div class="container">
      <div class="box">
        <p class="textBold">Address:</p>
        <p>
          {{ selectedUser.address.country }} <br>
          {{ selectedUser.address.city }} <br>
          {{ selectedUser.address.state }}
          {{ selectedUser.address.zipCode }} <br>
          {{ selectedUser.address.streetAddress }} <br>
        </p>
      </div>
      <div class="box2">
        <p class="textBold">Phone:</p>
        <p>{{ selectedUser.phone }}</p>
      </div>
      <div class="box">
        <p class="textBold">Email:</p>
        <p>{{ selectedUser.email }}</p>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  props: {
    selectedUser: {
      type: Object
    },
    imgError: {
      type: Boolean
    },
    onImgError: {
      type: Function
    }
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

  border: 2px solid black;
  margin-left: 10px;
}

img {
  width: 80px;
  border-radius: 50%;
  background: beige;
  margin: 10px;
}

.container {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  text-align: left;
  align-content: space-between;
}

.box {
  width: 200px;
}

.box2 {
  width: 100px;
  margin-left: -70px;
}

.textBold {
  font-weight: bold;
  margin-bottom: -15px;
}

</style>
