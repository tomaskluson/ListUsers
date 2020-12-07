<template>
  <div class="usersList">
      <table v-if="renderData.length > 0">
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
          <tr v-for="item in renderData" :key="item.id" @click.prevent="updateSelectedUser(item.id)">
            <td>{{ item.firstName }}</td>
            <td>{{ item.lastName }}</td>
            <td>{{ item.age }}</td>
            <td>{{ item.gender }}</td>
            <td v-bind:style="item.status === 'ACTIVE' ? greenIcon : redIcon"></td>
            <td>{{ item.email }}</td>
            <td> <img v-bind:src="item.avatar || '../../public/image/noimage.png'"></td>
          </tr>
        </tbody>
      </table>
      <pagination
          :total="this.users.length"
          :currentPage="currentPage"
          @on-page-click="onPageClick"
      />
    <!-- <h3 v-if="selectedUser != null">Selected user is: {{selectedUser}}</h3> -->
    <p>POČET STRAN: {{ this.pagesCount }}</p>
    <detail-user v-bind:selectedUser="selectedUser" />
  </div>
</template>

<script>
import axios from "axios";
import DetailUser from '../components/DetailUser.vue'
import Pagination from '../components/Pagination'

export default {
  name: 'UsersList',
  components: {
    DetailUser,
    Pagination
  },
  data() {
    return {
      users: [],
      selectedUser: {},
      pagesCount: '',
      currentPage: 1,
      size: 10,
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
  created: function() {
    this.getPagesCount();
  },
  mounted: function() {
    this.getUsers();
  },
  computed: {
    renderData() {
      const start = (this.currentPage - 1) * 10;
      const end = start + 10;
      return this.users.slice(start, end);
    },
  },
  methods: {
    getPagesCount() {
      return axios.get('http://localhost:3000/api/users')
          .then(response => this.pagesCount = response.data.meta.pagesCount);
    },
    async getUsers() {
      const countPage = await this.getPagesCount();
      console.log(countPage);
      for (let i = 1; i <= countPage; i++) {
        await axios.get(`http://localhost:3000/api/users?page=${i}`)
            .then(response => {
              response.data.results.forEach(user => this.users.push(user));
        })
      }
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
    updateSelectedUser(userId) {
      this.selectedUser = this.users.find(el => {
        if(el.id === userId){
          return this.selectedUser = el;
        }
      })
    },
    onPageClick(p) {
      this.currentPage = p;
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
