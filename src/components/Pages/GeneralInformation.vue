<template>
    <div>
        <v-container fluid style="width:100%;">
            <!--<input @change="handleFileChange" type="file"/>-->
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
                        <DxColumn data-field="Артикул" :width="120"/>
                        <DxColumn data-field="Наименование"/>
                        <DxColumn data-field="ОПТ" :width="100"/>
                        <DxColumn data-field="ОПТ-Мастер" :width="100"/>
                        <DxColumn
                                :group-index="0"
                                data-field="Название группы"
                                caption=""
                        />

                        <DxGroupPanel :visible="true"/>
                        <DxGrouping :auto-expand-all="autoExpandAll"/>
                        <DxPaging :page-size="1000000"/>
                        <DxSearchPanel :visible="true"/>
                    </DxDataGrid>
                </v-col>
            </v-row>
            <v-row v-else>
                <v-col>
                    <v-alert
                            text
                            prominent
                            type="error"
                            icon="mdi-cloud-alert"
                    >
                        Во время загрузки данных возникла ошибка. Обновите страницу клавишей F5. Если ошибка повторится
                        обратитесь к администратору.
                    </v-alert>
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
        DxFilterRow,
    } from 'devextreme-vue/data-grid';
    import ruMessages from "devextreme/localization/messages/ru.json";
    import { locale, loadMessages } from "devextreme/localization";
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
            ruMessages,
            locale, 
            loadMessages
        },
        name: "GeneralInformation",
        data: () => ({
            autoExpandAll: true,
            orders: [],
        }),

        methods: {
          downloadFileFromSite () {
            fetch('/price_copy.xls', 
            //fetch('https://skynet-service.com/price/price_copy.xls', 
            {
              method: 'GET', // *GET, POST, PUT, DELETE, etc.             
            }).then(response => response.blob()).then(blob => {
              this.fileToDevExtreme(this.blobToFile(blob, "test"))
            })
          },

          blobToFile(theBlob, fileName){
              theBlob.lastModifiedDate = new Date();
              theBlob.name = fileName;
              return theBlob;
          },

          fileToDevExtreme(e) {
                let file = e
                if (file) {
                    var fileReader = new FileReader();
                    fileReader.onload = (event) => {
                        var data = event.target.result;

                        var workbook = XLSX.read(data, {
                            type: "binary"
                        });
                        workbook.SheetNames.forEach(sheet => {
                            let rowObject = XLSX.utils.sheet_to_row_object_array(
                                workbook.Sheets[sheet]
                            );
                            let jsonObject = JSON.stringify(rowObject, null, '\t');
                          this.orders = JSON.parse(jsonObject)
                            // document.getElementById("jsonData").innerHTML = jsonObject;
                        });
                    };
                    fileReader.readAsBinaryString(file);
                }

            },
        },

        mounted() {
          this.downloadFileFromSite()
        },

        created() {
        loadMessages(ruMessages);
        locale(navigator.language);
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
