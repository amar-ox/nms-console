<template>
  <div class="auth-layout row align-content--center">
    <div class="flex xs12 pa-3 flex-center">
      <p align="center">
        <img src="./logo.png" />
      </p>
    </div>

    <div class="flex xs12 pa-3">
      <div class="d-flex justify--center">
        <va-card class="auth-layout__card">
          <div class="row">
            <va-notification color="danger" v-if="error != ''">
              {{ error }}
            </va-notification>
          </div>

          <form @submit.prevent="onsubmit">
            <va-input
              v-model="username"
              type="text"
              label="Username"
              :error="!!usernameErrors.length"
              :error-messages="usernameErrors"
            />

            <va-input
              v-model="password"
              type="password"
              label="Password"
              :error="!!passwordErrors.length"
              :error-messages="passwordErrors"
            />

            <div class="d-flex justify--center mt-3">
              <va-button type="submit" class="my-0">Login</va-button>
            </div>
          </form>
        </va-card>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Login',
  components: {},
  data () {
    return {
      username: '',
      password: '',
      usernameErrors: [],
      passwordErrors: [],
      error: '',
    }
  },
  computed: {
    formReady () {
      return !this.usernameErrors.length && !this.passwordErrors.length
    },
  },
  methods: {
    onsubmit () {
      this.usernameErrors = this.username ? [] : ['Username is required']
      this.passwordErrors = this.password ? [] : ['Password is required']
      if (!this.formReady) {
        return
      }
      const user = { username: this.username, password: this.password }
      axios.post(this.$apiURI + '/login/user', user, {
        headers: {},
      })
        .then(response => {
          const token = response.data
          axios.defaults.headers.common.Authorization = 'Bearer ' + token
          localStorage.setItem('user-token', token)
          this.$router.push('/')
        })
        .catch(e => {
          this.error = 'Authentication failed'
          localStorage.removeItem('user-token')
        })
    },
  },
}
</script>

<style lang="scss">
.auth-layout {
  min-height: 100vh;
  // background-image: linear-gradient(to right, #5d5e61, #131316);
  background-color: #3e3f41;

  &__card {
    width: 100%;
    max-width: 600px;
  }

  &__options {
    @include media-breakpoint-down(xs) {
      flex-direction: column;
    }
  }
}
</style>
