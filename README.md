# vue-user-pages
User pages components for Vue 2. (Sign In, Sign Up, Reset Password, Settings)

Relies on Firebase, Vuelidate, and Vuex. Reducing dependencies is the next step.

## Installation

    npm install @nylira/vue-user-pages

## Usage

    import VueRouter from 'vue-router'
    import { SignUp, SignIn, Reset, Settings } from '@nylira/vue-user-pages'

    const routes = [
      { path: '/signup', name: 'signup', SignUp },
      { path: '/signin', name: 'signin', SignIn },
      { path: '/reset', name: 'reset', Reset },
      { path: '/settings', name: 'settings', Settings },
    ]

    const router = new VueRouter({
      routes,
    })

    export default router
