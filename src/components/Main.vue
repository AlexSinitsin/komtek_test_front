<template>
  <div class="main">
    <Users v-if="calcPage" :propUsers = users @updateUsers="onUpdateUsers"/>
    <User v-else :propUsers = users :propUserId = idUser @updateUsers="onUpdateUsers"/>
  </div>
</template>

<script>
import User from '@/components/User.vue'
import Users from '@/components/Users.vue'
  export default {
    components: {
      User, Users
    },
    name: 'Main',
    data() {
      return {
        users: [],
        page: true,
        idUser: 0,
      }
    },
    computed: {
      calcPage () {
        return this.page;
      }
    },
    methods: {
      onUpdateUsers([users, page, id = 0]) {
        this.users = users
     //   console.log(this.users)
        this.page = page
        this.idUser = id
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
  .main {
    width: 1180px;
    margin: 0px auto;
  }

</style>
