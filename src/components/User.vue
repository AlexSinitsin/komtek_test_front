<template>
  <div>
    <h1>{{updateUser.surname + ' ' + updateUser.name + ' ' + updateUser.patronymic}}</h1>
    <form class="form" @submit.prevent="addReport(report)">
      <div>
        <input type="text" name="report" v-model="report.report" placeholder="Отчет" required
          @blur="$v.report.report.$touch()" :class="{ 'input_error': $v.report.report.$error }">
        <span v-if="$v.report.report.$error">
          <template v-if="!$v.report.report.rus">
            Поле заполняется кирилицей!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
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
            Введите правильный формат даты!
          </template>
          <template v-else>
            Это поле обязательно для заполнения!
          </template>
        </span>
      </div>

      <input type="submit" class="button" value="Добавить">
    </form>
    <table class="report_table">
      <tr>
        <th>Описание</th>
        <th>Время</th>
        <th>Дата</th>
        <th></th>
      </tr>
      <tr class="report" v-for="report in reports" :key="report.id">
        <td>
          <div>{{report.report}}</div>
        </td>
        <td>
          <div>{{report.hours}}</div>
        </td>
        <td>
          <div>{{report.date}}</div>
        </td>
        <td>
          <button class="delete" :data-id="report.id" @click="deleteReport()">Удалить запись</button>
        </td>
      </tr>
    </table>
    <button @click="openUsers()">На главную</button>
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
          rus: val => /^[а-яё]*$/i.test(val),
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
      //    console.log(this.user)
        }
        return user
      })
      this.lastReportId = this.reports.map(report => report.id).sort(function (a, b) {
        return b - a;
      })[0]
      if (this.reports.length === 0) {
        this.lastReportId = 0;
      }
   //   console.log(this.lastReportId)
    },
    methods: {
      addReport(lastReport) {
        let flag = true;
        for(const param in this.$v.report) {
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
          console.log(this.reports)
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
          console.log(user.id)
          console.log(id)
          console.log(user.id === id)
          if (user.id === this.propUserId) {
            user.reports = this.reports
          //  console.log(this.reports)
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
  h1,
  h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }

</style>
