<template>
  <div>
  <v-app-bar app class="main-cont">
      <v-container fluid style="width:100%; margin-left:-30px; margin-right:-30px;">
        <v-row no-gutters>
          <v-col md="auto">
              <v-btn
                class="mr-2 white--text"
                fab
                elevation="0"
                style="background-color: #0353B4 !important;"
                href="https://shop.skynet-service.com/"
              >
                <v-icon x-large>
                  mdi-arrow-left-thin-circle-outline
                </v-icon>
              </v-btn>
          </v-col>
          <v-col md="auto">
              <v-row  no-gutters class="mt-1">
                <v-col>
                  <p class="logo-text mb-0">Скайнет</p>
                </v-col>
              </v-row>
              <v-row  no-gutters>
                <v-col>
                  <p class="sub-logo mb-0">прайс лист компании</p>
                </v-col>
              </v-row>
          </v-col>      
        </v-row>
      </v-container>
  </v-app-bar>
  </div>
</template>

<script>
export default {
  name: "Main",

  data: () => ({

  }),

  methods: {
  },

  watch: {

  },
}
</script>

<style scoped>
  .main-cont{
    background-color: #0353B4 !important;
    font-family: Roboto !important;
    font-style: normal !important;
    font-weight: bold !important;
    font-size: 14px !important;
    line-height: 20px !important;
    color: #FFFFFF !important;
    text-align: right;
  }

  .logo-text{
    text-align: left !important;
    font-family: Roboto !important;
    font-style: normal !important;
    font-weight: 900 !important;
    font-size: 23px !important;
    line-height: 27px !important;
    letter-spacing: 0.36em !important;
    color: #FFFFFF !important;
  }

  .sub-logo{
    font-family: Roboto !important;
    font-style: normal !important;
    font-weight: bold !important;
    font-size: 14px !important;
    line-height: 16px !important;
    color: #FFFFFF !important;
    text-align: left !important;
  }
</style>