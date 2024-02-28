<template>
  <v-footer
    padless
    app
  >
    <v-btn
      class='info'
      style='position:absolute;bottom: 100px;right: 10px;'
      @click='connectUs = true'
    >
      {{ $t('footer_feedback') }}
    </v-btn>
    <v-btn
      class='info'
      style='position:absolute;bottom: 50px;right: 10px;'
      @click='openInstructions'
    >
      {{ $t('footer_instructions') }}
    </v-btn>
    <v-card class="flex" flat tile :elevation="10">
      <v-card-text class="py-2 text-center">
        &copy; ISKER Group {{ new Date().getFullYear() }}. All rights reserved. <u>Privacy Policy</u>
      </v-card-text>
    </v-card>

    <v-dialog
      v-model='connectUs'
      max-width='600'
    >
      <v-card
        :loading='loaderConnectUs'
      >
        <v-card-text style='text-align:center;padding: 24px 20px;color: black'>
          <h2 style='margin:5px 5px 20px 5px;'>{{ $t('footer_feedback_title') }}<br>{{ $t('footer_feedback_title2') }}</h2>
          <v-form
            ref='form'
            v-model='valid'
            @submit.prevent="sendConnectUs"
          >
          <h3 style='margin:5px 10px; text-align: left'>
            {{ $t('footer_feedback_email') }}:
          </h3>
          <v-text-field
            v-model='guest_email'
            :rules='emailRules'
            style='margin: 0 10px;'
            label='Email'
            :placeholder="$t('footer_feedback_email_ph')"
            type='email'
            autocomplete='email'
            autofocus
            outlined
            dense
          ></v-text-field>
            <h3 style='margin:0 10px 10px 10px; text-align: left'>
              {{ $t('footer_feedback_name') }}:
            </h3>
            <v-text-field
              v-model='guest_names'
              :rules='subjectRules'
              style='margin: 0 10px'
              :label="$t('footer_feedback_name_label')"
              :placeholder="$t('footer_feedback_name_ph')"
              type='text'
              outlined
              dense
            ></v-text-field>
            <h3 style='margin:0 10px 10px 10px; text-align: left'>
              {{ $t('footer_feedback_phone') }}:
            </h3>
            <v-text-field
              v-model='guest_phone'
              :rules='subjectRules'
              style='margin: 0 10px'
              :label="$t('footer_feedback_phone_label')"
              :placeholder="$t('footer_feedback_phone_ph')"
              type='text'
              outlined
              dense
            ></v-text-field>

          <h3 style='margin:0 10px 10px 10px; text-align: left'>
            {{ $t('footer_feedback_rfq') }}:
          </h3>
          <v-text-field
            v-model='guest_subject'
            :rules='subjectRules'
            style='margin: 0 10px'
            :label="$t('footer_feedback_rfq_label')"
            :placeholder="$t('footer_feedback_rfq_ph')"
            type='text'
            outlined
            dense
          ></v-text-field>

          <h3 style='margin:0 10px 10px 10px; text-align: left'>
            {{ $t('footer_feedback_text') }}:
          </h3>
          <v-textarea
            v-model='guest_text'
            :rules='textRules'
            style='margin: 0 10px'
            :label="$t('footer_feedback_text_label')"
            :placeholder="$t('footer_feedback_text_ph')"
            type='text'
            outlined
            auto-grow
            rows='4'
          ></v-textarea>


          <v-btn
            class='refusal white--text'
            width='150px'
            style='margin-right: 135px;'
            @click='connectUs = false'
          >
            <v-icon>mdi-close</v-icon>
            &nbsp;{{ $t('cancel') }}
          </v-btn>
          <v-btn
            class='confirmation white--text'
            :loading='loaderConnectUs'
            width='250px'
            type='submit'
          >
            {{ $t('send') }}
            <v-icon>mdi-check</v-icon>
          </v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-snackbar
      v-model='snack'
      :timeout='3000'
      :color='snackColor'
    >
      {{ snackText }}
      <template #action='{ attrs }'>
        <v-btn
          v-bind='attrs'
          class='white--text'
          text
          @click='snack = false'
        >
          {{ $t('close') }}
        </v-btn>
      </template>
    </v-snackbar>

    <v-dialog
      v-model='instructions'
      max-width='800'
    >
      <v-card
        class='mx-auto'
        tile
      >
        <v-card-text style='text-align:center;padding: 24px 20px;'>
          <h2>{{ $t('footer_instructions') }}</h2>

          <v-list>
            <v-list-item-group
              color='primary'
            >
              <v-list-item
                v-for='(item, i) in instructionList'
                :key='i'
                :href='item.file'
                target='_blank'
              >
                <v-list-item-icon>
                  <v-icon>mdi-file</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title
                    style='text-align: left'
                    v-text='item.name'
                  ></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card-text>
        <v-card-actions class='justify-end'>
          <v-btn
            text
            @click='instructions = false'
          >{{ $t('close') }}
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

  </v-footer>
</template>

<script>
export default {
  name: "Footer",
  data() {
    return {
      valid: false,
      connectUs: false, // форма обратной связи
      instructions: false, // окно с инструкциями
      guest_email: '',
      guest_subject: '',
      guest_text: '',

      guest_names: '',
      guest_phone: '',

      instructionList: [],
      emailRules: [
        v => !!v || this.$t('rules_email'),
        v => /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || this.$t('rules_email_err'),
      ],
      subjectRules: [
        v => !!v || this.$t('rules_empty'),
        v => v && v.length <= 150 || this.$t('rules_max150'),
      ],
      textRules: [
        v => !!v || this.$t('rules_empty'),
        v => v && v.length <= 1000 || this.$t('rules_max1000'),
      ],

      loaderConnectUs: false,
      snack: false,
      snackColor: '',
      snackText: '',
    }},

  methods: {
    async sendConnectUs(){
      if (this.$refs.form.validate()) {
        this.loaderConnectUs = true
        try {
          await this.$axios.$post('/api/v1/contact_us/', {
            email: this.guest_email,
            subject: this.guest_subject,
            text: this.guest_text,
            names: this.guest_names,
            phone: this.guest_phone,
          })
          this.connectUs = false
          this.snack = true
          this.snackColor = 'confirmation white--text'
          this.snackText = this.$t('footer_feedback_sent')
          this.$refs.form.reset()
        } catch (e) {
          this.connectUs = false
          this.snack = true
          this.snackColor = 'refusal white--text'
          this.snackText = this.$t('footer_feedback_err')
        }
        this.loaderConnectUs = false
      }
    },

    async openInstructions(){
      const itemList = await this.$axios.$get('/api/v1/instructions/')
      if (itemList) {
        console.log(itemList)
        this.instructionList = itemList
        this.instructions = true
      }
    },
  },
}
</script>

<style scoped>

</style>
