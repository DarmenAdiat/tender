<template>
  <v-container style='max-width: 1280px;'>
    <v-row>
      <v-col
        cols='12'
        sm='12'
      >
        <v-sheet
          min-height='70vh'
          rounded='lg'
          align='center'
          style='background-color: transparent!important'
        >
          <!--  -->

          <h2 style='padding: 10px;'>{{ $t('tenders') }} ISKER Group</h2>
          <h4 style='padding: 10px;margin-top: 0;padding-top: 0'>{{ $t('entered_as') }}: {{ user.full_name }}</h4>
          <v-container fluid>
            <v-row dense>
              <v-col>
                <v-card
                  outlined
                  style='margin-bottom: 30px;'
                >
                  <v-row style='margin: 0'>
                    <v-col
                      cols='8'
                      sm='8'
                      md='8'
                      lg='8'
                    >
                      <v-text-field
                        v-model='search'
                        :label="$t('search')"
                        :placeholder="$t('search_by')"
                        prepend-icon='mdi-magnify'
                        clearable
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols='4'
                      sm='4'
                      md='4'
                      lg='4'
                    >
                      <v-btn-toggle
                        v-model='filterCards'
                        style='margin: 10px auto;'
                        mandatory
                      >
                        <v-btn
                          width='125px'
                          :disabled='loaded_active'
                        >
                          {{ $t('rfq_active') }}
                        </v-btn>

                        <v-btn
                          width='165px'
                          :disabled='loaded_overdue'
                        >
                          {{ $t('rfq_closed') }}
                        </v-btn>
                        <v-btn
                          width='85px'
                          :disabled='loaded_all'
                        >
                          {{ $t('rfq_all') }}
                        </v-btn>
                      </v-btn-toggle>
                    </v-col>
                  </v-row>
                </v-card>

                <v-card
                  v-for='(item, i) in items'
                  :key='i'
                  outlined
                  style='margin-bottom: 30px;'
                  :disabled='item.state === "Declined"'
                >
                  <v-row style='margin: 5px'>
                    <v-col cols='7' sm='7' md='7' lg='7'>

                      <v-row style='margin: 0'>
                        <v-card-title style='padding-top: 0;padding-bottom: 0' class='col-6'>
                          {{ item.rfq }}
                        </v-card-title>
                      </v-row>
                      <v-row style='margin: 0'>
                        <v-card-subtitle align='left'>
                          {{ item.title }}
                        </v-card-subtitle>
                      </v-row>

                      <v-row style='margin: 0'>

                        <v-col cols='4' style='padding: 0'>
                          <v-card-text align='left' style='padding: 7px 15px'>{{ $t('start_date') }}: {{ item.started_at }}
                          </v-card-text>
                        </v-col>
                        <v-col cols='4' style='padding: 0'>
                          <v-card-text align='right' style='padding: 7px 15px'>{{ $t('end_date') }}: {{ item.closed_at }}
                          </v-card-text>
                        </v-col>

                      </v-row>

                    </v-col>
                    <v-col cols='5' sm='5' md='5' lg='5' style='padding: 0;display: flex;'>

                      <v-row style='align-items: baseline; flex-wrap: initial; margin: 2px'>
                        <v-chip
                          v-if='item.viewed'
                          style='margin: 10px;align-items: center;width:140px;'
                          color='gray'
                          label
                          outlined
                        >
                          {{ $t('rfq_in_viewed') }}
                        </v-chip>
                        <v-chip
                          v-else
                          style='margin: 10px;align-items: center;width:140px;'
                          color='gray'
                          label
                          outlined
                        >
                          {{ $t('rfq_in_not_viewed') }}
                        </v-chip>

                        <v-chip
                          v-if="item.state === 'Accepted'"
                          style='margin: 2px auto;width:130px;'
                          color='gray'
                          label
                          outlined
                        >
                          {{ $t('rfq_in_accepted') }}
                        </v-chip>
                        <v-chip
                          v-if="item.state === 'Declined'"
                          style='margin: 2px auto;width:130px;'
                          color='red'
                          label
                          outlined
                        >
                          {{ $t('rfq_in_declined') }}
                        </v-chip>
                        <v-chip
                          v-if="item.state === 'Ignored' || item.viewed === false"
                          style='margin: 2px auto;width:130px;'
                          color='gray'
                          label
                          outlined
                        >
                          {{ $t('rfq_in_ignored') }}
                        </v-chip>
                        <v-chip
                          v-if="item.state === 'Sent'"
                          style='margin: 2px auto;width:130px;'
                          color='green'
                          label
                          outlined
                        >
                          {{ $t('rfq_in_sent') }}
                        </v-chip>
                        <v-card-actions v-if='item.is_overdue'>
                          <v-chip
                            style='width:100%;min-width: 143px;'
                            color='gray'
                            label
                            outlined
                          >{{ $t('rfq_status') }}: {{ $t('rfq_status_closed') }}
                          </v-chip>
                          <v-btn icon :title="$t('add_favorites')" style='margin: 0 10px;'>
                            <v-icon>mdi-bookmark</v-icon>
                          </v-btn>
                        </v-card-actions>
                        <v-card-actions v-if='!item.is_overdue'>
                          <v-chip
                            v-if='item.status === "Активный"'
                            style='width:100%;min-width: 143px;'
                            color='green'
                            label
                            outlined
                          >{{ $t('rfq_status') }}: {{ $t('rfq_status_active') }}
                          </v-chip>
                          <v-chip
                            v-if='item.status === "Черновик"'
                            style='margin: 2px 10px;width:100%;min-width: 143px;'
                            color='blue'
                            label
                          >{{ $t('rfq_status') }}: {{ $t('rfq_status_draft') }}
                          </v-chip>
                          <v-btn icon :title="$t('add_favorites')" style='margin: 0 10px;'>
                            <v-icon>mdi-bookmark</v-icon>
                          </v-btn>
                        </v-card-actions>
                      </v-row>

                      <v-row style='margin: 0; width:190px; position: absolute; bottom: 10px; right: 15px;'>
                        <v-card-actions style='width: 100%; padding: 8px 0'>
                          <v-btn
                            style='width: 100%'
                            :to='localePath(`/card/${item.id}`)'
                          >
                            {{ $t('rfq_open') }}
                          </v-btn>
                        </v-card-actions>
                      </v-row>


                    </v-col>
                  </v-row>


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
import moment from 'moment'
import Vue from 'vue'
import VueGtag from 'vue-gtag'

(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
    new Date().getTime(),event:'gtm.js'});const f=d.getElementsByTagName(s)[0];
  const j=d.createElement(s); const dl=l!=='dataLayer'?'&l='+l:'';j.async=true;j.src=
    'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-TQKBM8D');



export default {
  modules: [
    '@nuxtjs/gtm'
  ],
  gtm: {
    id: 'GTM-TQKBM8D',
  },
  name: 'Main',
  middleware: 'auth',
  data() {
    return {
      search: '',
      filterCards: 0, // 0 => active; 1 => overdue; 2 => all
      loading: true,
      items: [],
      itemsLoaded: [],
      refreshUser: [],
      loaded_active: false,
      loaded_overdue: false,
      loaded_all: false,
      link: ''
    }
  },
  head() {
    return {
      title: this.$t('title_main')
    }
  },
  computed: {
    user() {
      return this.$auth.user
    },
    UserForGoogle() {
      return {
        iin: this.$auth.user.iin,
        full_name: this.$auth.user.full_name,
        name: this.$auth.user.email
      }
    },
    // postGoogle(){
    //   window.dataLayer = window.dataLayer || [];
    //   window.dataLayer.push({
    //     'event': 'login',
    //     'user_id': this.UserForGoogle.iin,
    //     'full_name_id': this.UserForGoogle.full_name,
    //     'email_id': this.UserForGoogle.name
    //   })
    //   return true
    // },
    admin() {
      return this.user !== null ? this.user.is_staff : false
    },
  },
  watch: {
    filterCards() {
      // this.getAccessedCards()
      this.getAccessedCards_new()
      // console.log(this.filterCards)
      // - отфильтрованные карточки тендеров
    },
    search() {
      this.searchByTitle(this.search)
    }
  },

  mounted() {
    // this.getAccessedCards()
    this.postGoogle()
    this.getAccessedCards_new()
    this.getLink()
  },
  created() {

  },
  methods: {
    postGoogle(){
      window.dataLayer = window.dataLayer || [];
      window.dataLayer.push({
        'event': 'login',
        'user_idnum': this.UserForGoogle.iin,
        'full_name_id': this.UserForGoogle.full_name,
        'email_id': this.UserForGoogle.name
      })
      Vue.use(VueGtag, {
        config: {
          id: 'G-DZ1JLGSCHD',
          event: 'login',
          // params:{
          //   'cid': this.UserForGoogle.iin,
          //   'sid': this.UserForGoogle.full_name,
          //   // 'email_id': this.UserForGoogle.name
          // }
        }
      })
      return true
    },
    searchByTitle(value) {
      if (value) {
        this.items = this.itemsLoaded.filter(function(item) {
          return item.rfq.toLowerCase().includes(value.toLowerCase()) || item.title.toLowerCase().includes(value.toLowerCase())
        })
      } else {
        this.items = this.itemsLoaded
      }

    },


    async getAccessedCards_new() {
      this.search = ''
      this.items = []
      this.itemsLoaded = []

      // console.log(this.UserForGoogle.iin)
      // console.log(this.UserForGoogle.full_name)
      // console.log(this.UserForGoogle.name)

      await this.refreshAccessedAndViewed() // обновляем состояние тендеров
      const states = await this.getState() // Список состояний тендеров rfq(id) / state
      const rfq = this.refreshUser.user.received // список идентификаторов доступных RFQ
      const viewedRFQ = this.refreshUser.user.viewed // список идентификаторов просмотренных RFQ

      // console.log(rfq)

      let data = []
      try {
        data = await this.$axios.$get('/api/v1/catalog/')
      } catch (e) {
        console.log(e)
      }
      if (data) {
        for (let i = 0; i < data.length; i++) {
          const receivedCard = data[i]
          // console.log(receivedCard.id)
          // if user is admin give him all tenders
          if (this.admin) {
            // COPY INCLUDES WITHOUT IF HERE
            if (receivedCard.started_at === null) {
              receivedCard.started_at = this.$t('rfq_no_data')
            } else {
              receivedCard.started_at = moment(receivedCard.started_at).format('DD.MM.yyyy')
            }
            if (receivedCard.closed_at === null) {
              receivedCard.closed_at = this.$t('rfq_no_data')
            } else {
              receivedCard.closed_at = moment(receivedCard.closed_at).format('DD.MM.yyyy')
            }
            if ((receivedCard.is_overdue) && (receivedCard.status !== 'Завершен')) {
              await this.$axios.$patch('/api/v1/card/' + receivedCard.id + '/', {
                status: 'Завершен'
              })
              receivedCard.status = 'Завершен'
            }

            if (this.filterCards === 0) {
              if (receivedCard.status === 'Активный') {
                receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
                // Добавляем в массив имеющиеся состояния тендеров
                for (let j = 0; j < states.length; j++) {
                  if (states[j].rfq === receivedCard.id) {
                    receivedCard.state = states[j].state
                  }
                }
                this.items.push(receivedCard)
              }
            }
            else if (this.filterCards === 1) {
              if (receivedCard.status === 'Завершен') {
                receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
                // Добавляем в массив имеющиеся состояния тендеров
                for (let j = 0; j < states.length; j++) {
                  if (states[j].rfq === receivedCard.id) {
                    receivedCard.state = states[j].state
                  }
                }

                this.items.push(receivedCard)
              }
            }
            else {
              receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
              // Добавляем в массив имеющиеся состояния тендеров
              for (let j = 0; j < states.length; j++) {
                if (states[j].rfq === receivedCard.id) {
                  receivedCard.state = states[j].state
                }
              }
              this.items.push(receivedCard)
            }

          }
          else if ((!this.admin) && (rfq.includes(receivedCard.id))) {
            // PASTE HERE INCLUDES WITH IF
            // проверять есть ли receivedCard.id тендера в rfq(списке доступных)
            // if (rfq.includes(receivedCard.id)){

            if (receivedCard.started_at === null) {
              receivedCard.started_at = this.$t('rfq_no_data')
            } else {
              receivedCard.started_at = moment(receivedCard.started_at).format('DD.MM.yyyy')
            }
            if (receivedCard.closed_at === null) {
              receivedCard.closed_at = this.$t('rfq_no_data')
            } else {
              receivedCard.closed_at = moment(receivedCard.closed_at).format('DD.MM.yyyy')
            }
            if ((receivedCard.is_overdue) && (receivedCard.status !== 'Завершен')) {
              await this.$axios.$patch('/api/v1/card/' + receivedCard.id + '/', {
                status: 'Завершен'
              })
              receivedCard.status = 'Завершен'
            }

            if (this.filterCards === 0) {
              if (receivedCard.status === 'Активный') {
                receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
                // Добавляем в массив имеющиеся состояния тендеров
                for (let j = 0; j < states.length; j++) {
                  if (states[j].rfq === receivedCard.id) {
                    receivedCard.state = states[j].state
                  }
                }
                this.items.push(receivedCard)
              }
            }
            else if (this.filterCards === 1) {
              if (receivedCard.status === 'Завершен') {
                receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
                // Добавляем в массив имеющиеся состояния тендеров
                for (let j = 0; j < states.length; j++) {
                  if (states[j].rfq === receivedCard.id) {
                    receivedCard.state = states[j].state
                  }
                }

                this.items.push(receivedCard)
              }
            }
            else {
              receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
              // Добавляем в массив имеющиеся состояния тендеров
              for (let j = 0; j < states.length; j++) {
                if (states[j].rfq === receivedCard.id) {
                  receivedCard.state = states[j].state
                }
              }
              this.items.push(receivedCard)
            }

            // }
          }



        }
        this.itemsLoaded = this.items
      }
    },

    async getAccessedCards() {
      this.search = ''
      this.items = []
      this.itemsLoaded = []

      await this.refreshAccessedAndViewed() // обновляем состояние тендеров
      const states = await this.getState() // Список состояний тендеров rfq(id) / state
      const rfq = this.refreshUser.user.received // список идентификаторов доступных RFQ
      const viewedRFQ = this.refreshUser.user.viewed // список идентификаторов просмотренных RFQ

      for (let i = 0; i < rfq.length; i++) {

        const receivedCard = await this.getCards(rfq[i])

        if (this.filterCards === 0) {
          // this.loaded_overdue = true
          // this.loaded_all = true

          if (receivedCard.status === 'Активный') {
            receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
            // Добавляем в массив имеющиеся состояния тендеров
            for (let j = 0; j < states.length; j++) {
              if (states[j].rfq === receivedCard.id) {
                receivedCard.state = states[j].state
              }
            }

            this.items.push(receivedCard)
          }
          // setTimeout(() => {
          //   this.loaded_all = false
          //   this.loaded_overdue = false
          // }, 500)

        } else if (this.filterCards === 1) {
          // this.loaded_active = true
          // this.loaded_all = true

          if (receivedCard.status === 'Завершен') {
            receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
            // Добавляем в массив имеющиеся состояния тендеров
            for (let j = 0; j < states.length; j++) {
              if (states[j].rfq === receivedCard.id) {
                receivedCard.state = states[j].state
              }
            }

            this.items.push(receivedCard)
          }
          // setTimeout(() => {
          //   this.loaded_active = false
          //   this.loaded_all = false
          // }, 500)

        } else {
          // this.loaded_active = true
          // this.loaded_overdue = true

          receivedCard.viewed = viewedRFQ.includes(receivedCard.id)
          // Добавляем в массив имеющиеся состояния тендеров
          for (let j = 0; j < states.length; j++) {
            if (states[j].rfq === receivedCard.id) {
              receivedCard.state = states[j].state
            }
          }

          this.items.push(receivedCard)

          // setTimeout(() => {
          //   this.loaded_active = false
          //   this.loaded_overdue = false
          // }, 500)

        }
      }
      this.itemsLoaded = this.items
    },

    async getCards(rfq) {
      this.loading = true
      this.errored = false
      let data = []
      try {
        data = await this.$axios.$get('/api/v1/catalog/' + rfq + '/')
      } catch (e) {
        console.log(e)
      }
      if (data) {
        if (data.started_at === null) {
          data.started_at = this.$t('rfq_no_data')
        } else {
          // data.started_at =  moment(data.started_at).format('HH:mm - DD.MM.yyyy')
          data.started_at = moment(data.started_at).format('DD.MM.yyyy')
        }
        if (data.closed_at === null) {
          data.closed_at = this.$t('rfq_no_data')
        } else {
          // data.closed_at =  moment(data.closed_at).format('HH:mm - DD.MM.yyyy')
          data.closed_at = moment(data.closed_at).format('DD.MM.yyyy')
        }

        if ((data.is_overdue) && (data.status !== 'Завершен')) {
          await this.$axios.$patch('/api/v1/card/' + rfq + '/', {
            status: 'Завершен'
          })
          await this.getCards(rfq)
        }
      }
      return data
    },


    async getState() {
      // Сделать выборку по идентификатору пользователя
      const data = await this.$axios.$get('/api/v1/tender_state/')
        .catch((e) => {
          console.log(e)
          this.errored = true
        })
        .finally(() => {

        })
      if (!data) return
      return data
    },

    async refreshAccessedAndViewed() {
      const refresh = await this.$axios.$get('/api/v1/profile/')
      this.refreshUser = refresh
    },

    updateViewed() {
      // const thisViewed = [2]
      // await this.$axios.$patch('/api/v1/viewed/' + this.user.id + '/',{
      //   viewed: thisViewed
      // })
      alert('CHECK')
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
