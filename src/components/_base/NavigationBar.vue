<template>
  <b-navbar class="nav-container">
    <b-navbar-brand class="nav-brand" v-if="user.user_role === 2">
      <router-link to="/home">
        <img src="../../assets/logo/vacatechPurple.png" />
      </router-link>
    </b-navbar-brand>
    <b-navbar-brand class="nav-brand" v-else>
      <img src="../../assets/logo/vacatechPurple.png" />
    </b-navbar-brand>
    <b-navbar-nav class="ml-auto nav-item">
      <b-nav-item>
        <b-row class="iconic">
          <b-col style="padding: 0">
            <b-img
              :src="require('../../assets/icon/bell.png')"
              id="popover-target-1"
              @click="seen()"
            ></b-img>

            <b-popover
              target="popover-target-1"
              triggers="hover"
              placement="bottom"
            >
              <b-img
                :src="require('../../assets/img/notif0.png')"
                v-if="notif.notification.length < 1"
              ></b-img>
              <div
                class="notification"
                v-else
                v-for="(value, index) in notif.notification"
                :key="index"
              >
                <p>
                  {{ value.message }}
                  (<span>
                    {{
                      value.created_at.split('T').join(', ').slice(0, 17)
                    }} </span
                  >)
                </p>
              </div>
            </b-popover>
          </b-col>
          <b-col class="red-dot" v-if="notif.unseen > 0">
            <span class="dot"></span>
          </b-col>
          <b-col>
            <router-link to="/roomchat">
              <b-img :src="require('../../assets/icon/mail.png')"></b-img>
            </router-link>
          </b-col>
          <b-col>
            <b-img
              v-bind="mainProps"
              :src="url + '/' + img"
              rounded="circle"
              alt="Circle image"
              id="popover-target-2"
              >></b-img
            >
            <b-popover
              target="popover-target-2"
              triggers="hover"
              placement="bottom"
            >
              <template v-slot:title>Halo, {{ user.user_name }}</template>
              <b-navbar variant="faded" type="light">
                <router-link to="/profile" v-if="user.user_role === 1">
                  <b-navbar-brand>Profile</b-navbar-brand>
                </router-link>
                <router-link to="/profile/company" v-if="user.user_role === 2">
                  <b-navbar-brand>Profile</b-navbar-brand>
                </router-link>
              </b-navbar>
              <b-navbar variant="faded" type="light">
                <b-navbar-brand @click="confirmLogout" class="logout"
                  >Logout</b-navbar-brand
                >
              </b-navbar>
            </b-popover>
          </b-col>
        </b-row>
      </b-nav-item>
    </b-navbar-nav>
  </b-navbar>
</template>

<style scoped>
.nav-container {
  padding: 2em 2em;
  box-shadow: 0px 10px 50px rgba(132, 132, 132, 0.25);
  background-color: white;
  width: 100%;
}

.nav-brand img {
  width: 127px;
  height: 35px;
  object-fit: fill;
}

.logout {
  cursor: pointer;
}

.dot {
  height: 10px;
  width: 10px;
  background-color: red;
  border-radius: 50%;
  display: inline-block;
  position: absolute;
  top: 0;
}

.red-dot {
  padding-left: 0;
  position: relative;
}

@media screen and (max-width: 768px) {
  .nav-container .iconic {
    display: flex;
    flex-direction: row;
  }
  .nav-container .iconic img {
    margin-top: 5px;
  }
}
</style>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  name: 'NavigationBar',
  props: ['img'],
  data() {
    return {
      url: process.env.VUE_APP_BASE_URL,
      mainProps: {
        width: 32,
        height: 32,
        class: 'm1'
      },
      form: {},
      isNotif: false
    }
  },
  methods: {
    ...mapActions({
      handleLogout: 'logout',
      handleNotif: 'getNotification',
      handleSeen: 'clickNotification'
    }),
    confirmLogout() {
      this.$bvModal
        .msgBoxConfirm('Are you sure want to logout?', {
          cancelVariant: 'light',
          okVariant: 'info',
          headerClass: 'p-2 border-bottom-0',
          footerClass: 'p-2 border-top-0',
          centered: true
        })
        .then((value) => {
          this.isLogout = value
          if (this.isLogout) {
            this.handleLogout()
          }
        })
    },
    seen() {
      this.handleSeen(this.user.user_id)
    }
  },
  computed: {
    ...mapGetters({ user: 'getUser', notif: 'getNotif' })
  },
  created() {
    this.handleNotif(this.user.user_id)
  }
}
</script>
