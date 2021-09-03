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
                            @exporting="onExporting"
                    >
                        <DxColumn data-field="Артикул" :width="120" cell-template="vendorСode" :allow-sorting="false"/>
                        <DxColumn data-field="Наименование" :allow-sorting="false"/>
                        <DxColumn data-field="ОПТ" :width="100" cell-template="opt" :allow-sorting="false"/>
                        <DxColumn data-field="ОПТ-Мастер" :width="100" cell-template="opt-master" :allow-sorting="false"/>
                        <DxColumn
                                :group-index="0"
                                data-field="Название группы"
                                caption=""
                                sort-order="asc"
                        />
                            
                         <template #vendorСode="{ data }">
                            <span class="vendor-code">{{data.value}}</span>
                        </template>
                        <template #opt="{ data }">
                            <span>{{data.value}} ₽</span>
                        </template>
                        <template #opt-master="{ data }">
                            <span>{{data.value}} ₽</span>
                        </template>
                        <DxGroupPanel :visible="true"/>
                        <DxGrouping :auto-expand-all="autoExpandAll"/>
                        <DxPaging :page-size="1000000"/>
                        <DxSearchPanel :visible="true"/>
                        <DxExport
                            :enabled="true"
                        />
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
                            v-if="!loading"
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
        DxExport,
    } from 'devextreme-vue/data-grid';
    import ruMessages from "devextreme/localization/messages/ru.json";
    import { locale, loadMessages } from "devextreme/localization";
    import XLSX from 'xlsx';
    import { Workbook } from 'exceljs';
    import { saveAs } from 'file-saver';
    import { exportDataGrid } from 'devextreme/excel_exporter';
    export default {
        components: {
            DxColumn,
            DxGroupPanel,
            DxGrouping,
            DxPaging,
            DxSearchPanel,
            DxDataGrid,
            ruMessages,
            locale, 
            loadMessages,
            DxExport,
        },
        name: "GeneralInformation",
        data: () => ({
            autoExpandAll: true,
            orders: [],
            loading:true,
                  saleAmountHeaderFilter: [{
        text: 'Less than $3000',
        value: ['SaleAmount', '<', 3000]
      }, {
        text: '$3000 - $5000',
        value: [
          ['SaleAmount', '>=', 3000],
          ['SaleAmount', '<', 5000]
        ]
      }, {
        text: '$5000 - $10000',
        value: [
          ['SaleAmount', '>=', 5000],
          ['SaleAmount', '<', 10000]
        ]
      }, {
        text: '$10000 - $20000',
        value: [
          ['SaleAmount', '>=', 10000],
          ['SaleAmount', '<', 20000]
        ]
      }, {
        text: 'Greater than $20000',
        value: ['SaleAmount', '>=', 20000]
      }],
        }),

        methods: {
          downloadFileFromSite () {
            this.loading=true;
            fetch('/price_copy.xls', 
            //fetch('https://skynet-service.com/price/price_copy.xls', 
            {
              method: 'GET', // *GET, POST, PUT, DELETE, etc.             
            }).then(response => response.blob()).then(blob => {
              this.fileToDevExtreme(this.blobToFile(blob, "test"))
            })
            this.loading=false;
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

      onExporting(e) {
      const workbook = new Workbook();
      const worksheet = workbook.addWorksheet('Companies');

      worksheet.columns = [
        { width: 15 }, { width: 100 }, { width: 12 }, { width: 13 }
      ];

      exportDataGrid({
        component: e.component,
        worksheet: worksheet,
        keepColumnWidths: false,
        topLeftCell: { row: 1, column: 1 },
        customizeCell: ({ gridCell, excelCell }) => {
          if(gridCell.rowType === 'data') {
            if(gridCell.column.dataField === 'Артикул') {
              excelCell.value = gridCell.value;
              excelCell.font = { color: { argb: 'B4C7DC' }, underline: false };
              excelCell.alignment = { horizontal: 'left' };
            }
            if(gridCell.column.dataField === 'ОПТ' || gridCell.column.dataField === 'ОПТ-Мастер') {
              excelCell.value = gridCell.value + ' ₽';
            }
            excelCell.border = {
                top: {style:'thin', color: {argb:'000000'}},
                left: {style:'thin', color: {argb:'000000'}},
                bottom: {style:'thin', color: {argb:'000000'}},
                right: {style:'thin', color: {argb:'000000'}}
                };
          }
          if(gridCell.rowType === 'group') {
            excelCell.value = gridCell.value;
            excelCell.fill = { type: 'pattern', pattern:'solid', fgColor: { argb: '5983B0' } };
            excelCell.font = { color: { argb: 'FFFFFF' }, bold: true};
          }
        }
      }).then(() => {
        workbook.xlsx.writeBuffer().then((buffer) => {
          saveAs(new Blob([buffer], { type: 'application/octet-stream' }), 'Скайнет прайс лист.xlsx');
        });
      });
      e.cancel = true;
    },
        },

        mounted() {
          this.downloadFileFromSite();
 
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

    .dx-datagrid-rowsview .dx-row.dx-group-row:not(.dx-row-focused) {
    color: #ffffff;
    background-color: #5983B0;
    }

    .dx-datagrid-group-opened {
        color: #ffffff;
    }

    .vendor-code{
        color:#B4C7DC;
    }

    td{
        border: 1px solid #ddd !important;
    }

    </style>
