<template>
  <div class="container-xxl py-3">
    <input type="text" @input="handleUpdate" :value="user.name" placeholder="Введите имя" class="d-block"/>
    <user-component :user="user"></user-component>
    <div v-if="userAgeUpdatedMessage" class="text-success"> {{ userAgeUpdatedMessage }}</div>
    <div class="my-2">
      <button @click="updateName" class="btn btn-primary">Изменить имя</button>
      <button-component @increase-age="increaseAge" @decrease-age="decreaseAge"></button-component>
    </div>
    <button @click="addUser" class="btn btn-primary">Добавить пользователя</button>
    <div v-if="showMessageAddedUser" class="text-success">Добавлен новый пользователь</div>
    <hr>
    <label>Введите имя
      <input type="text" v-model="userFilteredName"/>
    </label>
    <label>Введите от какого возраста
      <input type="text" v-model="userFilteredAge" @input="checkPositiveNumber"/>
    </label>
    <div class="text-danger" v-if="isPositiveNumber">Возраст должен быть позитивным числом</div>
    <ol>
      <li v-for="filteredUser in filteredUsers" class="my-1">
        {{ filteredUser.name }} - {{ filteredUser.age }} лет
      </li>
    </ol>
    <hr>
    <ol>
      <li v-for="user in myAndFetchUsers">
        {{ user }}
      </li>
    </ol>
    <hr>
  </div>
</template>

<script>
import UserComponent from "@/components/UserComponent.vue";
import ButtonComponent from "@/components/ButtonComponent.vue";

export default {
  name: "App",
  components: {
    UserComponent,
    ButtonComponent
  },
  data() {
    return {
      user: {
        name: 'Тимур',
        age: 22,
      },
      users: [
        {name: 'Тимур', age: 22},
        {name: 'Данил', age: 22},
        {name: 'Александр', age: 23},
        {name: 'Макс', age: 19},
      ],
      userFilteredName: '',
      userFilteredAge: '',
      fetchedUsers: [],
      isPositiveNumber: false,
      showMessageAddedUser: false,
      userAgeUpdatedMessage: '',
    }
  },
  computed: {
    filteredUsers() {
      return this.filterByAge(this.users.filter(user =>
          user.name.toLowerCase().includes(this.userFilteredName.toLowerCase().trim())
      ));
    },
    usersNames() {
      return [...this.users.map(user => user.name)];
    },
    myAndFetchUsers() {
      return [...this.usersNames, ...this.fetchedUsers];
    },
    usersLength() {
      return this.users.length;
    },
  },
  methods: {
    filterByAge(users) {
      const minAge = +this.userFilteredAge;
      if (!isNaN(minAge) && minAge > 0) {
        return users.filter(user => user.age >= minAge);
      }
      return users;
    },
    updateName() {
      this.user.name = 'Данил';
    },
    decreaseAge() {
      this.user.age--;
    },
    increaseAge() {
      this.user.age++;
    },
    handleUpdate(event) {
      this.user.name = event.target.value;
    },
    checkPositiveNumber(event) {
      const value = event.target.value;

      this.isPositiveNumber = isNaN(+value) || +value < 0;
    },
    async fetchUsers() {
      const response = await fetch('https://jsonplaceholder.typicode.com/users');
      const data = await response.json();

      this.fetchedUsers = data.map(user => user.name);
    },
    addUser() {
      this.users.push({name: this.user.name, age: this.user.age});
    },
    async sendUpdatedAgeToServer(age) {
      try {
        const response = await fetch('https://httpbin.org/post', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({age}),
        });

        if (response.ok) {
          console.log('Возраст успешно обновлен на сервере');
        } else {
          console.error('Ошибка при обновлении возраста на сервере');
        }
      } catch (error) {
        console.error('Ошибка сети:', error);
      }
    },
  },
  mounted() {
    this.fetchUsers();
  },
  watch: {
    user: {
      handler(newUser, oldUser) {
        console.log('New User:', newUser);
        console.log('Old User:', oldUser);
      },
      deep: true,
      immediate: true
    },
    usersLength(newLength, oldLength) {
      if (newLength > oldLength) {
        this.showMessageAddedUser = true;
      }
    },
    'user.age': {
      handler(newAge, oldAge) {
        if (newAge > oldAge) {
          this.userAgeUpdatedMessage = 'Возраст пользователя увеличен';
        } else {
          this.userAgeUpdatedMessage = 'Возраст пользователя уменьшен';
        }
        this.sendUpdatedAgeToServer(newAge);
      }
    },
  }
}
</script>

<style scoped>

</style>