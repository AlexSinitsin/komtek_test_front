<template>
  <div>
    <h1 class="h1">Пользователи</h1>
    <form class="form" @submit.prevent="addUser(user)">
      <div>
        <input type="email" name="email" v-model="user.email" placeholder="Email" @blur="$v.user.email.$touch()"
          :class="{ 'input_error': $v.user.email.$error }">
        <span v-if="$v.user.email.$error">
          <template v-if="!$v.user.email.email">
            Это поле должно быть электронной почтой!
          </template>
          <template v-else-if="!$v.user.email.unic">
            Пользователь с таким email уже существует!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>
      <div>
        <input type="text" name="surname" v-model="user.surname" placeholder="Фамилия" required
          @blur="$v.user.surname.$touch()" :class="{ 'input_error': $v.user.surname.$error }">
        <span v-if="$v.user.surname.$error">
          <template v-if="!$v.user.surname.rus">
            Поле заполняется кирилицей!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>
      <div>
        <input type="text" name="name" v-model="user.name" placeholder="Имя" required @blur="$v.user.name.$touch()"
          :class="{ 'input_error': $v.user.name.$error }">
        <span v-if="$v.user.name.$error">
          <template v-if="!$v.user.name.rus">
            Поле заполняется кирилицей!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>
      <div>
        <input type="text" name="patronymic" v-model="user.patronymic" placeholder="Отчество"
          @blur="$v.user.patronymic.$touch()" :class="{ 'input_error': $v.user.patronymic.$error }">
        <span v-if="$v.user.patronymic.$error">
          <template v-if="!$v.user.patronymic.rus">
            Поле заполняется кирилицей!
          </template>
        </span>
      </div>
      <input type="submit" class="button" value="Добавить">
    </form>
    <table class="users_table">
      <tr class="user" v-for="user in updateUsers" :key="user.id">
        <td>
          <div @click="openUser()" :data-userId="user.id" class="user_link">
            {{user.surname + ' ' + user.name + ' ' + user.patronymic}}</div>
        </td>
        <td>
          {{user.email}}
        </td>
        <td>
          <button class="edit" :data-id="user.id" @click="openForm()">Редактировать</button>
        </td>
      </tr>
    </table>
    <form class="popap_form" @submit.prevent="editUser()" v-show="updateOpenForm">
      <div>
        <input type="email" name="email" v-model="updateEditedUser.email" placeholder="Email"
          @blur="$v.editedUser.email.$touch()" :class="{ 'input_error': $v.editedUser.email.$error }">
        <span v-if="$v.editedUser.email.$error">
          <template v-if="!$v.editedUser.email.email">
            Это поле должно быть электронной почтой!
          </template>
          <template v-else-if="!$v.editedUser.email.unic">
            Пользователь с таким email уже существует!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>
      <div>
        <input type="text" name="surname" v-model="updateEditedUser.surname" placeholder="Фамилия" required
          @blur="$v.editedUser.surname.$touch()" :class="{ 'input_error': $v.editedUser.surname.$error }">
        <span v-if="$v.editedUser.surname.$error">
          <template v-if="!$v.editedUser.surname.rus">
            Поле заполняется кирилицей!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>
      <div>
        <input type="text" name="name" v-model="updateEditedUser.name" placeholder="Имя" required
          @blur="$v.editedUser.name.$touch()" :class="{ 'input_error': $v.editedUser.name.$error }">
        <span v-if="$v.editedUser.name.$error">
          <template v-if="!$v.editedUser.name.rus">
            Поле заполняется кирилицей!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>
      <div>
        <input type="text" name="patronymic" v-model="updateEditedUser.patronymic" placeholder="Отчество"
          @blur="$v.editedUser.patronymic.$touch()" :class="{ 'input_error': $v.editedUser.patronymic.$error }">
        <span v-if="$v.editedUser.patronymic.$error">
          <template v-if="!$v.editedUser.patronymic.rus">
            Поле заполняется кирилицей!
          </template>
        </span>
      </div>
      <input type="submit" class="button" value="Редактировать профиль">
      <button type="submit" class="button" @click="closeForm()">close</button>
    </form>
  </div>
</template>

<script>
  import {
    required,
    email
  } from 'vuelidate/lib/validators';

  const valUsers = [];

  export default {
    name: 'Main',
    props: ['propUsers'],
    data() {
      return {
        user: {
          email: '',
          surname: '',
          name: '',
          patronymic: '',
          id: 0,
          reports: [],
        },
        users: [],
        editedUser: {
          email: '',
          surname: '',
          name: '',
          patronymic: '',
          id: 0,
          reports: [],
        },
        lastUserId: 0,
        editUserId: 0,
        openFormFlag: false,
      }
    },
    validations: {
      user: {
        email: {
          required,
          email: email,
          unic: function (val) {
            if (this.users.length > 0) {
       //       console.log(val)
              return this.users.filter(user => user.email === val).length === 0
            }
            return true
          },
        },
        name: {
          required,
          rus: val => /^[а-яё]*$/i.test(val),
        },
        surname: {
          required,
          rus: val => /^[а-яё]*$/i.test(val),
        },
        patronymic: {
          rus: val => /^[а-яё]*$/i.test(val),
        }
      },
      editedUser: {
        email: {
          required,
          email: email,
        },
        name: {
          required,
          rus: val => /^[а-яё]*$/i.test(val),
        },
        surname: {
          required,
          rus: val => /^[а-яё]*$/i.test(val),
        },
        patronymic: {
          rus: val => /^[а-яё]*$/i.test(val),
        }
      }
    },
    computed: {
      updateOpenForm() {
        return this.openFormFlag;
      },
      updateEditedUser() {
        return this.editedUser
      },
      updateUsers() {
        return this.users
      }
    },
    mounted() {
      this.users = [...this.propUsers]
      this.lastUserId = this.users.map(user => user.id).sort(function (a, b) {
        return b - a;
      })[0]
      if (this.users.length === 0) {
        this.lastUserId = 0;
      }
    },
    methods: {
      addUser(lastUser) {
        let flag = true;
        for (const param in this.$v.user) {
          if (this.$v.user[param].$error === true) {
            flag = false
          }
        }
        if (flag) {
          this.lastUserId += 1
          lastUser.id = this.lastUserId
          this.users.push({
            ...lastUser
          })
          for (const key in this.user) {
            this.user[key] = '';
          }
          this.$emit('updateUsers', [this.users, true])
          this.$v.user.$reset()
        }
      },
      openForm() {

        let id = Number(event.target.getAttribute('data-id'))
        this.users.map(user => {
          if (user.id === id) {
            this.editedUser = {
              ...user
            }
          }
        })
        this.openFormFlag = true;
        this.editUserId = id;

      },
      closeForm() {
        this.openFormFlag = false;
      },
      editUser() {
        let flag = true;
        for (const param in this.$v.editedUser) {
          if (this.$v.editedUser[param].$error === true) {
            flag = false
          }
        }
        if (flag) {
          let users = this.users.map(user => {
        //    console.log(user.id)
       //     console.log(this.editUserId)
            if (user.id === this.editUserId) {
       //       console.log(this.editedUser)
              return {
                ...this.editedUser
              }
            }
            return user
          })
          this.users = users
          this.$emit('updateUsers', [this.users, true])
          this.$v.editedUser.$reset()
        }
      },
      openUser() {
        let id = Number(event.target.getAttribute('data-userId'))
        this.$emit('updateUsers', [this.users, false, id])
      }
    }
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .h1 {
    color:rgb(110, 110, 110);
    text-transform: uppercase;
    font-size: 30px;
  }
  .form {
    display: flex;
    flex-flow: row wrap;
    justify-content:space-between;
    width: 1000px;
  }
  .form > div {
    width: 480px;
    margin-bottom: 20px;
  }
  .form > div input {
    width: 480px;
    box-sizing: border-box;
    padding: 10px 20px;
    border-radius: 5px;
    border: 2px solid rgb(147, 223, 147);;

  }
  ::-webkit-input-placeholder {color:rgb(110, 110, 110); font-size: 14px;;}
::-moz-placeholder          {color:rgb(110, 110, 110)}/* Firefox 19+ */
:-moz-placeholder           {color:rgb(110, 110, 110)}/* Firefox 18- */
:-ms-input-placeholder      {color:rgb(110, 110, 110)}

  .form .button {
    width: 1000px;
  }
</style>
