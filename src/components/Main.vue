<template>
  <div>
  <v-app-bar app class="main-cont" 
      absolute 
      hide-on-scroll 
      scroll-target="#scrolling-techniques-4">
      <v-container>
        <v-row no-gutters>
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
    background-color: #0A74C0 !important;
    font-family: Montserrat !important;
    font-style: normal !important;
    font-weight: bold !important;
    font-size: 14px !important;
    line-height: 20px !important;
    color: #FFFFFF !important;
    text-align: right;
  }

  .logo-text{
    text-align: left !important;
    font-family: Montserrat !important;
    font-style: normal !important;
    font-weight: 900 !important;
    font-size: 23px !important;
    line-height: 27px !important;
    letter-spacing: 0.36em !important;
    color: #FFFFFF !important;
  }

  .sub-logo{
    font-family: Montserrat !important;
    font-style: normal !important;
    font-weight: bold !important;
    font-size: 14px !important;
    line-height: 16px !important;
    color: #FFFFFF !important;
    text-align: left !important;
  }
</style>