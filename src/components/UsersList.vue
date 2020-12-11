<template>
  <div class="usersList">
    <table>
        <thead>
          <tr>
            <th>First Name</th>
            <th @click="sortCol('lastName', sortOrder)">Last Name <i class="fas" :class="cssSort('lastName')"></i></th>
            <th @click="sortCol('age', sortOrder)">Age <i class="fas" :class="cssSort('age')"></i></th>
            <th @click="sortCol('gender', sortOrder)">Gender <i class="fas" :class="cssSort('gender')"></i></th>
            <th @click="sortCol('active', sortOrder)">Active <i class="fas" :class="cssSort('active')"></i></th>
            <th>Email</th>
            <th>Avatar</th>
          </tr>
        </thead>
        <tbody>
        <!-- místo renderData by mělo být pole (this.users) -->
          <tr v-for="item in this.users.slice(pageBegin(), pageEnd())" :key="item.id" @click.prevent="updateSelectedUser(item.id)">
            <td>{{ item.firstName }}</td>
            <td>{{ item.lastName }}</td>
            <td>{{ item.age }}</td>
            <td>{{ item.gender }}</td>
            <td :style="item.status === 'ACTIVE' ? greenIcon : redIcon "></td>
            <td>{{ item.email }}</td>
            <td><img v-if="item.avatar" :src="item.avatar"></td>
          </tr>
        </tbody>
      </table>
       <pagination @current-count-page="currentPage = $event" :usersCount="usersCount" :size="size"/>
    <detail-user v-bind:selectedUser="selectedUser" :sortDataTable="sortDataTable"/>
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
      usersCount: 0,
      pagesCount: 0,
      size: 10,
      currentPage: 1,
      sortOrder: "asc",
      sortColumn: '',
      countPage: 1,
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

  mounted: function(){
    this.getFirstUsers();
    this.getNextUsers();
    this.sortCol();
  },
  methods: {
    async getFirstUsers() {
      await axios.get(`http://localhost:3000/api/users`)
        .then(response => {
            response.data.results.forEach(user => this.users.push(this.extractData(user)));
            this.usersCount = response.data.meta.usersCount;
        })
    },
    async getNextUsers() {
      this.pagesCount = await this.getPagesCount();
      for (let i = 2; i <= this.pagesCount; i++) {
        await axios.get(`http://localhost:3000/api/users?page=${i}`)
            .then(response => {
              response.data.results.forEach(user => this.users.push(this.extractData(user)));
            })
      }
    },
    getPagesCount() {
      return axios.get('http://localhost:3000/api/users')
          .then(response => this.pagesCount = response.data.meta.pagesCount);
    },
    /*sortCol(column, order) {
      this.sortColumn = column;
      this.sortOrder = order === "asc" ? "desc" : "asc";

      let usersSort = this.users.slice(0, this.pageEnd());
      console.log(usersSort);
      usersSort = usersSort.sort(this.compareValues(this.sortColumn, this.sortOrder));
      let usersNoSort = this.users.slice(this.pageEnd(), this.users.length - 1);
      this.users = usersSort.concat(usersNoSort);
      return this.users;
      //console.log(this.currentPage);
      /*this.userSort = this.users.slice(0, this.pageEnd());
      this.userSort = this.userSort.sort(this.compareValues(this.sortColumn, this.sortOrder))
      return this.userSort;*/

      // reset current page
      //this.currentPage = 1;
    //},
    sortCol(column, order) {
      this.sortColumn = column;
      this.sortOrder = order === "asc" ? "desc" : "asc";
      this.sortDataTable();
    },
    sortDataTable() {
      let usersSort = this.users.slice(0, this.pageEnd());
      console.log(usersSort);
      usersSort = usersSort.sort(this.compareValues(this.sortColumn, this.sortOrder));
      let usersNoSort = this.users.slice(this.pageEnd(), this.users.length - 1);
      this.users = usersSort.concat(usersNoSort);
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
    extractData({ id, firstName, lastName, active: status, gender, email, dob: age, phone, avatar, address }) {
      let birthdate = this.formatBirthDate(age);
      status = this.getStatus(status);
      age = this.countAge(age);
      return { id, firstName, lastName, status, gender, email, age, phone, avatar, birthdate, address }
    },
    updateSelectedUser(userId) {
      this.selectedUser = this.users.find(el => {
        if(el.id === userId){
          return this.selectedUser = el;
        }
      })
    },

    compareValues(key, order = 'asc') {
      return function innerSort(a, b) {
        if (!a.hasOwnProperty(key) || !b.hasOwnProperty(key)) {
          return 0;
        }

        const varA = (typeof a[key] === 'string')
            ? a[key].toUpperCase() : a[key];
        const varB = (typeof b[key] === 'string')
            ? b[key].toUpperCase() : b[key];

        let comparison = 0;
        if (varA > varB) {
          comparison = 1;
        } else if (varA < varB) {
          comparison = -1;
        }
        return (
            (order === 'desc') ? (comparison * -1) : comparison
        );
      };
    },
    isColumn(column) {
      return this.sortColumn === column;
    },
    isOrder(order) {
      return this.sortOrder === order;
    },
    cssSort(column) {
      return {
        "fa-sort": !this.isColumn(column),
        "fa-sort-up": this.isColumn(column) && this.isOrder("asc"),
        "fa-sort-down": this.isColumn(column) && this.isOrder("desc"),
      };
    },
    pageEnd() {
      return this.currentPage * this.size;
    },
    pageBegin() {
      return this.pageEnd() - this.size;
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
