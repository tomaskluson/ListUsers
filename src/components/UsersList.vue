<template>
  <div class="usersList">
    <table>
        <thead>
          <tr>
            <th>First Name</th>
            <th @click="sortCol('lastName', sortOrder)">Last Name <i class="fas" :class="cssSort('lastName')"></i></th>
            <th @click="sortCol('age', sortOrder)">Age <i class="fas" :class="cssSort('age')"></i></th>
            <th @click="sortCol('gender', sortOrder)">Gender <i class="fas" :class="cssSort('gender')"></i></th>
            <th @click="sortCol('status', sortOrder)">Active <i class="fas" :class="cssSort('status')"></i></th>
            <th>Email</th>
            <th>Avatar</th>
          </tr>
        </thead>
        <tbody>
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
      <div class="flex">
        <button :disabled="currentPage === 1" @click="prevPage()"><img src="../../public/image/left.svg" alt="" class="arrows">Previous</button>
        <div class="content">Current page: <span> {{ this.currentPage }} </span></div>
        <button :disabled="currentPage > countActualPage() - 1" @click="nextPage()">Next <img src="../../public/image/right.svg" alt="" class="arrows"></button>
      </div>
    <detail-user v-bind:selectedUser="selectedUser"/>
  </div>
</template>

<script>
//import axios from "axios";
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
      previousSortColumn: '',
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

  mounted: function() {
    this.getFirstUsers();
    this.getNextUsers();
    this.sortCol();
    //this.methodForGetPromise('https://s3.amazonaws.com/uifaces/faces/twitter/giancarlon/128.jpg');
  },
  methods: {

    async getFirstUsers() {
      //async getFirstUsers() {
      /*await axios.get(`http://localhost:3000/api/users`)
        .then(response => {
            response.data.results.forEach(user => this.users.push(this.extractData(user)));
            this.usersCount = response.data.meta.usersCount;
        })*/
      return fetch('http://localhost:3000/api/users')
          .then(response => response.json())
          .then(data => data.results.forEach(
              user => this.users.push(this.extractData(user))));
    },
    async getNextUsers() {
      this.pagesCount = await this.getPagesCount();
      this.usersCount = await this.getUsersCount();

      for (let i = 2; i <= this.pagesCount; i++) {
        fetch(`http://localhost:3000/api/users?page=${i}`)
          .then(response => response.json())
          .then(data => data.results.forEach(
              user => this.users.push(this.extractData(user))));
      }
      /*for (let i = 2; i <= this.pagesCount; i++) {
        await axios.get(`http://localhost:3000/api/users?page=${i}`)
            .then(response => {
              response.data.results.forEach(user => this.users.push(this.extractData(user)));
            })
      }*/
    },
    async getPagesCount() {
      return fetch('http://localhost:3000/api/users')
          .then(response => response.json())
          .then(data => this.pagesCount = data.meta.pagesCount)
      /*return axios.get('http://localhost:3000/api/users')
          .then(response => this.pagesCount = response.data.meta.pagesCount);*/
    },
    async getUsersCount() {
      return fetch('http://localhost:3000/api/users')
          .then(response => response.json())
          .then(data => this.usersCount = data.meta.usersCount);
    },
    sortCol(column, order) {
      this.sortColumn = column;
      this.sortOrder = order === "asc" ? "desc" : "asc";
      this.showOnFirstPage();
      this.sortDataTable();
    },
    sortDataTable() {
      if(this.sortColumn){
        let usersSort = this.users.slice(0, this.pageEnd());
        this.compare(this.sortOrder, this.sortColumn, usersSort);
        let usersNoSort = this.users.slice(this.pageEnd(), this.users.length);
        this.users = usersSort.concat(usersNoSort);
        this.showOnFirstPage();
      }
    },
    showOnFirstPage(){
      if(this.sortColumn !== this.previousSortColumn){
        this.previousSortColumn = this.sortColumn;
        this.currentPage = 1;
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
    extractData({ id, firstName, lastName, active: status, gender, email, dob: age, phone, avatar, address }) {
      let birthdate = this.formatBirthDate(age);
      status = this.getStatus(status);
      age = this.countAge(age);
      /*avatar = this.existImageAvatar(avatar);*/
      return { id, firstName, lastName, status, gender, email, age, phone, avatar, birthdate, address };
    },
    /*existImageAvatar(avatar) {
      let avatar2 = "";
      let xmlHttp = new XMLHttpRequest();
      xmlHttp.open( "GET", avatar, false); // false for synchronous request
      xmlHttp.send( null );
      let status = xmlHttp.status;
      if(status === 403){
        return avatar2 = '../image/noimage.png';
      }
      return avatar;
    },*/
    updateSelectedUser(userId) {
      this.selectedUser = this.users.find(el => {
        if(el.id === userId) {
          return this.selectedUser = el;
        }
      })
    },
    compare( order, column, partOfSortUsers) {
      if(order === "asc") {
        partOfSortUsers.sort((a, b) => (a[column] > b[column]) ? 1 : -1)
      } else if(this.sortOrder === "desc") {
        partOfSortUsers.sort((a, b) => (a[column] < b[column]) ? 1 : -1)
      }
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
    nextPage(){
      //this.$emit("current-count-page", ++this.currentPage);
      this.currentPage++;
      this.sortDataTable();
    },
    prevPage(){
      //this.$emit("current-count-page", --this.currentPage);
      this.currentPage--;
    },
    countActualPage() {
      return Math.ceil(this.usersCount / this.size);
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

  img {
    height: 50px;
    width: 50px;
  }

  .arrows {
    height: 12px;
    width: 12px;
  }

</style>
