<template>
  <section id="landing" class="closed">
    <div class="hero wf-section">
      <div data-w-id="2220b184-5210-3094-780d-86fc54edc368" class="hero-div">
        <div class="div-block-6">
          <div class="div-block-5">
            <h1 class="hero-title">Memorable</h1>
            <h1 class="hero-title"><span class="hero_title_animation">conventions</span></h1>
          </div>
          <h1 class="hero-title">bringing you to NFT world</h1>
        </div>
        <div class="div-block-7">
          <a href="https://twitter.com/mintickt" target="_blank" class="social_link w-inline-block"><img src="@/assets/landing/Social-media-logo.svg" loading="lazy" alt="" class="image-5"></a>
          <a href="https://discord.gg/Evk3KMYefX" target="_blank" class="social_link w-inline-block"><img src="@/assets/landing/Logo-Mark-White.svg" loading="lazy" alt="" class="image-6"></a>
        </div>
      </div>
    </div>
    <!-- <div data-w-id="00ea4e38-8cc5-6392-e081-bdfca1d8e146" class="just-in-case">
      <h3 class="heading-boxes">Just in case</h3>
      <div class="faq w-container">
        <div class="w-layout-grid grid">
          <div id="w-node-_025dfed6-58ad-fe90-5cb0-2aaa98f28bf4-99945a8b" class="boxxx box">
            <div class="box-wrapper"><img src="@/assets/landing/Question.svg" loading="lazy" width="30" alt="" class="image">
              <h3 class="title-boxes">What is a NFT?</h3>
              <p class="paragraph">NFTs (Non Fungible Tokens) are digital items that you own. The proof of ownership is stored on a blockchain, which is a publicly accessible digital database.</p>
            </div>
          </div>
          <div id="w-node-_246488a9-ce31-d2c8-80cb-f915f7aaf453-99945a8b" class="boxxx box">
            <div class="box-wrapper"><img src="@/assets/landing/settings.svg" loading="lazy" width="30" alt="" class="image">
              <h3 class="title-boxes">How does an NFT work?</h3>
              <p class="paragraph">The fungibility of crypto makes it a reliable medium for transacting on the blockchain. Each has a digital signature that makes it impossible for NFTs to be exchanged or be the same as each other (hence, non-fungible).</p>
            </div>
          </div>
          <div id="w-node-_81b6e30c-39ef-2d79-4534-98304c026850-99945a8b" class="boxxx box">
            <div class="box-wrapper"><img src="@/assets/landing/rock.svg" loading="lazy" width="30" alt="" class="image">
              <h3 class="title-boxes">Why NFT Ticketing?</h3>
              <p class="paragraph">Prevent fake tickets and scam, reduce costs, quick production (NFT tickets minted in minutes), perpetual revenue (royalties) and add new revenue opportunities like food or drink deals, pre sale of merchandising and even rewarding fans who have gathered many event tickets. </p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div data-w-id="e756a33a-1b3e-1b5b-3631-23a5688bf0e2" class="just-in-case how-u">
      <h3 class="heading-boxes">How you benefit</h3>
      <div class="faq w-container">
        <div class="w-layout-grid grid">
          <div id="w-node-e756a33a-1b3e-1b5b-3631-23a5688bf0e7-99945a8b" class="boxxx box">
            <div class="box-wrapper"><img src="@/assets/landing/Workflow-Teammate-Circle.svg" loading="lazy" width="30" alt="" class="image">
              <h3 class="title-boxes">Promoters</h3>
              <p class="paragraph">Say goodbye to fake tickets and scam and say hello to less costs and gain control over the secondary market with earning royalties on secondary sales.</p>
            </div>
          </div>
          <div id="w-node-e756a33a-1b3e-1b5b-3631-23a5688bf0ee-99945a8b" class="boxxx box">
            <div class="box-wrapper"><img src="@/assets/landing/Ticket.svg" loading="lazy" width="30" alt="" class="image">
              <h3 class="title-boxes">Attendees</h3>
              <p class="paragraph">Collecting tickets never made as much sense as it does now, now thanks to NFT&#x27;s you can prove you were there, earn rewards (someone said airdrops?) or even connect with your favorite artist away from the noise of social media on our Discord.</p>
            </div>
          </div>
          <div id="w-node-e756a33a-1b3e-1b5b-3631-23a5688bf0f5-99945a8b" class="boxxx box">
            <div class="box-wrapper"><img src="@/assets/landing/Professions-Man-Artist.svg" loading="lazy" width="30" alt="" class="image">
              <h3 class="title-boxes">Digital artists</h3>
              <p class="paragraph">In the last few years the digital artists community has given the NFT world a shake up with hundreds of PFP projects. Why not design event ticket templates? You will make money every time a template designed by you is sold. You may even be hired to design a custom ticket.</p>
            </div>
          </div>
        </div>
      </div>
    </div> -->

    <section class="divcol" data-w-id="00ea4e38-8cc5-6392-e081-bdfca1d8e146">
      <h2 class="h2 likeRegister">Or hire someone.</h2>

      <div class="slider" ref="slider" @mousemove.prevent="onDragging" @mousedown="startDragging" @mouseup="stopDragging" @mouseleave="stopDragging">
        <v-card v-for="(item, i) in dataSlider" :key="i">
          <img :src="item.headerImg" alt="slider-card-header-image">

          <aside class="slider__content">
            <span class="slider__content-title">{{item.title}}</span>

            <div style="display: flex">
              <img :src="item.avatar" alt="avatar">
              <span class="avatar-name">{{item.name}}</span>
            </div>

            <ul>
              <li v-for="(item2, i) in item.description" :key="i">{{item2}}</li>
            </ul>

            <div class="space">
              <div class="divcol">
                <span class="from-text">From</span>
                <span class="from-price">{{item.price ? `$${item.price}USD` : ''}}</span>
              </div>

              <v-btn>
                More info
              </v-btn>
            </div>
          </aside>
        </v-card>
      </div>
    </section>

    <section class="container-steps" data-w-id="e756a33a-1b3e-1b5b-3631-23a5688bf0e2">
      <v-card class="step-cards">
        <span class="h2">Fill the webform</span>
        <p>Fill the webform with all the details of your event including location, pricing and even drink deals or pre sale for your merch.</p>
      </v-card>
      
      <v-card class="step-cards">
        <span class="h2">Create your event</span>
        <p>Fill the webform with all the details of your gig.</p>
      </v-card>
    </section>
  </section>
</template>

<script>
export default {
  name: "Landing",
  data() {
    return {
      dataSlider: [
        {
          headerImg: require("@/assets/landing/zombie-image-landing.jpg"),
          title: "I will design a custom ticket design for your event.",
          avatar: require("@/assets/landing/avatar.svg"),
          name: "Pintamones",
          description: [
            "Unique and 100% original illustration",
            "Source file (300 DPI - AI and PSD file)",
            "All in common format such as JPG / PNG",
            "Revision",
            "Commercial use",
          ],
          price: 300,
        },
        {
          headerImg: require("@/assets/landing/zombie-image-landing.jpg"),
          title: "I will design a custom ticket design for your event.",
          avatar: require("@/assets/landing/avatar.svg"),
          name: "Pintamones",
          description: [
            "Unique and 100% original illustration",
            "Source file (300 DPI - AI and PSD file)",
            "All in common format such as JPG / PNG",
            "Revision",
            "Commercial use",
          ],
          price: 300,
        },
        {
          headerImg: require("@/assets/landing/zombie-image-landing.jpg"),
          title: "I will design a custom ticket design for your event.",
          avatar: require("@/assets/landing/avatar.svg"),
          name: "Pintamones",
          description: [
            "Unique and 100% original illustration",
            "Source file (300 DPI - AI and PSD file)",
            "All in common format such as JPG / PNG",
            "Revision",
            "Commercial use",
          ],
          price: 300,
        },
        {
          headerImg: require("@/assets/landing/zombie-image-landing.jpg"),
          title: "I will design a custom ticket design for your event.",
          avatar: require("@/assets/landing/avatar.svg"),
          name: "Pintamones",
          description: [
            "Unique and 100% original illustration",
            "Source file (300 DPI - AI and PSD file)",
            "All in common format such as JPG / PNG",
            "Revision",
            "Commercial use",
          ],
          price: 300,
        },
        {
          headerImg: require("@/assets/landing/zombie-image-landing.jpg"),
          title: "I will design a custom ticket design for your event.",
          avatar: require("@/assets/landing/avatar.svg"),
          name: "Pintamones",
          description: [
            "Unique and 100% original illustration",
            "Source file (300 DPI - AI and PSD file)",
            "All in common format such as JPG / PNG",
            "Revision",
            "Commercial use",
          ],
          price: 300,
        },
      ],
      slider: 0,
      mouseDown: false,
      startX: undefined,
      scrollLeft: undefined,
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
    startDragging(e) {
      this.mouseDown = true;
      this.startX = e.pageX - this.$refs.slider.offsetLeft;
      this.scrollLeft = this.$refs.slider.scrollLeft;
      console.log("start");
    },
    stopDragging() {
      this.mouseDown = false;
      console.log("stop");
    },
    onDragging(e) {
      if(!this.mouseDown) { return; }
      const x = e.pageX - this.$refs.slider.offsetLeft;
      const scroll = x - this.startX;
      this.$refs.slider.scrollLeft = this.scrollLeft - scroll;
      console.log("dragging");
    }
  }
};
</script>

<style src="../pages.scss" lang="scss"></style>
<style src="./Landing.scss" lang="scss"></style>
<style src="./NewLanding.scss" lang="scss"></style>
