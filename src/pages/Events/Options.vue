<template>
  <section id="options" class="divcol gap align">
    <ModalSuccess ref="modal"></ModalSuccess>

    <div class="acenter" style="position: relative">
      <v-btn id="go-back" icon to="/events">
        <v-icon style="color: #ffffff !important">mdi-arrow-left</v-icon>
      </v-btn>
      <h2 class="p" style="margin: 0">{{ name }} / Settings</h2>
    </div>

    <aside v-if="itemTickets.id" class="container-actions divcol">
      <span>Burn a ticket</span>
      <label>(Access Control)</label>
      <div class="space">
        <v-btn @click="modalTicket = true">
          <img src="@/assets/icons/qr.svg" alt="qr icon" />
          Show QR
        </v-btn>
        <v-btn @click="copyTicket" :loading="loading_tickets">
          <img src="@/assets/icons/copy.svg" alt="copy icon" />
          {{ message_ticket }}
        </v-btn>
      </div>
    </aside>

    <aside v-if="itemGoodies.id" class="container-actions divcol">
      <span>Burn a goodie</span>
      <label>(Goodies)</label>
      <div class="space">
        <v-btn @click="modalGoodie = true">
          <img src="@/assets/icons/qr.svg" alt="qr icon" />
          Show QR
        </v-btn>
        <v-btn @click="copyGoodies" :loading="loading_goodies">
          <img src="@/assets/icons/copy.svg" alt="copy icon" />
          {{ message_goodies }}
        </v-btn>
      </div>
    </aside>

    <aside class="container-info divcol">
      <div class="space">
        <div class="divcol">
          <label>Tickets minted</label>
          <span>{{ listed }} / {{ minted }}</span>
        </div>
        <div id="container-actions" class="gap">
          <v-btn @click="modalMintMore = true">Add tickets</v-btn>
        </div>
      </div>

      <div class="space">
        <div class="divcol">
          <label>Tickets price</label>
          <span>{{ ticketPrice }} USD</span>
        </div>

        <v-btn @click="modalListMore = true">Change price</v-btn>
      </div>

      <!-- <div class="space">
        <div class="divcol"></div>
        <v-btn color="red" @click="deleteEvent()">Stop Event</v-btn>
      </div> -->
    </aside>

    <!-- modalQr -->
    <v-dialog v-model="modalQR" width="300px">
      <StreamBarcodeReader @decode="onDecode('hola')" @loaded="onLoaded"></StreamBarcodeReader>
    </v-dialog>

    <!-- modal mint more -->
    <v-dialog v-model="modalMintMore" width="300px">
      <v-card class="modalMore">
        <label for="amount">Amount</label>
        <v-text-field id="amount" v-model="mint_amount" type="number" hide-spin-buttons hide-details solo>
          <template v-slot:append>
            <v-btn color=" #C4C4C4" @click="controlAmount('less')">
              <v-icon color="black"> mdi-minus </v-icon>
            </v-btn>
            <v-btn color=" #C4C4C4" @click="controlAmount('more')">
              <v-icon color="black"> mdi-plus </v-icon>
            </v-btn>
          </template>
        </v-text-field>
        <v-btn style="margin-top: 2em" @click="addTickets()" :disabled="btnDisabled">Add tickets</v-btn>
      </v-card>
    </v-dialog>

    <!-- modal list more -->
    <v-dialog v-model="modalListMore" width="300px">
      <v-card class="modalMore divcol">
        <!-- <div class="divcol">
          <label for="amount">Amount</label>
          <v-text-field id="amount" v-model="amount_list" type="number" v-debounce:800ms="checkListAmount" hide-spin-buttons hide-details solo>
            <template v-slot:append>
              <v-btn color=" #C4C4C4" @click="controlListAmount('less')">
                <v-icon color="black"> mdi-minus </v-icon>
              </v-btn>
              <v-btn color=" #C4C4C4" @click="controlListAmount('more')">
                <v-icon color="black"> mdi-plus </v-icon>
              </v-btn>
            </template>
          </v-text-field>
        </div> -->

        <div class="divcol" style="margin-top: 2em">
          <label for="amount">Price (USD)</label>
          <v-text-field id="amount" v-model="price_list" type="number" hide-spin-buttons hide-details solo>
            <template v-slot:append>
              <v-btn color=" #C4C4C4" @click="controlPrice('less')">
                <v-icon color="black"> mdi-minus </v-icon>
              </v-btn>
              <v-btn color=" #C4C4C4" @click="controlPrice('more')">
                <v-icon color="black"> mdi-plus </v-icon>
              </v-btn>
            </template>
          </v-text-field>
          <span class="conversion">~ {{ dollarConversion(price_list) }} N</span>
        </div>
        <v-btn @click="changePrice()" :disabled="btnDisabled">Change price</v-btn>
      </v-card>
    </v-dialog>

    <!--Modal ticket Url -->
    <v-dialog v-model="modalTicket" width="420" content-class="dialogUrl">
      <v-card id="modalUrl">
        <div class="divcol center">
          <h3 class="p">Url Code</h3>
        </div>
        <center>
          <div id="my-node-ticket">
            <qr-code :text="qrtickets" error-level="L"> </qr-code>
          </div>
        </center>
        <div class="divcol center">
          <v-btn :loading="loading_tickets_qr" @click="downloadQrTicket">Download QR</v-btn>
        </div>
      </v-card>
    </v-dialog>

    <!--Modal goodie Url -->
    <v-dialog v-model="modalGoodie" width="420">
      <v-card id="modalUrl">
        <div class="divcol center">
          <h3 class="p">Url Code</h3>
        </div>
        <center>
          <div id="my-node-goodies">
            <qr-code :text="qrgoodies" error-level="L"> </qr-code>
          </div>
        </center>
        <div class="divcol center">
          <v-btn :loading="loading_goodies_qr" @click="downloadQrGoodies">Download QR</v-btn>
        </div>
      </v-card>
    </v-dialog>

    <v-dialog v-model="modalSuccess" max-width="420px">
      <v-card id="modalSucess">
        <div class="divcol center">
          <h3 class="p">Success!</h3>
          <p class="p">Your transaction was succesful.</p>
        </div>

        <div class="divcol center">
          <v-btn @click="modalSuccess = false">Ok</v-btn>
          <a class="acenter" style="gap: 0.3em" :href="urlTx" target="_blank">
            <span class="p">See transaction</span>
            <img src="@/assets/icons/transaction.svg" alt="link icon" />
          </a>
        </div>
      </v-card>
    </v-dialog>
    <div class="text-center">
      <v-overlay :value="false">
        <v-progress-circular indeterminate size="64"></v-progress-circular>
        <h3 class="mt-3">Minting in progress...</h3>
        <h3 ref="tminted">{{ show_total_minted }}</h3>
      </v-overlay>

      <v-overlay :value="false">
        <v-progress-circular indeterminate size="64"></v-progress-circular>
        <h3 class="mt-3">Building event be pacient...</h3>
      </v-overlay>
    </div>
  </section>
</template>

<script>
import { StreamBarcodeReader } from "vue-barcode-reader";
import ModalSuccess from "./ModalSuccess";
import gql from "graphql-tag";
import { Wallet, Chain } from "mintbase";
import html2canvas from "html2canvas";
import * as nearAPI from "near-api-js";

const { connect, keyStores, utils, Contract } = nearAPI;
const your_event = gql`
  query MyQuery($eventId: String!) {
    serie(id: $eventId) {
      title
      nftsold
      supply
      copies
      creator_id
      description
      expires_at
      extra
      fecha
      id
      issued_at
      media
      price
      price_near
      reference
      starts_at
      typetoken_id
      updated_at
    }
  }
`;

const eventsObjects = gql`
  query MyQuery($eventId: String!) {
    series(where: { reference: $eventId }) {
      title
      nftsold
      supply
      copies
      creator_id
      description
      expires_at
      extra
      fecha
      id
      issued_at
      media
      price
      object_event
      price_near
      reference
      starts_at
      typetoken_id
      updated_at
    }
  }
`;
const mb_views_nft_tokens_aggregate = gql`
  query MyQuery($store: String!, $user: String!, $metadata_id: String!) {
    nft_tokens_aggregate(
      where: { nft_contract_id: { _eq: $store }, metadata_id: { _eq: $metadata_id }, burned_receipt_id: { _is_null: true }, owner: { _eq: $user } }
    ) {
      aggregate {
        count
      }
    }
    nft_earnings_aggregate(where: { receiver_id: { _eq: $user }, nft_token: { metadata_id: { _eq: metadata_id } } }) {
      aggregate {
        count
      }
    }
  }
`;
const tokens_id = gql`
  query MyQuery($metadata_id: String) {
    nft_tokens_aggregate(where: { nft_contract_id: {}, metadata_id: { _eq: $metadata_id } }) {
      nodes {
        token_id
        nft_listings {
          token_id
          price
        }
      }
    }
  }
`;
const nft_tokens_aggregate = gql`
  query MyQuery($metadata_id: String!, $owner: String!) {
    nft_tokens_aggregate(where: { metadata_id: { _eq: $metadata_id }, burned_receipt_id: { _is_null: true }, owner: { _eq: $owner } }) {
      aggregate {
        count
      }
    }
  }
`;
const mb_views_nft_tokens_ticketing = gql`
  query MyQuery($metadata_id: String!) {
    mb_views_nft_tokens(where: { reference_blob: { _cast: { String: { _iregex: $metadata_id } } }, extra: { _eq: "ticketing" } }, limit: 1) {
      mint_memo
      metadata_id
      reference
      royalties
      royalties_percent
      reference_hash
      base_uri
      extra
      owner
      title
      media
    }
  }
`;
const mb_views_nft_tokens_redeemed = gql`
  query MyQuery($metadata_id: String!) {
    mb_views_nft_tokens(where: { reference_blob: { _cast: { String: { _iregex: $metadata_id } } }, extra: { _eq: "redeemed" } }, limit: 1) {
      mint_memo
      metadata_id
      reference
      royalties
      royalties_percent
      reference_hash
      base_uri
      extra
      owner
    }
  }
`;
const mb_views_nft_tokens = gql`
  query MyQuery($_iregex: String!) {
    mb_views_nft_tokens_aggregate(
      where: {
        reference_blob: { _cast: { String: { _iregex: $_iregex } } }
        burned_receipt_id: { _is_null: true }
        last_transfer_timestamp: { _is_null: true }
      }
    ) {
      aggregate {
        count
      }
    }
  }
`;
export default {
  name: "options",
  components: { StreamBarcodeReader, ModalSuccess },
  data() {
    return {
      modalSuccess: false,
      modalMintMore: false,
      modalListMore: false,
      mint_amount: 1,
      modalQR: false,
      minted: 0,
      listed: 0,
      ticketPrice: null,
      usd: 0,
      name: "",
      price_list: 0,
      amount_list: 0,
      disable: true,
      loading: false,
      loading_tickets: false,
      loading_goodies: false,
      loading_tickets_qr: false,
      loading_goodies_qr: false,
      btnDisabled: false,
      txs: [],
      counter: 0,
      message_ticket: "Copy url",
      message_goodies: "Copy url",
      modalTicket: false,
      modalGoodie: false,
      urlgoodies: this.$burn_page_ticket + "?id=",
      urltickets: this.$burn_page_ticket + "?id=",
      qrgoodies: "",
      qrtickets: "",
      show_total_minted: this.$session.get("total_minted"),
      itemTickets: {},
      itemGoodies: {},
      available_to_list: 0,
      overlay: false,
      new_minted: 0,
      overlay_building: false,
      eventId: null,
      nearPrice: 0,
      urlTx: "",
    };
  },
  async mounted() {
    if (!this.$session.exists()) {
      this.$session.start();
    }

    this.eventId = this.$session.get("event_id_op");
    await this.getNearPrice();
    this.getData();
    // this.getTotalMinted();
  },
  methods: {
    dollarConversion(price) {
      return (Number(price) / this.nearPrice || 0).toFixed(4);
    },
    async getNearPrice() {
      const account = await this.$near.account(this.$ramper.getAccountId());
      const contract = new Contract(account, process.env.VUE_APP_CONTRACT_NFT, {
        viewMethods: ["get_tasa"],
        sender: account,
      });

      const price = await contract.get_tasa();
      this.nearPrice = price || 0;
    },
    async addTickets() {
      this.btnDisabled = true;
      if (this.$ramper.getUser()) {
        const action = [
          this.$ramper.functionCall(
            "update_nft_event",
            {
              token_event_id: this.tokenId,
              copies: this.mint_amount,
              is_mintable: true,
            },
            "300000000000000",
            "1"
          ),
        ];

        const resTx = await this.$ramper.sendTransaction({
          transactionActions: [
            {
              receiverId: process.env.VUE_APP_CONTRACT_NFT,
              actions: action,
            },
          ],
          network: process.env.VUE_APP_NETWORK,
        });
        console.log(res);
        this.btnDisabled = false;
        this.mint_amount = false;
        if ((resTx &&
          JSON.parse(localStorage.getItem('ramper_loggedInUser'))
            .signupSource === 'near_wallet' &&
            resTx.txHashes.length > 0) || (resTx.result || resTx.result[0]?.status?.SuccessValue || resTx.result[0]?.status?.SuccessValue === "")) {
          this.modalMintMore = false;
          if (process.env.VUE_APP_NETWORK === "mainnet") {
            this.urlTx = "https://explorer.near.org/transactions/" + res.txHashes[0];
          } else {
            this.urlTx = "https://explorer.testnet.near.org/transactions/" + res.txHashes[0];
          }
          this.modalSuccess = true;
        } else {
          console.log("ERRROR", res);
        }
      }
      this.btnDisabled = false;
    },
    async changePrice() {
      this.btnDisabled = true;
      if (this.$ramper.getUser()) {
        const action = [
          this.$ramper.functionCall(
            "update_nft_event",
            {
              token_event_id: this.tokenId,
              price: Number(this.price_list),
            },
            "300000000000000",
            "1"
          ),
        ];

        const resTx = await this.$ramper.sendTransaction({
          transactionActions: [
            {
              receiverId: process.env.VUE_APP_CONTRACT_NFT,
              actions: action,
            },
          ],
          network: process.env.VUE_APP_NETWORK,
        });
        console.log(res);
        this.btnDisabled = false;
        this.price_list = 0;
        if ((resTx &&
          JSON.parse(localStorage.getItem('ramper_loggedInUser'))
            .signupSource === 'near_wallet' &&
            resTx.txHashes.length > 0) || (resTx.result || resTx.result[0]?.status?.SuccessValue || resTx.result[0]?.status?.SuccessValue === "")) {
          this.modalListMore = false;
          if (process.env.VUE_APP_NETWORK === "mainnet") {
            this.urlTx = "https://explorer.near.org/transactions/" + res.txHashes[0];
          } else {
            this.urlTx = "https://explorer.testnet.near.org/transactions/" + res.txHashes[0];
          }
          this.modalSuccess = true;
        } else {
          console.log("ERRROR", res);
        }
      }
      this.btnDisabled = false;
    },
    async deleteEvent() {
      this.btnDisabled = true;
      if (this.$ramper.getUser()) {
        const action = [
          this.$ramper.functionCall(
            "update_nft_event",
            {
              token_event_id: this.tokenId,
              is_mintable: false,
            },
            "300000000000000",
            "1"
          ),
        ];

        const resTx = await this.$ramper.sendTransaction({
          transactionActions: [
            {
              receiverId: process.env.VUE_APP_CONTRACT_NFT,
              actions: action,
            },
          ],
          network: process.env.VUE_APP_NETWORK,
        });
        console.log(res);
        this.btnDisabled = false;
        if (res.result && typeof res.result[0]?.status?.SuccessValue === "string") {
          this.$session.set("hashSuccess", res.txHashes[0]);
          this.$router.push("/events");
        } else {
          console.log("ERRROR", res);
        }
      }
      this.btnDisabled = false;
    },
    onDecode(text) {
      console.log(`Decode text from QR code is ${text}`);
    },
    onLoaded() {
      console.log(`Ready to start scanning barcodes`);
    },
    ModalQR(key) {
      if (key == "ticket") {
        this.modalQR = true;
      }
      if (key == "goodie") {
        this.modalQR = true;
      }
    },
    async getData() {
      const user = this.$ramper.getAccountId();
      this.$apollo
        .watchQuery({
          query: your_event,
          variables: {
            eventId: this.eventId,
          },
          pollInterval: 3000, // 10 seconds in milliseconds
        })
        .subscribe(({ data }) => {
          const dataSerie = data.serie;

          this.tokenId = dataSerie.id;
          this.name = dataSerie.title;
          this.minted = dataSerie.copies;
          this.listed = dataSerie.supply;
          this.ticketPrice = dataSerie.price;

          this.available_to_list = this.minted - this.listed;

          this.$apollo
            .watchQuery({
              query: eventsObjects,
              variables: {
                eventId: this.eventId,
              },
              pollInterval: 3000, // 10 seconds in milliseconds
            })
            .subscribe((response) => {
              const dataEvents = response.data.series;

              for (let i = 0; i < dataEvents.length; i++) {
                if (dataEvents[i].object_event) {
                  this.itemTickets = dataEvents[i];
                  this.qrtickets = this.urltickets + this.itemTickets.id;
                } else if (dataEvents[i].object_event === false) {
                  this.itemGoodies = dataEvents[i];
                  this.qrgoodies = this.urlgoodies + this.itemGoodies.id;
                }
              }
            });
        });
      this.loading = false;
    },
    async getData2() {
      let datos = JSON.parse(localStorage.getItem("Mintbase.js_wallet_auth_key"));
      const user = datos.accountId;
      this.$apollo
        .watchQuery({
          query: your_events,
          variables: {
            store: this.$store_mintbase,
            user: user,
            metadata_id: this.$route.query.thingid.toLowerCase(),
          },
          pollInterval: 3000, // 10 seconds in milliseconds
        })
        .subscribe(({ data }) => {
          //Map the objectvalue
          Object.entries(data).forEach(([key, value]) => {
            // inner object entries
            Object.entries(value).forEach(([i, value1]) => {
              //Getting the minted nft
              //Tokens aggregate and earnings by metadata id
              this.$apollo
                .watchQuery({
                  query: mb_views_nft_tokens_aggregate,
                  variables: {
                    store: this.$store_mintbase,
                    user: user,
                    metadata_id: this.$route.query.thingid.toLowerCase(),
                  },
                  pollInterval: 3000, // 10 seconds in milliseconds
                })
                .subscribe(({ data }) => {
                  (this.name = this.$route.query.event),
                    (this.minted = data.nft_tokens_aggregate.aggregate.count),
                    (this.listed = value1.listings_aggregate.aggregate.count);
                  this.available_to_list = this.minted - this.listed;
                });
            });
          });
        });
      this.loading = false;
    },
    async mint() {
      this.loading = true;
      //Api key an data
      let API_KEY = this.$dev_key.toString();
      let networkName = this.$networkName.toString();
      const { data: walletData } = await new Wallet().init({
        networkName: networkName,
        chain: Chain.near,
        apiKey: API_KEY,
      });
      // console.log(this.mint_amount)
      // console.log(this.$route.query.thingid.toLowerCase())
      const { wallet } = walletData;
      //console.log(wallet)
      //localStorage.setItem('total_minted', this.mint_amount);
      const { data, error } = await wallet.mintMore(parseInt(this.mint_amount), this.$route.query.thingid.toLowerCase());
      console.log("error", error);
      console.log("data", data);
    },
    //Get the tokens id minted
    async list() {
      this.loading = true;
      const mintbase_marketplace = this.$mintbase_marketplace;
      let store = this.$store_mintbase;
      let datos = JSON.parse(localStorage.getItem("Mintbase.js_wallet_auth_key"));
      const user = datos.accountId;
      this.$apollo
        .query({
          query: tokens_id,
          variables: {
            metadata_id: this.$route.query.thingid.toLowerCase(),
          },
        })
        .then((response) => {
          //Firts call storage deposit
          console.log(this.amount_list);
          this.txs.push({
            receiverId: mintbase_marketplace,
            functionCalls: [
              {
                methodName: "deposit_storage",
                receiverId: mintbase_marketplace,
                gas: "200000000000000",
                args: {},
                deposit: utils.format.parseNearAmount((0.0108 * this.amount_list).toString()),
              },
            ],
          });
          var actual_counter = this.$session.get("control_mint_appoval");
          this.$session.get("control_mint_appoval", actual_counter);
          //Map the object value to be listed
          let counter = 0;
          Object.entries(response.data).forEach(([key, value]) => {
            Object.entries(value.nodes).forEach(([i, value1]) => {
              if (value1.nft_listings.length === 0) {
                if (counter < this.amount_list) {
                  //console.log(value1.token_id);
                  counter++;
                  // console.log(counter + '-' + this.amount_list)
                  // console.log(value1.token_id);
                  this.txs.push({
                    receiverId: store,
                    functionCalls: [
                      {
                        methodName: "nft_approve",
                        receiverId: store,
                        gas: "200000000000000",
                        args: {
                          token_id: value1.token_id.toString(),
                          account_id: mintbase_marketplace,
                          msg: JSON.stringify({
                            price: utils.format.parseNearAmount(this.price_list.toString()),
                            autotransfer: true,
                          }),
                        },
                        deposit: utils.format.parseNearAmount((0.0008 * this.amount_list).toString()),
                      },
                    ],
                  });
                }
              }
            });
          });
        })
        .catch((err) => {
          console.log("Error", err);
        });
      //Mint ticketing
      // this.$apollo
      //   .query({
      //     query: mb_views_nft_tokens_ticketing,
      //     variables: {
      //       metadata_id: localStorage.getItem("metadata_id").split(":")[1],
      //     },
      //   })
      //   .then((response) => {
      //     //Map the object value to be listed
      //     Object.entries(response.data).forEach(([key, value]) => {
      //       const owners = {};
      //       owners[value[0].owner] = 9000;
      //       owners[this.$value_user_mint] = 1000;
      //       this.txs.push({
      //         receiverId: store,
      //         functionCalls: [
      //           {
      //             methodName: "nft_batch_mint",
      //             receiverId: store,
      //             gas: "200000000000000",
      //             args: {
      //               owner_id: value[0].owner,
      //               metadata: {
      //                 reference: value[0].reference,
      //                 extra: value[0].mint_memo,
      //               },
      //               num_to_mint: parseInt(this.amount_list),
      //               royalty_args: null,
      //               split_owners: owners,
      //             },
      //             deposit: "1",
      //           },
      //         ],
      //       });
      //     });
      //   })
      //   .catch((err) => {
      //     console.log("Error", err);
      //   });
      //Mint redeemed
      // this.$apollo
      //   .query({
      //     query: mb_views_nft_tokens_redeemed,
      //     variables: {
      //       metadata_id: localStorage.getItem("metadata_id").split(":")[1],
      //     },
      //   })
      //   .then((response) => {
      //     //Map the object value to be listed
      //     Object.entries(response.data).forEach(([key, value]) => {
      //       const owners = {};
      //       owners[value[0].owner] = 9000;
      //       owners[this.$value_user_mint] = 1000;
      //       this.txs.push({
      //         receiverId: store,
      //         functionCalls: [
      //           {
      //             methodName: "nft_batch_mint",
      //             receiverId: store,
      //             gas: "200000000000000",
      //             args: {
      //               owner_id: value[0].owner,
      //               metadata: {
      //                 reference: value[0].reference,
      //                 extra: value[0].mint_memo,
      //               },
      //               num_to_mint: parseInt(this.amount_list),
      //               royalty_args: null,
      //               split_owners: owners,
      //             },
      //             deposit: "1",
      //           },
      //         ],
      //       });
      //     });
      //   })
      // .catch((err) => {
      //   console.log("Error", err);
      // });
      this.new_minted = parseInt(this.$session.get("total_minted")) + parseInt(this.amount_list);
      this.$session.set("new_minted", this.new_minted);
      this.executeMultipleTransactions();
    },
    async executeMultipleTransactions() {
      //Gettintg the tokens ID
      //this.getTokensId();
      //Adding metadata for the burn ticket
      let API_KEY = this.$dev_key.toString();
      let networkName = this.$networkName.toString();
      const { data: walletData } = await new Wallet().init({
        networkName: networkName,
        chain: Chain.near,
        apiKey: API_KEY,
      });
      const { wallet } = walletData;

      await wallet.executeMultipleTransactions({
        transactions: this.txs,
        options: {
          meta: "list",
        },
      });
    },
    controlAmount(item) {
      this.getData();
      if (item == "more") {
        this.mint_amount++;
      }
      if (item == "less" && this.mint_amount > 1) {
        this.mint_amount--;
      }
    },
    controlListAmount(item) {
      this.getData();
      if (item == "more" && this.amount_list < this.available_to_list) {
        this.amount_list++;
      }
      if (item == "less" && this.amount_list > 1) {
        this.amount_list--;
      }
    },
    controlPrice(item) {
      this.getData();
      if (item == "more") {
        this.price_list++;
      }
      if (item == "less" && this.price_list > 1) {
        this.price_list--;
      }
      this.price_list === 0 ? (this.disable = true) : (this.disable = false);
      //this.priceNEAR();
    },
    nearToYocto: function (nearToYocto) {
      const amountInYocto = utils.format.parseNearAmount(nearToYocto);
      // console.log(amountInYocto);
      return amountInYocto.toString();
    },
    priceNEAR() {
      //console.log('ededed')
      const BINANCE_NEAR = this.$binance;
      var request = new XMLHttpRequest();
      request.open("GET", BINANCE_NEAR);
      request.send();
      this.disable = false;
      request.onload = () => {
        this.usd = (parseFloat(JSON.parse(request.responseText).lastPrice) * this.price_list).toFixed(2);
      };
    },
    copyTicket() {
      this.loading_tickets = true;
      if (this.tokenId) {
        this.$copyText(this.urltickets + this.itemTickets.id).then(
          function (e) {
            console.log(e);
          },
          function (e) {
            console.log(e);
          }
        );
        this.message_ticket = "Copied!";
        setTimeout(() => {
          this.message_ticket = "Copy url";
        }, 2000);
      }
      this.loading_tickets = false;
      this.$forceUpdate();
    },
    copyGoodies() {
      this.loading_goodies = true;
      if (this.tokenId) {
        this.$copyText(this.urlgoodies + this.itemGoodies.id).then(
          function (e) {
            console.log(e);
          },
          function (e) {
            console.log(e);
          }
        );
        this.message_goodies = "Copied!";
        setTimeout(() => {
          this.message_goodies = "Copy url";
        }, 2000);
      }
      this.loading_goodies = false;
      this.$forceUpdate();
    },
    downloadQrTicket() {
      this.loading_tickets_qr = true;
      var container = document.getElementById("my-node-ticket"); /* full page */
      html2canvas(container, {
        height: 450,
        y: -80,
      }).then((canvas) => {
        let link = document.createElement("a");
        link.download = "url_ticket.png";
        link.href = canvas.toDataURL("image/png", 1.0);
        document.body.appendChild(link);
        link.click();
        this.loading_tickets_qr = false;
      });
    },
    downloadQrGoodies() {
      this.loading_goodies_qr = true;
      var container = document.getElementById("my-node-goodies"); /* full page */
      html2canvas(container, {
        height: 450,
        y: -80,
      }).then((canvas) => {
        let link = document.createElement("a");
        link.download = "url_goodies.png";
        link.href = canvas.toDataURL("image/png", 1.0);
        document.body.appendChild(link);
        link.click();
        this.loading_goodies_qr = false;
      });
    },
    checkMintAmount() {
      this.mint_amount > 20 ? (this.mint_amount = 20) : (this.mint_amount = this.mint_amount);
    },
    checkListAmount() {
      var total_minted = this.available_to_list;
      this.amount_list > total_minted ? (this.amount_list = total_minted) : (this.amount_list = this.amount_list);
    },
    async getTotalMinted() {
      this.$apollo
        .watchQuery({
          query: mb_views_nft_tokens,
          variables: {
            _iregex: this.$route.query.thingid.toLowerCase().split(":")[1],
          },
          pollInterval: 3000, // 10 seconds in milliseconds
        })
        .subscribe(({ data }) => {
          var counter = data.mb_views_nft_tokens_aggregate.aggregate.count;
          this.$session.set("total_minted", counter);
        });
    },
  },
};
</script>

<style src="../pages.scss" lang="scss" />
