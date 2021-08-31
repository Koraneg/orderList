<template>
  <div>
    <v-container fluid style="width:100%;">
      <v-row v-if="orders.length!==0">
        <v-col>
          <DxDataGrid
            :allow-column-reordering="true"
            :data-source="orders"
            key-expr="Код"
            :show-borders="true"
          >
          <DxFilterRow
              :visible="true"
              :apply-filter="true"
            />
            <DxHeaderFilter
              :visible="true"
            />
            <DxColumn data-field="Код"/>
            <DxColumn data-field="Артикул"/>
            <DxColumn data-field="Наименование"/>
            <DxColumn data-field="ОПТ"/>
            <DxColumn data-field="ОПТ-Мастер"/>
            <DxColumn data-field="Описание"/>
            <DxColumn
              :group-index="0"
              data-field="Название группы"
            />

            <DxGroupPanel :visible="true"/>
            <DxGrouping :auto-expand-all="autoExpandAll"/>
            <DxPaging :page-size="19"/>
            <DxSearchPanel :visible="true"/>
          </DxDataGrid>
    </v-col>
    </v-row>
    <v-row>
      <v-col>
            <v-alert
              text
              prominent
              type="error"
              icon="mdi-cloud-alert"
            >
              Во время загрузки данных возникла ошибка. Обновите страницу клавишей F5. Если ошибка повторится обратитесь к администратору.
            </v-alert>
        <XLSX />
      </v-col>
    </v-row>
    </v-container>
  </div>
</template>

<script>
import {
  DxDataGrid,
  DxColumn,
  DxGrouping,
  DxGroupPanel,
  DxSearchPanel,
  DxPaging,
  DxHeaderFilter,
  DxFilterRow
} from 'devextreme-vue/data-grid';
import XLSX from 'xlsx';
export default {
components: {
DxColumn,
DxGroupPanel,
DxGrouping,
DxPaging,
DxSearchPanel,
DxDataGrid,
DxHeaderFilter,
DxFilterRow,
XLSX
},
name: "GeneralInformation",
  data: () => ({
    autoExpandAll: true,
    orders:[],
  }),

  methods:{
     previewFiles() {
        //var url = "https://skynet-service.com/price/price_copy.xls";
        var url = "./assets/test.xlsx"; 
        var oReq = new XMLHttpRequest();
        oReq.open("GET", url, true);
        oReq.responseType = "arraybuffer";        

        oReq.onload = function() {
            var arraybuffer = oReq.response;                   

            var data = new Uint8Array(arraybuffer);
            var arr = new Array();   
            
            console.log(data)

            for (var i = 0; i != data.length; ++i) arr[i] = String.fromCharCode(data[i]);

            var bstr = arr.join("");

            console.log(bstr)

            var workbook = XLSX.read(bstr, {
                type: "binary"
            });

            var first_sheet_name = workbook.SheetNames[0];
            var worksheet = workbook.Sheets[first_sheet_name];
            console.log(XLSX.utils.sheet_to_json(worksheet, {
                raw: true
            }));
        }

        oReq.send();
       }
  },

  mounted() {
     this.previewFiles();
  }
}


</script>

<style>
   .options {
    padding: 20px;
    margin-top: 20px;
    background-color: rgba(191, 191, 191, 0.15);
    }

    .caption {
    font-size: 18px;
    font-weight: 500;
    }

    .option {
    margin-top: 10px;
    }
</style>