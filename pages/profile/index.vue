<template>
  <v-container style='max-width: 1280px;'>
    <v-row>
      <v-col
        cols='12'
        sm='12'
      >
        <v-sheet
          min-height='70vh'
          align='center'
          style='background-color: transparent!important'
        >
          <v-tabs
            v-model='tab'
            align='center'
            centered
            background-color='transparent'
            color='black'
            style='-webkit-text-fill-color: #00695C'
          >
            <v-tabs-slider color='#00695C'></v-tabs-slider>
            <v-tab
              v-for='item in items'
              :key='item'
              style='font-size: large'>
              {{ item }}
            </v-tab>

          </v-tabs>
          <v-tabs-items v-model='tab' style='background-color: transparent'>
            <v-tab-item>
              <v-card style='border-radius: 20px' outlined>
                <v-container fluid style='border-radius: 20px'>
                  <v-row dense>
                    <v-col>

                      <v-row style='margin: 20px;'>
                        <v-col
                          cols='6'
                          sm='6'
                          md='6'
                          lg='6'
                          style='text-align: left;padding: 0 10px'
                        >

                        </v-col>
                        <v-col
                          cols='6'
                          sm='6'
                          md='6'
                          lg='6'
                          style='text-align: right;padding: 0 10px'
                        >
                          <h3>Статус:
                            <span
                              v-if='userInfo.agent_status === "Approved"'
                              style='color: green'
                            >
                          {{ $t('profile_status_approved') }}
                        </span>
                            <span
                              v-else
                              style='color: green'
                            >
                          {{ $t('profile_status_not_approved') }}
                        </span>
                          </h3>
                        </v-col>
                      </v-row>
                      <v-row style='margin: 10px 20px;padding: 10px;'>
                        <heig>
                          <v-row
                            no-gutters

                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>
                                {{ $t('profile_contractor') }}: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.full_name }}</afterheig>
                            </v-col>
                          </v-row>
                          <v-divider></v-divider>
                          <v-row
                            no-gutters
                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>БИН: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.iin }}</afterheig>
                            </v-col>
                          </v-row>
                          <v-divider></v-divider>
                          <v-row
                            no-gutters
                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>
                                {{ $t('profile_email') }}: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.email }}</afterheig>
                            </v-col>
                          </v-row>
                          <v-divider></v-divider>
                          <v-row
                            no-gutters
                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>
                                {{ $t('profile_phone') }}: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.phone }}</afterheig>
                            </v-col>
                          </v-row>
                          <v-divider></v-divider>
                          <v-row
                            no-gutters
                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>
                                {{ $t('profile_website') }}: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.web_site }}</afterheig>
                            </v-col>
                          </v-row>
                          <v-divider></v-divider>
                          <v-row
                            no-gutters
                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>
                                {{ $t('profile_actual_addr') }}: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.actual_address }}</afterheig>
                            </v-col>
                          </v-row>
                          <v-divider></v-divider>
                          <v-row
                            no-gutters
                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>
                                {{ $t('profile_legal_addr') }}: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.legal_address }}</afterheig>
                            </v-col>
                          </v-row>
                          <v-divider></v-divider>
                          <v-row
                            no-gutters
                          >
                            <v-col md='3'>
                              <h4 style='width: 100%;text-align: left; color:#414141; padding-left: 10px'>
                                {{ $t('profile_manager') }}: </h4>
                            </v-col>
                            <v-divider vertical></v-divider>
                            <v-col>
                              <afterheig>{{ userInfo.general_manager }}</afterheig>
                            </v-col>
                          </v-row>
                        </heig>
                      </v-row>

                    </v-col>
                  </v-row>
                </v-container>
              </v-card>

            </v-tab-item>
            <v-tab-item>
              <v-card rounded outlined>
                <v-container fluid>
                  <v-row style='margin: 20px;padding: 10px'>
                    <v-data-table
                      :headers='bank_details_headers'
                      :items='bank_details'
                      class='elevation-1'
                      style='width: 100%'
                      disable-sort
                      hide-default-footer
                      :items-per-page='-1'
                    >
                      <template #[`item.index`]='{ item }'>
                        {{ bank_details.indexOf(item) + 1 }}
                      </template>
                    </v-data-table>
                  </v-row>
                </v-container>
              </v-card>
            </v-tab-item>
            <v-tab-item>
              <v-card rounded outlined>
                <v-container fluid>
                  <v-row style='margin: 20px;padding: 10px'>
                    <v-data-table
                      :headers='contracts_headers'
                      :items='contracts'
                      class='elevation-1'
                      style='width: 100%'
                      disable-sort
                      hide-default-footer
                      :items-per-page='-1'
                    >
                      <template #[`item.index`]='{ item }'>
                        {{ contracts.indexOf(item) + 1 }}
                      </template>
                    </v-data-table>
                  </v-row>
                </v-container>
              </v-card>
            </v-tab-item>
            <v-tab-item>
              <v-card outlined rounded>
                <v-container fluid>
                  <v-row style='margin: 20px;padding: 10px'>
                    <v-data-table
                      :headers='contacts_headers'
                      :items='contacts'
                      class='elevation-1'
                      style='width: 100%'
                      disable-sort
                      hide-default-footer
                      :items-per-page='-1'
                    >
                      <template #[`item.index`]='{ item }'>
                        {{ contacts.indexOf(item) + 1 }}
                      </template>
                    </v-data-table>
                  </v-row>
                </v-container>
              </v-card>
            </v-tab-item>
          </v-tabs-items>
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
// import users from '~/pages/admin/users'

export default {
  name: 'Profile',
  data() {
    return {
      tab: null,
      items: [
        this.$t('profile_tabs_info'), this.$t('profile_tabs_bank'), this.$t('profile_tabs_contracts'), this.$t('profile_tabs_contacts')
      ],
      access_categories: false,
      groupsCurrent: [],
      userInfo: [],
      bank_details_headers: [
        { text: '#', value: 'index', class: '', cellClass: '' },
        { text: this.$t('profile_table_bank'), value: 'name', class: '', cellClass: '' },
        { text: this.$t('profile_table_currency'), value: 'currency', class: '', cellClass: '' },
        { text: this.$t('profile_table_account'), value: 'acc_number', class: '', cellClass: '' },
        { text: this.$t('profile_table_bank_name'), value: 'bank_name', class: '', cellClass: '' },
        { text: this.$t('profile_table_bik'), value: 'bik', class: '', cellClass: '' }
      ],
      contracts_headers: [
        { text: '#', value: 'index', class: '', cellClass: '' },
        { text: this.$t('profile_table_number'), value: 'num', class: '', cellClass: '' },
        { text: this.$t('profile_table_name'), value: 'name', class: '', cellClass: '' },
        { text: this.$t('profile_table_state'), value: 'state', class: '', cellClass: '' },
        { text: this.$t('profile_table_from'), value: 'start_date', class: '', cellClass: '' },
        { text: this.$t('profile_table_to'), value: 'end_date', class: '', cellClass: '' },
        { text: this.$t('profile_table_pay_currency'), value: 'currency', class: '', cellClass: '' }
      ],
      contacts_headers: [
        { text: '#', value: 'index', class: '', cellClass: '' },
        { text: this.$t('profile_table_FIO'), value: 'name', class: '', cellClass: '' },
        { text: this.$t('profile_table_pos'), value: 'position', class: '', cellClass: '' },
        { text: this.$t('profile_table_phone'), value: 'phone', class: '', cellClass: '' },
        { text: this.$t('profile_table_email'), value: 'email', class: '', cellClass: '' }
      ],
      bank_details: [],
      contracts: [],
      contacts: [],
      link: ''
    }
  },
  head: {
    title: 'Profile'
  },
  computed: {
    user() {
      return this.$auth.user
    }
  },
  mounted() {
    this.loadGroups() // Загрузка функций страницы
    this.loadUserInfo()
  },
  methods: {
    addCategory() {
      alert('MODAL OPEN')
    },

    // Загрузка всех имеющихся категорий для контрагента
    async loadGroups() {
      // const getGroups = await this.$axios.get(this.link + 'GetPositionGroupList', {
      //   credentials: true,
      //   auth: {
      //     username: 'web',
      //     password: 'web'
      //   },
      // })
      // const mainGroups = 0
      // console.log(mainGroups)
      // console.log(getGroups)
    },

    // Получаем информацию о контрагенте по отдельному адресу
    async loadUserInfo() {
      try {
        const data = await this.$axios.$get('/api/v1/user_info')
        this.userInfo = data[0]

        this.bank_details = JSON.parse(this.userInfo.bank_details)
        this.contracts = JSON.parse(this.userInfo.contracts)
        this.contacts = JSON.parse(this.userInfo.contacts)


        console.log(this.userInfo.agent_status)
      } catch (e) {
        console.log(e)
      }
      // api/v1/user_info/jumperfox2@mail.ru/

      console.log('user information received')
    },
    async getLink() {
      // this.link +
      const ERP = await this.$axios.$get('api/v1/link/')
      this.link = ERP[0].link
    }

  }
}
</script>

<style scoped>

</style>
