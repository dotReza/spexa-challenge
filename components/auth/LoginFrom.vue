<template>
  <v-form ref="form" @submit.prevent="submit">
    <v-card color="transparent" flat>
      <v-card-title class="justify-center">{{ title }}</v-card-title>
      <v-card-subtitle v-if="snackbar.value" class="text-center error--text">
        {{ snackbar.message }}
      </v-card-subtitle>
      <v-card-text>
        <v-text-field
          v-model="user.email"
          :rules="[rules.required, rules.email]"
          outlined
          clearable
          label="email"
        ></v-text-field>
        <v-text-field
          v-model="user.password"
          :rules="[rules.required]"
          :type="passwordField"
          outlined
          label="password"
          :append-icon="passwordIcon"
          @click:append="showPassword"
        ></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn dark type="submit" :loading="loading" block
          >Register / Login</v-btn
        >
      </v-card-actions>
    </v-card>
  </v-form>
</template>

<script>
import { mapGetters, mapMutations } from 'vuex'
import { mdiEyeOff, mdiEye } from '@mdi/js'
export default {
  name: 'LoginForm',
  data() {
    return {
      title: 'Try the product out fo free',
      user: {
        email: '',
        password: '',
      },
      rules: {
        required: (value) => !!value || 'Required.',
        email: (value) => {
          const pattern =
            /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
          return pattern.test(value) || 'Invalid e-mail.'
        },
      },
      passwordVisable: false,
    }
  },
  computed: {
    ...mapGetters(['loading', 'snackbar']),
    passwordIcon() {
      if (!this.user.password) {
        return ''
      } else return this.passwordVisable ? mdiEye : mdiEyeOff
    },
    passwordField() {
      return this.passwordVisable ? 'text' : 'password'
    },
  },
  methods: {
    ...mapMutations({
      setRootDirectoryId: 'directory/setRootDirectoryId',
      setUserEmail: 'setUserEmail',
    }),
    async submit() {
      if (this.$refs.form.validate()) {
        const res = await this.$auth.loginWith('local', {
          data: this.user,
        })
        const data = await res.data.data
        this.setUserEmail(this.user.email)
        this.setRootDirectoryId(data.root_directory_id)
        this.$router.push('/')
      }
    },
    showPassword() {
      this.passwordVisable = !this.passwordVisable
    },
  },
}
</script>

<style></style>
