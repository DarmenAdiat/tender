<template>
  <v-container style='max-width: 1280px;'>
    <v-row>
      <v-col
        cols='12'
        sm='12'
      >
        <v-sheet
          rounded='lg'
          align='center'
          style='width: 670px;margin: auto;background-color: transparent!important'
        >
          <!--  -->
          <v-container fluid>
            <v-row dense>
              <v-col>
                <v-card
                  outlined
                  style='width:600px;margin: 20px;min-height: 100px;padding: 20px;'
                >
                  <h1 style='margin: 0 auto 10px'>Вход на сайт</h1>

                  <v-btn-toggle
                    v-model="wayToEnter"
                    style='margin-bottom: 20px;width: 100%'
                    mandatory
                  >
                    <v-btn
                      width='50%'
                    >
                      <v-icon>mdi-email</v-icon>
                      &nbsp;{{ $t('login_by_email') }}
                    </v-btn>

                    <v-btn
                      width='50%'
                    >
                      {{ $t('login_by_bin') }}&nbsp;
                      <v-icon>confirmation_num</v-icon>
                    </v-btn>
                  </v-btn-toggle>


                  <!-- Если пользователь НЕ существует -->
                  <v-form
                    v-if='exist === false && active === false && wayToEnter === 0'
                    ref='form'
                    v-model='valid'
                    @submit.prevent="checkUsername"
                  >
                    <v-text-field
                      v-model='login'
                      :rules='loginRules'
                      :error-messages="error_message_username"
                      :label="$t('login_username')"
                      prepend-icon='mdi-account'
                      autocomplete='email'
                      required
                    ></v-text-field>
                    <v-btn
                       class='primary'
                       style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                       @click='checkUsername'
                     >
                      {{ $t('login_next') }}
                     </v-btn>
                  </v-form>

                  <!-- Если пользователь существует, и Активен = Логиним -->
                  <v-form
                    v-if='exist === true && active === true && wayToEnter === 0'
                    ref='form'
                    v-model='valid'
                    @submit.prevent="submit"
                  >
                    <v-text-field
                      v-model='login'
                      :rules='loginRules'
                      :label="$t('login_username')"
                      prepend-icon='mdi-account'
                      autocomplete='email'
                      disabled
                      required
                    ></v-text-field>
                    <v-text-field
                      v-if='!resetEmail'
                      v-model="password"
                      :rules="passwordRules"
                      :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show ? 'text' : 'password'"
                      :error-messages="error_message_pass"
                      prepend-icon="mdi-lock"
                      autofocus
                      counter
                      :label="$t('login_pass')"
                      autocomplete="password"
                      required
                      @click:append="show = !show"
                    ></v-text-field>

                    <v-btn
                      v-if='!resetEmail'
                      class='primary'
                      type="submit"
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                    >
                      {{ $t('login_enter') }}
                    </v-btn>

                    <h5
                      v-if='!resetEmail'
                      style='margin: 10px auto 0'
                    >
                      <a @click='resetByEmail'>{{ $t('login_forgot_pass') }}</a>
                    </h5>

                    <v-btn
                      v-if='resetEmail'
                      class='primary'
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click='sendResetCode'
                    >
                      {{ $t('login_restore') }}
                    </v-btn>

                    <v-text-field
                      v-if='codeSent'
                      v-model="emailCode"
                      v-mask="'######'"
                      :rules='codeRules'
                      :disabled='validCode'
                      :error-messages="error_message_code"
                      type='number'
                      style='width: 100%; margin-top: 20px'
                      :label="$t('login_code')"
                      :placeholder="$t('login_code_ph')"
                      prepend-icon="mdi-lock"
                      reqiured
                    ></v-text-field>
                    <v-btn
                      v-if='codeSent'
                      :disabled='validCode'
                      :hidden='validCode'
                      class='primary'
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click='checkCode'
                    >{{ $t('login_code_enter') }}</v-btn>

                    <h3
                      v-if='validCode'
                      style='margin: 10px;'
                    >{{ $t('login_set_pass') }}</h3>
                    <v-text-field
                      v-if='validCode'
                      v-model="password"
                      :rules="passwordRules"
                      :append-icon="show2 ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show2 ? 'text' : 'password'"
                      :error-messages="error_message_new_pass"
                      prepend-icon="mdi-lock"
                      counter
                      :label="$t('login_pass_enter')"
                      required
                      @click:append="show2 = !show2"
                    ></v-text-field>
                    <v-text-field
                      v-if='validCode'
                      v-model="passwordRepeat"
                      :rules="[passwordConfirmationRule]"
                      :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show ? 'text' : 'password'"
                      prepend-icon="mdi-lock"
                      counter
                      :label="$t('login_pass_repeat')"
                      required
                      @click:append="show = !show"
                    ></v-text-field>
                    <v-btn
                      v-if='validCode'
                      class="info"
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click="saveNewPassword"
                    >
                      {{ $t('login_pass_save') }}
                    </v-btn>

                  </v-form>

                  <!-- Если пользователь существует, но НЕактивен -->
                  <v-form
                    v-if='exist === true && active === false && wayToEnter === 0'
                    ref='form'
                    v-model='valid'
                    @submit.prevent=""
                  >
                    <v-text-field
                      v-model='login'
                      :rules='loginRules'
                      :label="$t('login_username')"
                      prepend-icon='mdi-account'
                      autocomplete='email'
                      disabled
                      required
                    ></v-text-field>
                    <v-btn
                      class='primary'
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      :disabled='codeSent'
                      @click='sendCode'
                    >
                      {{ $t('login_code_send') }}
                    </v-btn>

                    <v-text-field
                      v-if='codeSent'
                      v-model="emailCode"
                      v-mask="'######'"
                      :rules='codeRules'
                      :disabled='validCode'
                      :error-messages="error_message_code"
                      type='number'
                      style='width: 100%; margin-top: 20px'
                      :label="$t('login_code')"
                      :placeholder="$t('login_code_ph')"
                      prepend-icon="mdi-lock"
                      reqiured
                    ></v-text-field>
                    <v-btn
                      v-if='codeSent'
                      :disabled='validCode'
                      :hidden='validCode'
                      class='primary'
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click='checkCode'
                    >{{ $t('login_code_enter') }}</v-btn>

                    <h3
                      v-if='validCode'
                      style='margin: 10px;'
                    >{{ $t('login_set_pass') }}</h3>
                    <v-text-field
                      v-if='validCode'
                      v-model="password"
                      :rules="passwordRules"
                      :append-icon="show2 ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show2 ? 'text' : 'password'"
                      :error-messages="error_message_new_pass"
                      prepend-icon="mdi-lock"
                      counter
                      :label="$t('login_pass_enter')"
                      required
                      @click:append="show2 = !show2"
                    ></v-text-field>
                    <v-text-field
                      v-if='validCode'
                      v-model="passwordRepeat"
                      :rules="[passwordConfirmationRule]"
                      :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show ? 'text' : 'password'"
                      prepend-icon="mdi-lock"
                      counter
                      :label="$t('login_pass_repeat')"
                      required
                      @click:append="show = !show"
                    ></v-text-field>
                    <v-checkbox
                      v-if='validCode'
                      v-model="checkbox"
                      :rules="checkboxRules"
                    >
                      <template #label>
                        <div>
                          {{ $t('login_confirm_rules') }}
                          <v-tooltip bottom>
                            <template #activator="{ on }">
                              <a
                                target="_blank"
                                href="/privacy.docx"
                                @click.stop
                                v-on="on"
                              >
                                {{ $t('login_confirm_rules2') }}
                              </a>
                            </template>
                            {{ $t('login_confirm_rules3') }}
                          </v-tooltip>
                          {{ $t('login_confirm_rules4') }}
                        </div>
                      </template>
                    </v-checkbox>
                    <v-btn
                      v-if='validCode'
                      class="info"
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click="saveNewPassword"
                    >
                      {{ $t('login_pass_save') }}
                    </v-btn>
                  </v-form>
                  <!-- LOGIN BY BIN -->
                  <v-form
                    v-if='wayToEnter === 1'
                    id='login_bin'
                    ref='form'
                    v-model='valid'
                    @submit.prevent="submit_bin"
                  >
                    <v-text-field
                      v-model='login_bin'
                      :rules='login_binRules'
                      type='number'
                      :label="$t('login_user_bin')"
                      prepend-icon='mdi-account'
                      autocomplete='bin'
                      min='0'
                      :error-messages="error_message_username"
                      counter
                      autofocus
                      required
                    ></v-text-field>
                    <v-text-field
                      v-if='!resetBin'
                      v-model="password"
                      :rules="passwordRules"
                      :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show ? 'text' : 'password'"
                      :error-messages="error_message_pass"
                      prepend-icon="mdi-lock"
                      counter
                      :label="$t('login_pass')"
                      autocomplete="password"
                      required
                      @click:append="show = !show"
                    ></v-text-field>

                    <v-btn
                      v-if='!resetBin'
                      class='primary'
                      type="submit"
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                    >
                      {{ $t('login_enter') }}
                    </v-btn>
                    <h5
                      v-if='!resetBin'
                      style='margin: 10px auto 0'
                    >
                      <a @click='resetByBin'>{{ $t('login_forgot_pass') }}</a>
                    </h5>
                    <v-btn
                      v-if='resetBin && !hideCheckBin'
                      class='primary'
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click='checkBin'
                    >
                      {{ $t('login_enter_bin') }}
                    </v-btn>

                    <h3
                      v-if='codeSent'
                    >{{ $t('login_email_sent') }} {{ resetBinMessage }} {{ $t('login_email_sent2') }}</h3>


                    <v-text-field
                      v-if='codeSent'
                      v-model="emailCode"
                      v-mask="'######'"
                      :rules='codeRules'
                      :disabled='validCode'
                      :error-messages="error_message_code"
                      type='number'
                      style='width: 100%; margin-top: 20px'
                      :label="$t('login_code')"
                      :placeholder="$t('login_code_ph')"
                      prepend-icon="mdi-lock"
                      reqiured
                    ></v-text-field>
                    <v-btn
                      v-if='codeSent'
                      :disabled='validCode'
                      :hidden='validCode'
                      class='primary'
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click='checkCode'
                    >{{ $t('login_code_enter') }}</v-btn>

                    <h3
                      v-if='validCode'
                      style='margin: 10px;'
                    >{{ $t('login_set_pass') }}</h3>
                    <v-text-field
                      v-if='validCode'
                      v-model="password"
                      :rules="passwordRules"
                      :append-icon="show2 ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show2 ? 'text' : 'password'"
                      :error-messages="error_message_new_pass"
                      prepend-icon="mdi-lock"
                      counter
                      :label="$t('login_pass_enter')"
                      required
                      @click:append="show2 = !show2"
                    ></v-text-field>
                    <v-text-field
                      v-if='validCode'
                      v-model="passwordRepeat"
                      :rules="[passwordConfirmationRule]"
                      :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show ? 'text' : 'password'"
                      prepend-icon="mdi-lock"
                      counter
                      :label="$t('login_pass_repeat')"
                      required
                      @click:append="show = !show"
                    ></v-text-field>
                    <v-btn
                      v-if='validCode'
                      class="info"
                      style='width: 100%;height:40px;margin: 20px auto 10px;text-align: center;'
                      @click="saveNewPassword"
                    >
                      {{ $t('login_pass_save') }}
                    </v-btn>


                  </v-form>



                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { VueMaskDirective } from 'v-mask'
import Vue from 'vue'
Vue.directive('mask', VueMaskDirective);

export default {
  name: 'Login',

  data() {
    return {
    exist: false, // существует ли логин в базе
    active: false, // активирован ли пользователь

    wayToEnter: 0,

    resetEmail: false, // восстановить пароль по Email-у
    resetBin: false, // восстановить пароль по БИНу

    resetEmailCodeSent: false, // код подтверждения сброса отправлен

    resetBinMessage: '',
    hideCheckBin: false,

    codeSent: false, // отправлен ли код подтверждения - открываем инпут проверки кода
    // codeSent: true, // отправлен ли код подтверждения - открываем инпут проверки кода
    emailCode: '', // переменная введенного кода подтверждения
    validCode: false, // правильно ли введен код подтверждения - открываем ввод пароля
    // validCode: true, // правильно ли введен код подтверждения - открываем ввод пароля

    valid: false, // модель формы

    error_message_username: '',
    error_message_pass: '',
    error_message_code: '',
    error_message_new_pass: '',

    // checkLogin: false,
    login: '',
    login_bin: '',
    // password data
    password: '',
    passwordRepeat: '',
    show: false,
    show2: false,
    // Rules
    loginRules: [
      v => !!v || this.$t('login_rules_username'),
      v => /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || v === 'jfox' || this.$t('login_rules_username_err'),
    ],
    login_binRules: [
      v => !!v || this.$t('rules_empty'),
      v => v && v.length <= 50 || this.$t('rules_max50'),
    ],
    passwordRules: [
      v => !!v || this.$t('login_rules_pass_req'),
      v => v && v.length <= 100 || this.$t('rules_max100'),
      // v => v && v.length >= 8 || "Минимум 8 символов",
      v => !v || /\d/.test(v) || this.$t('login_rules_pass_dig'),
      // v => !v || /[a-z]/.test(v) || 'Пароль должен содержать минимум одну букву нижнего регистра',
      // v => !v || /[A-Z]/.test(v) || 'Пароль должен содержать минимум одну букву верхнего регистра',
    ],
    codeRules: [
      v => !!v || this.$t('login_code_ph'),
      v => v && v.length === 6 || this.$t('login_rules_code_dig'),
    ],
    checkbox: false,
    checkboxRules:[
      v => v === true || this.$t('login_rules_confirm'),
    ],
  }},
  head () {
    return {
      title: this.$t('login_rules_auth')
    }
  },
  computed:{
    passwordConfirmationRule() {
      // check is repeated password is similar with first entered
      return () => (this.password === this.passwordRepeat) || this.$t('login_rules_pass_rep_err')
    },
  },

  watch:{
    wayToEnter(){
      // if (!this.wayToEnter) this.wayToEnter = 0
      console.log(this.wayToEnter)
    }
  },
  mounted() {
    if (this.$auth.loggedIn){
      this.$router.push(this.localePath('/'))
    }
  },
  methods: {

    // функция логина
    async submit() {
      if (this.$refs.form.validate()) {
        try {
          const login = {
            'username': this.login,
            'password': this.password
          }
          await this.$auth.loginWith('local', { data: login })
          await this.$router.push(this.localePath('/'))
        } catch (e) {
          console.log(e)
        }
        this.error_message_pass = this.$t('login_rules_pass_err')
      }
    },
    async submit_bin(){
      if (this.$refs.form.validate()) {
        try {
          const login = {
            'username': this.login_bin,
            'password': this.password
          }
          await this.$auth.loginWith('local_bin', { data: login })
          // await this.$router.push('/')
        } catch (e) {
          console.log(e)
        }
        this.error_message_pass = this.$t('login_rules_pass_err')
      }
    },

    // проверка имени пользователя
    async checkUsername() {
      if (this.$refs.form.validate()) {
        try {
          // this.checkLogin = false
          const username = this.login

          let checker = await this.$axios.$get('api/v1/is_active/' + username + '/')
          checker = checker[0]
          if (checker) {
            this.exist = true
            if (checker.is_active === true) {
              this.active = true
            } else {
              this.active = false
            }
          } else {
            this.exist = false
            this.error_message_username = this.$t('login_rules_no_user')
            // console.log(this.exist)
          }
        } catch (e) {
          console.log(e)
          // this.checkLogin = true
        }
      }
    },
    async checkBin(){
      try {
        const username = this.login_bin

        let checker = await this.$axios.$get('api/v1/check_bin/' + username + '/')
        checker = checker[0]
        if (checker) {
          this.error_message_username = ''
          this.resetBinMessage = checker.email // зашифрованная почта
          this.hideCheckBin = true
          await this.sendCode()
          console.log(this.resetBinMessage)
        } else {
          this.error_message_username = this.$t('login_rules_no_bin')
        }
      } catch (e) {
        console.log(e)
      }
    },

    // Отправить код подтверждения на почту
    async sendCode(){
    // sendCode(){
        try {
          let username = ''
          if (this.hideCheckBin) { username = this.login_bin }
          else { username = this.login }
          // отправляем запрос на сервер, там генерируем одноразовый пароль и отправляем на почту пользователя
          await this.$axios.$post('api/v1/verification/' + username + '/')
          this.codeSent = true
        } catch (e) {
          console.log(e)
        }
    },

    // проверить код подтверждения - совпадает ли
    async checkCode(){
      if (this.$refs.form.validate()) {
        try {
          let username = ''
          if (this.hideCheckBin) { username = this.login_bin }
          else { username = this.login }

          let check = await this.$axios.$get('api/v1/verification/' + username + '/')
          check = check[0].verification
          check = parseInt(check)
          // this.emailCode = parseInt(this.emailCode)

          if (check === parseInt(this.emailCode)){
            this.validCode = true // если код правильный
            // alert('CODE SUCCESS') // если код правильный
            // проверяем одноразовый пароль, если совпадает то даем возможность ввести новый пароль
          } else {
            // если НЕ совпадает то выводим `error-messages`
            this.validCode = false // если код НЕправильный
            this.error_message_code = this.$t('login_rules_code_err')
          }
        } catch (e) {
          console.log(e)
        }
      }
      else {
        this.validCode = false
      }
    },

    // Сохранить новый пароль от аккаунта и при успехе перезагрузить страницу для логина
    async saveNewPassword(){
      if (this.$refs.form.validate()) {
        try {
          let username = ''
          if (this.hideCheckBin) { username = this.login_bin }
          else { username = this.login }

          await this.$axios.$patch('api/v1/activate/' + username + '/',{
            code: this.emailCode,
            new_password: this.password,
            new_password2: this.passwordRepeat,
          })
          // console.log(response)
          await new Promise(resolve => this.$router.go())
        } catch (e) {
          console.log(e)
          this.error_message_new_pass = e.response.data.new_password
        }
      } else {
        console.log('Not Valid')
      }
    },

    resetByEmail(){
      this.resetEmail = true
      console.log('reset by email')
    },
    resetByBin(){
      this.resetBin = true
      console.log('reset by BIN')
    },

    sendResetCode(){
      this.resetEmailCodeSent = true
      this.sendCode()

      console.log('reset code sent')
    },



  }
}
</script>

<style scoped>

</style>
