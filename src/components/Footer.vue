<template>
  <v-footer app class="main-cont">
    <v-container fluid>
      <v-row no-gutters  justify="center">
          <v-col md="auto" class="mr-3">
              <v-btn color="white" 
              class="foot-but" 
              href="https://shop.skynet-service.com/"
              min-width="300"
              elevation="0"
              >Интернет магазин</v-btn>
          </v-col>
          <v-col md="auto" >
              <v-btn 
              color="white" 
              class="foot-but"
              min-width="300"
              elevation="0"
              >Мы на карте</v-btn>
          </v-col>
      </v-row>
    </v-container>
  </v-footer>
</template>

<script>
export default {
name: "Footer",
  data: () => ({

  }),

  computed: {
    },
}
</script>

<style scoped>
  .main-cont{
    background-color: #0A74C0 !important;
    font-family: Roboto !important;
    font-style: normal !important;
    font-weight: bold !important;
    font-size: 14px !important;
    line-height: 20px !important;
    color: #FFFFFF !important;
    text-align: right!important;
    z-index: 1;
  }

  .foot-but
  {
    border: 1px solid #0A74C0 !important;
    box-sizing: border-box !important;
    border-radius: 4px !important;
    font-family: Montserrat !important;
    font-style: normal !important;
    font-weight: 600 !important;
    font-size: 14px !important;
    line-height: 20px !important;
    text-align: center !important;
    color: #0A74C0 !important;
  }

</style>