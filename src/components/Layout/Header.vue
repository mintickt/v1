<template>
  <section>
    <v-app-bar
      id="headerApp"
      color="transparent"
      height="140px"
      absolute
      :class="{
        events: routePath.includes('events') || routePath.includes('profile'),
        register: routePath === '/events/register',
        landing: landingList.includes(routeName),
        store: routeName == 'Store',
      }"
    >
      <v-row
        class="align"
        :class="{
          limiter: landingList.includes(routeName) || !routePath.includes('events') || !routePath.includes('profile'),
        }"
      >
        <v-col
          class="space"
          :style="
            routePath.includes('events') || routePath.includes('profile') || landingList.includes(routeName)
              ? 'padding:0'
              : 'padding-inline: clamp(1em, 4vw, 4em)'
          "
        >
          <a class="center" href="/#/">
            <img v-if="responsiveActions" class="logoHeaderEvents" src="@/assets/logo/mintickt_beta_mobile.svg" alt="logo" />
            <img v-else class="logoHeader" src="@/assets/logo/mintickt_beta.svg" alt="logo" />
          </a>

          <aside
            class="container-buttons-header center" style="gap: 20px"
            :style="`
              ${(routePath === '/events' || !user) ? '' : 'display:contents'};
              ${routeName === 'Landing' ? 'flex-direction: row-reverse' : ''}
            `"
          >
            <v-btn
              v-show="(routePath === '/events' || !user) && canShowBtn"
              class="h9-em eliminarmobile"
              :class="{
                createEventBtnLanding: landingList.includes(routeName),
                createEventBtn: routePath.includes('/events') || routePath.includes('/store')
              }"
              @click="goToEvent"
            >
              <span>create an event</span>
            </v-btn>

            <v-menu v-model="menuLogin" bottom offset-y content-class="menuButtonProfile">
              <template #activator="{ on, attrs }">
                <!-- <v-btn
                  v-if="responsiveActions"
                  color="white"
                  class="btnNearMobile h7-em"
                  v-on="nearid ? on : undefined"
                  v-bind="nearid ? attrs : undefined"
                  @click="nearid ? undefined : connect()"
                >
                  <img src="@/assets/logo/near-black.svg" alt="near logo" />
                </v-btn> -->

                <!--  atributes used before changes 👇 -->
                <!-- text
                rounded
                color="white" -->

                <v-btn
                  :class="{buttonProfile: user, login: !user}"
                  v-on="user ? on : undefined"
                  v-bind="user ? attrs : undefined"
                  @click="user ? menuOpened = !menuOpened : connectRamper()"
                  :title="user"
                >
                  <div class="center">
                    <img v-if="user" src="@/assets/logo/near.svg" alt="near" />
                    <span>{{ user || "Log In" }}</span>
                  </div>
                  <v-icon v-if="user" color="#fff" :style="menuLogin ? 'transform: rotate(180deg)' : null">mdi-chevron-down</v-icon>
                  <!-- <span><v-icon class="mr-1 ml-1" style="font-size:20px;color:orange">mdi-square-rounded</v-icon>Testnet</span> -->
                </v-btn>
              </template>

              <v-list color="transparent">
                <!-- <v-list-item v-show="responsiveActions" disabled style="background-color: #FFF !important; border-radius: 5px">
                  <v-list-item-title style="color: #000">{{user}}</v-list-item-title>
                </v-list-item> -->
                <v-list-item :to="'/profile'">
                  <v-list-item-title style="color: #fff">
                    <img src="@/assets/icons/ticket.svg" alt="profile">
                    <span>My tickets</span>
                  </v-list-item-title>
                </v-list-item>
                <v-list-item @click="$ramper.openWallet()">
                  <v-list-item-title style="color: #fff">
                    <img src="@/assets/icons/wallet.svg" alt="wallet">
                    <span>Open Wallet</span>
                  </v-list-item-title>
                </v-list-item>
                <v-list-item to="/events">
                  <v-list-item-title style="color: #fff">
                    <img src="@/assets/icons/building.svg" alt="profile">
                    <span>Events</span>
                  </v-list-item-title>
                </v-list-item>
                <!-- <v-list-item v-show="!routePath.includes('events') || (routePath === '/events/register' || routePath === '/events/select-ticket')" to="/events">
                  <v-list-item-title style="color: #fff">
                    <img src="@/assets/icons/building.svg" alt="profile">
                    <span>Events</span>
                  </v-list-item-title>
                </v-list-item>
                <v-list-item v-show="routePath.includes('events') && (routePath !== '/events/register' && routePath !== '/events/select-ticket')" @click="goToEvent()">
                  <v-list-item-title style="color: #fff">
                    <img src="@/assets/icons/building.svg" alt="profile">
                    <span>Create event</span>
                  </v-list-item-title>
                </v-list-item> -->
                <v-list-item @click="logOut">
                  <v-list-item-title style="color: #fff">
                    <img src="@/assets/icons/log-out.svg" alt="logout">
                    <span>Log out</span>
                  </v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>

            <!-- <v-btn class="createEventBtn h9-em" v-show="routePath !== '/events/register' && routeName !== 'Store' && !user" @click="goToEvent">
              <span>create an event</span>
            </v-btn> -->

            <modal-fill ref="modalfill"></modal-fill>
          </aside>
        </v-col>
      </v-row>
    </v-app-bar>
  </section>
</template>

<script>
import * as nearAPI from "near-api-js";
import modalFill from "../../pages/Store/ModalFill.vue";
const { Contract } = nearAPI;
// window.ramper.setConfig({
//   appName: "Near Test App",
//   chainName: "near",
//   defaultTokenAddresses: [],
//   theme: "dark",
//   network: "testnet",
//   authProviders: ["twitter", "google", "facebook", "apple", "email"],
// });
export default {
  name: "Header",
  components: {
    modalFill,
  },
  i18n: require("./i18n"),
  data() {
    return {
      menuLogin: false,
      user: undefined,
      canShowBtn: false,
      responsiveActions: false,
      routePath: this.$router.currentRoute.path,
      routeName: this.$router.currentRoute.name,
      landingList: [
        "Landing",
        "TermsAndConditions",
        "PrivacyPolicy",
      ],
    };
  },
  async beforeMount() {
    await this.getNearPrice();
    this.revisar();
  },
  mounted() {
    // * styles
    this.responsive();
    window.onresize = () => {
      this.responsive();
    };

    if(this.$ramper.getUser()){
      this.user = this.$ramper.getAccountId();
    }    

    const header = document.getElementById("headerApp");
    if (this.$route.path === "/events/liveData" || this.$route.path == "/events/options") {
      header.classList.add("delpaddlivedata");
    } else {
      header.classList.remove("delpaddlivedata");
    }
  },
  watch: {
    $route(current) {
      const header = document.getElementById("headerApp");
      (this.routePath = current.path), (this.routeName = current.name);
      if (current.path === "/events/liveData" || current.path == "/events/options") {
        header.classList.add("delpaddlivedata");
      } else {
        header.classList.remove("delpaddlivedata");
      }
    },
  },
  methods: {
    // async getNearPrice() {
    //   try {
    //     const nearPrice = await this.axios.get("https://api.coingecko.com/api/v3/simple/price?ids=NEAR&vs_currencies=USD");

    //     if (!nearPrice.data.near.usd) throw new Error("Error near usd");
    //     // return nearPrice.data.near.usd;
    //     this.$session.set("nearPrice", nearPrice.data.near.usd);
    //   } catch (error) {
    //     const nearPrice = await this.axios.get("https://nearblocks.io/api/near-price");
    //     if (!nearPrice.data.usd) throw new Error("Error near usd");
    //     // return nearPrice.data.usd;
    //     this.$session.set("nearPrice", nearPrice.data.usd);
    //   }
    // },
    async getNearPrice() {
      const account = await this.$near.account(this.$ramper.getAccountId());
      const contract = new Contract(account, process.env.VUE_APP_CONTRACT_NFT, {
        viewMethods: ["get_tasa"],
        sender: account,
      });

      const price = await contract.get_tasa();
      this.$session.set("nearPrice", price);
    },
    limitStr(item, num) {
      if (item) {
        if (item.length > num) {
          return item.substring(0, num) + "...";
        }
      }
      return item;
    },
    responsive() {
      if (window.innerWidth <= 880) {
        this.responsiveActions = true;
      } else {
        this.responsiveActions = false;
      }
    },
    async connectRamper() {
      if (this.$ramper.getUser()) return this.$ramper.signOut();

      const login = await this.$ramper.signIn()
     
      if (login && login.user) location.reload();

      setTimeout(() => location.reload(), 200)
      this.$router.push("/");
    },
    async revisar() {
      if(this.$ramper.getUser()){
        this.user = this.$ramper.getAccountId();
      }    
      const account = await this.$near.account(this.$ramper.getAccountId());
      if (!this.$session.get("nearSocialName")) {
        const contract = new Contract(account, process.env.VUE_APP_CONTRACT_SOCIAL, {
          viewMethods: ["get"],
          sender: account,
        });

        const myArray = [account.accountId + "/profile/**"];
        //console.log(myArray)
        const social = await contract.get({
            keys: myArray
          });
        
        Object.entries(social).forEach(([key, value]) => {
          this.$session.set("nearSocialName", value.profile.name);
          this.$session.set("nearSocialProfileImage", value.profile.image.ipfs_cid);
          if (value.profile.backgroundImage?.ipfs_cid) {
            this.$session.set("nearSocialProfileBackgroundImage", value.profile.backgroundImage.ipfs_cid);
          }
        });
      }

      setTimeout(() => {
        if (this.$session.get("nearSocialName")) {
          this.user = this.$session.get("nearSocialName")
        } else if(this.$ramper.getUser()){
          this.user = this.$ramper.getAccountId();
        }
        this.canShowBtn = true
      }, 200)
    },
    async logOut() {
      this.$ramper.signOut();
      this.$session.clear()
      setTimeout(() => location.reload(), 200)
      this.$router.push("/");
    },
    async goToEvent() {
      const balance = await this.getBalance();
      if (balance < 0.05) {
        this.$refs.modalfill.modalFill = true;
        return;
      }

      this.$session.clear();
      localStorage.setItem("step", 1);
      this.$router.push("/events/register");
    },
    async getBalance() {
      try {
        if (this.$ramper.getUser()) {
          const account = await this.$near.account(this.$ramper.getAccountId());
          const response = await account.state();
          const valueStorage = Math.pow(10, 19);
          const valueYocto = Math.pow(10, 24);

          const storage = (response.storage_usage * valueStorage) / valueYocto;
          return (response.amount / valueYocto - storage).toFixed(2);
        }
      } catch (error) {
        return "0";
      }
    },
  },
};
</script>

<style src="./layout.scss" lang="scss" />
