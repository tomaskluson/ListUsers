<template>
  <div class="usersList">
      <table>
        <thead>
          <tr>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Age</th>
            <th>Gender</th>
            <th>Active</th>
            <th>Email</th>
            <th>Avatar</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users">
            <td>{{ user.firstName }}</td>
            <td>{{ user.lastName }}</td>
            <td>{{ user.age }}</td>
            <td>{{ user.gender }}</td>
            <td v-bind:style="user.status === 'ACTIVE' ? greenIcon : redIcon"></td>
            <td>{{ user.email }}</td>
            <td> <img v-bind:src="user.avatar || '../../public/image/noimage.png'"></td>
          </tr>
        </tbody>
      </table>
      <div class="flex">
        <button><img src="../../public/image/left.svg" alt="">Previous</button>
        <div class="content">Current page: <span>1</span></div>
        <button>Next <img src="../../public/image/right.svg" alt=""></button>
      </div>
    <detail-user v-for="user in users" :key="user.id" :user="user" />
  </div>
</template>

<script>
import axios from "axios";
import DetailUser from '../components/DetailUser.vue'

export default {
  name: 'UsersList',
  components: {
    DetailUser
  },
  data() {
    return {
      limit: 10,
      users: [],
      greenIcon: {
        display: 'block',
        'background-color': '#29b473',
        'border-radius': '30px',
        width: '1px',
        height: '1px',
        margin: '16px',
        padding: '8px',
      },
      redIcon: {
        display: 'block',
        'background-color': 'red',
        'border-radius': '30px',
        width: '1px',
        height: '1px',
        margin: '16px',
        padding: '8px',
      },
    };
  },
  mounted: function() {
    this.getUsers();
  },
  methods: {
    getUsers() {
      const header = {
        headers: { "content-type": "application/x-www-form-urlencoded" }
      };
      axios
          .get(
              `http://localhost:3000/api/users?${this.limit}`, header
          )
          .then(response => {
            this.users = response.data.results
                .map(user => this.extractData(user));
          })
          .catch(error => console.log(error));
    },
    dateConvert(dob) {
      return dob.split("T")[0];
    },
    formatBirthDate(dob) {
      const birthday = dob.split("T")[0];
      return birthday.replaceAll('-', ".");
    },
    countAge(dob) {
      let date = new Date(this.dateConvert(dob));
      let ageDifMs = Date.now() - date.getTime();
      let ageDate = new Date(ageDifMs);
      return Math.abs(ageDate.getUTCFullYear() - 1970);
    },
    getStatus(val) {
      let status = '';
      if(val) {
        status = 'ACTIVE';
      } else {
        status = 'INACTIVE';
      }
      return status;
    },
    extractData(user) {
      let objUser = {};
      objUser = {
        id: user.id,
        firstName: user.firstName,
        lastName: user.lastName,
        age: this.countAge(user.dob),
        gender: user.gender,
        status: this.getStatus(user.active),
        email: user.email,
        avatar: user.avatar,
        birthdate: this.formatBirthDate(user.dob),
        phone: user.phone,
        address: {
          city: user.address.city,
          country: user.address.country,
          state: user.address.state,
          streetAddress: user.address.streetAddress,
          zipCode: user.address.zipCode
        }
      };
      return objUser;
    },
  }
}
</script>

<style lang="scss" scoped>
  body {
    margin-top: -30px;
  }

  table {
    border-collapse: collapse;
    border: 2px solid black;
  }

  tr, td {
    padding: 15px 15px;
  }

  tr:hover {
    background: lightskyblue;
  }

  thead {
    border: 2px solid black;
    text-align: center;
  }

  .flex{
    display: flex;
    justify-content: space-between;
    margin-top: 10px;
  }

  span {
    font-weight: bold;
  }

  button {
    border: 2px solid green;
    background: limegreen;
    height: 30px;
    width: 90px;
  }

  .content {
    padding-top: 10px;
  }

  .usersList {
    height: 100%;
    width: 50%;
  }


</style>
