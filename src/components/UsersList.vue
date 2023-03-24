<template>
   <div>
      <div class="user__search">
      <input v-model="searchText" placeholder="Поиск" class="user__input">
      <ul class="user__input-info" v-if="searchInfo.length">
         <li  v-for="info of searchInfo" :key="info.id" class="input__info-item">
            {{ `${info.name} (${info.email})` }}
         </li>
      </ul>
      </div>
      <table class="user__table">
         <thead>
            <th>Имя 
               <span @click="sortTable($event)" class="user__sort" data-table="name">&#9660;</span>
            </th>
            <th>Фамилия 
               <span @click="sortTable($event)" class="user__sort" data-table="family">&#9660;</span>
            </th>
            <th>Эл. почта 
               <span @click="sortTable($event)" class="user__sort" data-table="email">&#9660;</span>
            </th>
         </thead>
         <tbody>
            <tr v-for="user in users" :key="user.id">
               <td>{{ firstName(user) }}</td>
               <td>{{ lastName(user)  }}</td>
               <td>{{ user.email }}</td>
            </tr>
         </tbody>
      </table>
   </div>
</template>

<script>
import getUserList from '@/api/getUserList';

   export default {
      data() {
         return {
            users: [],
            searchText: '',
            searchInfo: [],
         }
      },
      mounted() {
         getUserList().then((result) => {
            this.users = result;
         });
         document.addEventListener('click', this.watchClickDocument);
      },
      destroyed() {
         document.removeEventListener('click', this.watchClickDocument);
      },
      methods: {
         firstName(user) {
            const name = user.name.split(' ')[0];
            return name ? name : '-';
         },
         lastName(user) {
            const family = user.name.split(' ')[1];
            return family ? family : '-';
         },
         watchClickDocument(event) {
               if (this.searchInfo.length && !event.target.closest('.user__search') ) {
                  this.searchInfo = [];
               }
         },
         sortTable(event) {
            const targetData = event.target.dataset.table;
            switch(targetData) {
               case 'name': 
                  this.users = this.users.sort((a, b) => a.name.split(' ')[0] > b.name.split(' ')[0] ? 1 : -1);
               break;
               case 'family': 
                  this.users = this.users.sort((a, b) => a.name.split(' ')[1] > b.name.split(' ')[1] ? 1 : -1)
               break;
               case 'email': 
                  this.users = this.users.sort((a, b) => a.email > b.email ? 1 : -1)
               break;
            }
            document.querySelectorAll('.user__sort').forEach((span) => {
               span.style.color = 'black';
            })
            event.target.style.color = 'green';
         }
      },
      watch: {
         searchText(newSearch) {
            const searchText = newSearch.toLowerCase();
            if (searchText.length) {
               this.searchInfo = this.users.filter(user => {
                  const name = user.name.toLowerCase();
                  const email = user.email.toLowerCase();

                  if (name.includes(searchText) || email.includes(searchText)) {
                     return true;
                  }
               })
            } else {
               this.searchInfo = [];
            }
         }
      }
   }
</script>

<style scoped>
.user__table {
	width: 100%;
   margin: 20px 0;
	border: 1px solid #dddddd;
	border-collapse: collapse; 
}
.user__table th {
	font-weight: bold;
	padding: 5px;
	background: #efefef;
	border: 1px solid #dddddd;
}
.user__table td {
	border: 1px solid #dddddd;
	padding: 5px;
   text-align: center;
}

.user__search {
   display: inline-block;
   position: relative;
}

.user__input {
   width: 25%;
   min-width: 300px;
   height: 25px;
   text-align: center;
}

.user__input-info {
   position: absolute;
   background-color: rgb(241, 241, 241);
   width: 100%;
   overflow-y: auto;
   padding: 0;
   margin: 0;
   color: rgb(0, 0, 0);
   border-radius: 0 0 5px 5px;
}

.input__info-item {
   transition: all .5s ease;
   padding: 5px;
}
.input__info-item:hover {
   background-color: rgb(199, 254, 213);
}

.user__sort {
   cursor: pointer;
}
</style>

