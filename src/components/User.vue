<template>
  <div>
    <h1>{{updateUser.surname + ' ' + updateUser.name + ' ' + updateUser.patronymic}}</h1>
    <form class="form" @submit.prevent="addReport(report)">
      <div>
        <textarea cols="30" rows="10" name="report" v-model="report.report" placeholder="Отчет" required
          @blur="$v.report.report.$touch()" :class="{ 'input_error': $v.report.report.$error }"></textarea>
        <span v-if="$v.report.report.$error">
          Это поле обязательно для заполнения!
        </span>
      </div>
      <div>
        <input type="text" name="hours" v-model="report.hours" placeholder="Время" required
          @blur="$v.report.hours.$touch()" :class="{ 'input_error': $v.report.hours.$error }">
        <span v-if="$v.report.hours.$error">
          <template v-if="!$v.report.hours.number">
            Поле заполняется количеством часов за один день!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>
      <div>
        <input type="text" name="date" v-model="report.date" placeholder="Дата" required @blur="$v.report.date.$touch()"
          :class="{ 'input_error': $v.report.date.$error }">
        <span v-if="$v.report.date.$error">
          <template v-if="!$v.report.date.date">
            Введите правильный формат даты: ДД.ММ.ГГГГ!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>

      <input type="submit" class="button" value="Добавить">
    </form>
    <table class="table">
      <tr>
        <th>Описание</th>
        <th>Время</th>
        <th>Дата</th>
        <th></th>
      </tr>
      <tr class="report" v-for="report in reports" :key="report.id">
        <td>
          {{report.report}}
        </td>
        <td>
          {{report.hours}}
        </td>
        <td>
          {{report.date}}
        </td>
        <td>
          <button class="delete" :data-id="report.id" @click="deleteReport()">Удалить</button>
        </td>
      </tr>
    </table>
    <button class="button back" @click="openUsers()">На главную</button>
  </div>
</template>

<script>
  import {
    required
  } from 'vuelidate/lib/validators';
  import moment from 'moment';
  export default {
    name: 'Main',
    props: ['propUsers', 'propUserId'],
    data() {
      return {
        user: {},
        report: {
          report: '',
          hours: '',
          date: '',
          id: 0
        },
        reports: [],
        users: [],
        userId: 0,
        lastReportId: 0,
        stateEditButton: false,
      }
    },
    validations: {
      report: {
        report: {
          required,
        },
        hours: {
          required,
          number: val => /^[1-2]{0,1}[0-9]{0,1}$/.test(val),
        },
        date: {
          required,
          date: val => moment(val, 'DD.MM.YYYY', true).isValid(),
        },
      }
    },
    computed: {
      editButton() {
        return this.stateEditButton;
      },
      updateReport() {
        return this.reports
      },
      updateUser() {
        return this.user
      },
    },
    mounted() {
      this.users = [...this.propUsers]
      this.userId = this.propUserId
      this.users.map(user => {
        if (user.id === this.userId) {
          this.reports = user.reports
          this.user = user
        }
        return user
      })
      if (this.reports.length === 0) {
        this.lastReportId = 0;
      } else {
        this.lastReportId = this.reports.map(report => report.id).sort(function (a, b) {
          return b - a;
        })[0]
      }
    },
    methods: {
      addReport(lastReport) {
        let flag = true;
        for (const param in this.$v.report) {
          if (this.$v.report[param].$error === true) {
            flag = false
          }
        }
        if (flag) {
          this.lastReportId += 1
          lastReport.id = this.lastReportId
          this.reports.push({
            ...lastReport
          })
          for (const key in this.report) {
            this.report[key] = '';
          }
          this.users.reports = this.reports
          this.$v.report.$reset()
        }
      },
      deleteReport() {
        let id = Number(event.target.getAttribute('data-id'))
        this.reports = this.reports.filter(report => report.id !== id)
        this.users = this.users
          .map(user => {
            if (user.id === this.propUserId) {
              user.reports = this.reports
            }
            return user;
          })
      },
      openUsers() {
        this.$emit('updateUsers', [this.users, true])
      }
    }
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .h1 {
    color: rgb(110, 110, 110);
    text-transform: uppercase;
    font-size: 30px;
    margin-bottom: 60px;
  }

  .form {
    display: flex;
    flex-flow: row wrap;
    justify-content: space-between;
    width: 1180px;
  }

  .form>div {
    position: relative;
    width: 580px;
    margin-bottom: 20px;
  }

  .form>div:first-child {
    width: 100%
  }

  .form>div input,
  .form>div textarea {

    font-family: Roboto;
    width: 580px;
    box-sizing: border-box;
    padding: 10px 20px;
    border-radius: 5px;
    border: 2px solid rgb(147, 223, 147);

  }

  .form>div textarea {
    width: 100%;
    height: 200px;
  }

  ::-webkit-input-placeholder {
    color: rgb(110, 110, 110);
    font-size: 14px;
  }

  ::-moz-placeholder {
    color: rgb(110, 110, 110);
    font-size: 14px;
  }

  :-moz-placeholder {
    color: rgb(110, 110, 110);
    font-size: 14px;
  }

  :-ms-input-placeholder {
    color: rgb(110, 110, 110);
    font-size: 14px;
  }

  .form>div span {
    position: absolute;
    background: red;
    padding: 5px 10px;
    top: 0px;
    width: auto;
    white-space: nowrap;
    z-index: 1000;
    border-radius: 50px;
    border: 1px solid rgb(255, 255, 255);
    color: white;
    left: 10px;
    top: -20px;
    font-size: 12px;
  }

  .form>div span:before {
    content: '';
    position: absolute;
    display: block;
    border-top: 5px solid red;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-bottom: 5px solid transparent;
    bottom: -9px;
  }

  .form>div input.input_error,
  .form>div textarea.input_error {
    border-color: red;
  }

  .button {
    width: 1180px;
    background: rgb(147, 223, 147);
    border: 0px;
    border-radius: 5px;
    color: rgb(255, 255, 255);
    box-sizing: border-box;
    padding: 10px 20px;
    font-size: 20px;
    font-weight: 800;
    margin-bottom: 30px;
    cursor: pointer;
    transition: 0.3s;
  }

  .button:hover {
    opacity: 0.7;
  }

  .table {
    border-collapse: collapse;
    ;
    width: 100%;
    color: rgb(110, 110, 110)
  }

  .table tr td {
    padding: 10px 20px;
    border-right: 1px solid rgb(147, 223, 147);
    border-bottom: 1px solid rgb(147, 223, 147);
  }

  .table tr td:last-child {
    border-bottom: 1px solid rgb(255, 255, 255);
  }

  .table tr:last-child td:last-child {
    border-bottom: 1px solid rgb(147, 223, 147);
  }

  .table tr th {
    background: rgb(228, 228, 228);
  }

  .table tr td:first-child,
  .table tr th:first-child {
    width: 800px;
    height: auto;
  }

  .table tr td:last-child,
  .table tr th:last-child {
    width: 80px;
  }

  .table tr th {
    padding: 10px 20px;
  }

  .table tr td .user_link {
    text-decoration: underline;
    cursor: pointer;
  }

  .table tr td:last-child {
    background: rgb(147, 223, 147)
  }

  .table tr td .delete {
    width: 100%;
    height: 100%;
    border: 0px;
    background: rgb(147, 223, 147);
    font-size: 16px;
    color: white;
    font-weight: 800;
    cursor: pointer;
  }

  .popap_form {
    position: fixed;
    background: white;
    padding: 60px 40px 40px;
    box-shadow: 5px 5px 15px 5px rgba(0, 0, 0, 0.2);
    top: 100px;
    left: calc(50% - 630px);
    text-align: center;
    z-index: 2000
  }

  .popap_form .form>div span {
    z-index: 2000;
  }

  .cross {
    position: absolute;
    display: inline-block;
    width: 20px;
    height: 20px;
    cursor: pointer;
  }

  .cross:before {
    content: '';
    display: block;
    position: absolute;
    width: 24px;
    height: 2px;
    background: rgb(0, 0, 0);
    transform: rotate(45deg);
    top: 9px;
    left: -2px
  }

  .cross:after {
    content: '';
    display: block;
    position: absolute;
    width: 24px;
    height: 2px;
    background: rgb(0, 0, 0);
    transform: rotate(-45deg);
    top: 9px;
    left: -2px
  }

  .back {
    margin-top: 40px;
    background: transparent;
    color: rgb(147, 223, 147);
    border: 2px solid rgb(147, 223, 147);
  }

</style>
