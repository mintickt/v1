<template>
  <Background>
    <template #content>
      <!-- hero -->
      <section id="hero">
        <h1>You may want to collect these tickets</h1>
      </section>

      <div id="hero-down-img" class="eliminarmobile">
        <img src="@/assets/newLanding/hero-down.png" width="100%">
      </div>

      <!-- bullshit -->
      <section id="bullshit">
        <div class="divcol center">
          <h2>0% bullshit, 100% utility</h2>

          <v-data-table
            :headers="bullshitHeaders"
            :items="bullshitItems"
            mobile-breakpoint="-1"
            hide-default-footer
          >
            <template v-slot:[`item.compare`]="{ item }">
              <span>{{item.compare}}</span>
            </template>

            <template v-slot:[`item.old`]="{ item }">
              <img :src="require(`@/assets/newLanding/${item.old ? 'check' :'x-close'}.svg`)">
            </template>
            
            <template v-slot:[`item.our`]="{ item }">
              <img :src="require(`@/assets/newLanding/${item.our ? 'check' :'x-close'}.svg`)">
            </template>
          </v-data-table>

          <!-- <img src="@/assets/newLanding/bullshit-desktop.jpg" alt="utility image" class="eliminarmobile">
          <img src="@/assets/newLanding/bullshit-mobile.jpg" alt="utility image" class="vermobile"> -->
        </div>
      </section>

      <div id="hero-down-img" class="vermobile">
        <img src="@/assets/newLanding/hero-down.png" width="100%">
      </div>

      <!-- about -->
      <section id="about">
        <article>
          <div class="divcol">
            <h2>Hire a designer to get a custom ticket for your event...</h2>

            <v-btn :href="storeMintabe" target="_blank" class="stylish" >
              <span>More info</span>
            </v-btn>
          </div>

          <img src="@/assets/newLanding/ticket-about-up.png">
        </article>

        <article>
          <img src="@/assets/newLanding/ticket-about-down.png">

          <div class="divcol">
            <h2>... or use one of our templates to design your ticket.</h2>

            <v-btn class="stylish" @click="goToEvent">
              <span>Create event</span>
            </v-btn>
          </div>
        </article>
      </section>

      <!-- FAQ -->
      <section id="FAQ">
        <div class="divcol" style="gap: 25px">
          <h2 id="faq">FAQs</h2>
          <span class="text">Answers to the most frequently asked questions</span>
        </div>

        <v-expansion-panels v-model="expansionModel" class="expansion-faqs">
          <v-expansion-panel v-for="(item,i) in dataFaqs" :key="i">
            <v-expansion-panel-header disable-icon-rotate>
              {{item.question}}

              <template v-slot:actions>
                <v-icon>{{expansionModel === i ? 'mdi-minus' : 'mdi-plus'}}</v-icon>
              </template>
            </v-expansion-panel-header>

            <v-expansion-panel-content>{{item.answer}}</v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>

        <div class="divcol center" style="gap: 20px">
          <h2>Still have a question?</h2>
          <v-btn class="stylish" href="https://t.me/mintickt" target="_blank">
            <span>Contact Us</span>
          </v-btn>
        </div>
      </section>

      <!-- memorable -->
      <section id="memorable">
        <div class="divcol">
          <h2>Memorable events start with NFT tickets</h2>
          <v-btn class="stylish" @click="goToEvent">
            <span>Create event</span>
          </v-btn>
        </div>
      </section>

      <!-- footer -->
      <Footer></Footer>
    </template>
  </Background>
</template>

<script>
import Background from '@/components/NewLanding/Background.vue'
import Footer from '@/components/NewLanding/Footer.vue'

export default {
  name: "Landing",
  components: {Background, Footer},
  data() {
    return {
      bullshitHeaders: [
        { align: "start", value: "compare", sortable: false },
        { text: "Old tickets", align: "center", value: "old", sortable: false },
        { text: "Our tickets", align: "center", value: "our", sortable: false },
      ],
      bullshitItems: [
        {
          compare: "Verification",
          old: false,
          our: true,
        },
        {
          compare: "Proof of Ownership",
          old: false,
          our: true,
        },
        {
          compare: "Transferability",
          old: false,
          our: true,
        },
        {
          compare: "Traceability",
          old: false,
          our: true,
        },
        {
          compare: "Collector's Value",
          old: false,
          our: true,
        },
        {
          compare: "Interactive Features",
          old: false,
          our: true,
        },
        {
          compare: "Personalization",
          old: false,
          our: true,
        },
        {
          compare: "Easy Access",
          old: false,
          our: true,
        },
        {
          compare: "Integration with Digital Media",
          old: false,
          our: true,
        },
      ],
      email: undefined,
      expansionModel: undefined,
      dataFaqs: [
        {
          question: "What is an NFT ticket?",
          answer: "An NFT ticket, also known as a non-fungible token ticket, is a digital representation of a ticket using blockchain technology. Each NFT ticket is unique, verifiable, and cannot be duplicated or counterfeited. It allows for secure ownership and transferability of tickets in a digital format.",
        },
        {
          question: "How do I purchase an NFT ticket?",
          answer: "To purchase an NFT ticket, you can browse the available events on our website and select the ticket you want. Once you have made your selection, follow the instructions to complete the purchase using a compatible cryptocurrency or fiat currency. After successful purchase, the NFT ticket will be transferred to your digital wallet.",
        },
        {
          question: "How do I access my NFT ticket?",
          answer: "After purchasing an NFT ticket, it will be associated with your digital wallet. To access your NFT ticket, simply login to your wallet and navigate to the specific ticket.",
        },
        {
          question: "Can I resell my NFT ticket?",
          answer: "Yes, one of the advantages of NFT tickets is their easy transferability. You can sell your NFT ticket to another interested party by initiating a transfer from your digital wallet to the recipient's wallet. However, please note that any resale or transfer may be subject to certain terms and conditions, such as ticket restrictions or event organizer policies.",
        },
        {
          question: "Are NFT tickets secure?",
          answer: "Yes, NFT tickets are built on blockchain technology, which provides a high level of security. Each NFT ticket is uniquely identified and recorded on the blockchain, making it virtually impossible to counterfeit or manipulate. The transparent nature of the blockchain also ensures the authenticity and traceability of each ticket.",
        },

        
        {
          question: "What happens if I lose access to my digital wallet?",
          answer: "It is important to keep your digital wallet secure and backed up to prevent loss of access. If you lose access to your wallet where your NFT tickets are stored, there may be limited options for recovery. We recommend following best practices for wallet security and keeping multiple backups of your wallet's private keys or recovery phrases.",
        },
        {
          question: "Can I attend an event using a screenshot or printed copy of my NFT ticket?",
          answer: "No, attending an event using a screenshot or printed copy of your NFT ticket is not recommended. NFT tickets are designed to be digitally verified, and event organizers typically require the original digital version for entry. Presenting a counterfeit or duplicated ticket may result in denial of entry.",
        },
        {
          question: "Can I upgrade or downgrade my NFT ticket?",
          answer: "The ability to upgrade or downgrade your NFT ticket depends on the policies set by the event organizer. Some organizers may allow ticket upgrades or downgrades, while others may have specific restrictions in place. We recommend checking the event details or contacting the organizer directly to inquire about ticket modification options.",
        },
        {
          question: "What happens if an event is canceled or rescheduled?",
          answer: "In the event of a cancellation or rescheduling, the policies and procedures may vary depending on the event organizer. Typically, organizers will communicate any changes or updates directly to ticket holders. Refund or exchange options may be available based on the organizer's terms and conditions.",
        },
      ],
      storeMintabe: process.env.VUE_APP_MINTBASE_STORE
    };
  },
  mounted() {
    const queryString = window.location.search;
    const urlParams = new URLSearchParams(queryString);
    urlParams.get("transactionHashes");
    if (urlParams.get("transactionHashes") !== null) {
     history.replaceState(
        null,
        location.href.split("?")[0],
        "/#/"
      );
    }
    if (urlParams.get("errorCode") !== null) {
      history.replaceState(
        null,
        location.href.split("?")[0],
        "/#/"
      );
    }
  },
  methods: {
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
  }
};
</script>

<style src="../pages.scss" lang="scss"></style>
<style src="./Landing.scss" lang="scss"></style>
<style src="./NewLanding.scss" lang="scss"></style>
