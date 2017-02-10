<template>
<div class="page-user">
  <page-header title="Sign In" type="center"></page-header>
  <form class="form form-narrow" v-on:submit.prevent.default="validateSignIn">

    <div class="form-group" :class="{ 'form-group-error': $v.fields.email.$error }">
      <label for="user-signin-email">Email</label>
      <field
        id="user-signin-email"
        v-model="fields.email"
        type="email"
        placeholder="name@example.com"
      >
      </field>
      <form-msg name="Email" type="required" v-if="!$v.fields.email.required"></form-msg>
      <form-msg name="Email" type="valid" v-if="!$v.fields.email.email"></form-msg>
    </div><!--form-group-->

    <div class="form-group" :class="{ 'form-group-error': $v.fields.password.$error }">
      <label for="user-signin-password">Password</label>
      <field
        id="user-signin-password"
        v-model="fields.password"
        type="password"
        placeholder="Password"
      >
      </field>
      <form-msg name="Password" type="required" v-if="!$v.fields.password.required"></form-msg>
      <form-msg name="Password" type="length" min="8" max="1024" v-if="!$v.fields.password.minLength || !$v.fields.password.maxLength"></form-msg>
    </div><!--form-group-->

    <div class="form-footer">
      <router-link to="/reset">Forgot password?</router-link>
      <btn type="submit" value="Sign In"></btn>
    </div>

  </form>
</div>
</template>

<script>
import firebase from 'firebase'
import { mapGetters } from 'vuex'
import { required, minLength, maxLength, email } from 'vuelidate/lib/validators'
import PageHeader from '@nylira/vue-page-header'
import Btn from '@nylira/vue-button'
import Field from '@nylira/vue-input'
import FormMsg from '@nylira/vue-form-msg'
export default {
  name: 'page-user-signin',
  components: {
    PageHeader,
    Btn,
    Field,
    FormMsg
  },
  computed: {
    ...mapGetters(['sessionRequest'])
  },
  data () {
    return {
      fields: {
        email: '',
        password: ''
      }
    }
  },
  methods: {
    validateSignIn () {
      this.$v.$touch()
      if (this.$v.$error) return
      else this.signIn()
    },
    signIn () {
      let self = this
      let email = this.fields.email
      let password = this.fields.password
      firebase.auth().signInWithEmailAndPassword(email, password)
        .catch(function (error) {
          self.$store.commit('notifyError', { title: error.code, body: error.message })
        })
      firebase.auth().onAuthStateChanged(function (user) {
        if (user) {
          self.signInSuccess()
        }
      })
    },
    signInSuccess () {
      this.$store.commit('notifySignIn')
      if (this.sessionRequest) {
        this.$router.push(this.sessionRequest)
        this.$store.commit('setSessionRequest', '')
      } else {
        this.$router.push('/')
      }
    }
  },
  mounted () {
    document.querySelector('#user-signin-email').focus()

    let self = this
    firebase.auth().onAuthStateChanged(function (user) {
      if (user) {
        self.fields.email = user.email
        document.querySelector('#user-signin-password').focus()
      }
    })
  },
  validations: {
    fields: {
      email: {
        required,
        email
      },
      password: {
        required,
        minLength: minLength(8),
        maxLength: maxLength(1024)
      }
    }
  }
}
</script>
