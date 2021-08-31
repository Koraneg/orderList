<template>
  <v-footer app class="main-cont">
    <v-spacer></v-spacer>
     <a cass="foter-a d-inline-flex justify-end" href="https://mmis.ru/" target="_blank">Разработано ООО "Лаборатория ММИС"</a>
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
    background-color: #0353B4 !important;
    font-family: Roboto !important;
    font-style: normal !important;
    font-weight: bold !important;
    font-size: 14px !important;
    line-height: 20px !important;
    color: #FFFFFF !important;
    text-align: right!important;
  }

  a{
    text-decoration: none !important;
    color: #FFFFFF !important;
  }
</style>