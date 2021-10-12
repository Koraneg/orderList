<template>
    <div>
        <v-container fluid style="width:100%;">
            <v-row justify="center">
                <v-dialog
                v-model="loading"
                overlay-opacity="1"
                overlay-color="#fff"
                >
                <v-card class="load-card">
                            <v-progress-circular
                            :size="100"
                            color="primary"
                            indeterminate
                            ></v-progress-circular>
                </v-card>
                </v-dialog>
            </v-row>
            
            <v-row v-if="listOfCities.length!==0" no-gutters>
                <v-col>
                    <v-row no-gutters>                       
                        <v-col md="auto" class="mr-3 mt-2 select-text">
                            Выберите город:
                        </v-col>
                        <v-col md="auto" style="min-width:300px;" class="py-2" >                         
                            <v-select
                                :items="listOfCities"
                                solo
                                @change="downloadFileFromSite"
                                dense
                                hide-details
                                v-model="city"
                                label="Ростов"
                            >
                               <template v-slot:selection="data">
                                    {{ data.item.Сity}}
                                </template>
                                <template v-slot:item="data" >
                                    <v-list-item-content v-text="data.item.Сity" style="color: #000 !important;background-color: #fff;background: #fff;  caret-color: #fff !important;"></v-list-item-content>                                                 
                                </template>
                            </v-select>
                        </v-col>
                        <v-spacer class="no-spacer"></v-spacer>
                        <v-col md="auto">
                            <div class="dx-button-mode-contained-copy">Скачать прайс лист</div>
                        </v-col>
                    </v-row>
                </v-col>
            </v-row>
            <v-row>
                <v-col style="min-width:300px;">
                   <!-- <v-autocomplete
                        v-model="model"
                        :items="orders"                  
                        hide-no-data
                        hide-selected
                        item-text="Наименование"                      
                        prepend-icon="mdi-database-search"
                        return-object
                        hide-details
                        solo
                        clearable
                        @change="changeValue"
                         @keydown="keyPressEvent"
                         @click:clear="clearMass"
                         @click="rebornMassForClick"
                        ></v-autocomplete>-->
                        <v-text-field
                            label="Введите строку для поиска"
                            solo
                            v-model="search"
                            hide-no-data
                            hide-details
                            @keydown="enterPress"
                        ></v-text-field>
                </v-col>
                <v-col md="auto" class="px-1">
                    <v-btn
                        outlined
                        color="#0353B4"
                        class="mt-2"
                        @click="filterMass"
                    >
                    <v-icon left>
                        fas fa-search
                    </v-icon>
                    Поиск
                    </v-btn>
                </v-col>
                <v-col md="auto" class="px-1 pr-3">
                    <v-btn
                        color="#0353B4"
                        outlined
                        class="mt-2"
                        @click="clearMass"
                    >
                    <v-icon left>
                        far fa-times-circle
                    </v-icon>
                    Очистить
                    </v-btn>
                </v-col>
            </v-row>
            <v-row v-if="orders.length!==0" class="no-margin">
                <v-col>
                    
                    <DxDataGrid
                            :allow-column-reordering="true"
                            :data-source="orders"
                            key-expr="Код"
                            :show-borders="true"
                            @exporting="onExporting"
                    >
                        <DxColumn data-field="Артикул" :width="120" cell-template="vendorСode" :allow-sorting="false" :hiding-priority="1"/>
                        <DxColumn data-field="Наименование" :allow-sorting="false" :hiding-priority="4"  />
                        <DxColumn data-field="ОПТ" :width="100" cell-template="opt" :allow-sorting="false" :hiding-priority="3"/>
                        <DxColumn data-field="ОПТ-Мастер" :width="100" cell-template="opt-master" :allow-sorting="false" :hiding-priority="2"/>
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
                        <DxSearchPanel :visible="false"/>
                        <DxExport
                            :enabled="true"
                        />
                    </DxDataGrid>
                </v-col>
            </v-row>
            <v-row v-if="orders.length===0 && !loading">
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
            ordersCopy: [],
            loading:true,
            listOfCities:[],
            city: {Link:"", Сity:""},
            descriptionLimit: 60,
            model: null,
            search: "",
            searchMass: [],
        }),

        methods: {

        rebornMassForClick()
        {
            this.rebornMass();          
        },


        changeValue(e)
        {      
              
            if(e===undefined)
            {   
                this.loading=true;
                this.rebornMass();    
                setTimeout(() => this.loading=false, 3000);            
            }
            else
            {             
                 this.orders.splice(0, this.orders.length);
                 if(this.model!==null)
                 {
                      this.orders = [this.model];
                }              
            }
        },

        clearMass()
        {
            this.loading=true;
            this.rebornMass();  
            this.search="";  
            setTimeout(() => this.loading=false, 3000);   
        },

        rebornMass()
        {
            
            this.orders.splice(0, this.orders.length);
            
            for(let i=0;i<this.ordersCopy.length;i++)
            {
                this.orders.push(this.ordersCopy[i])
            }  
            
          
        },

        enterPress(e){
            if(e.key==="Enter")
               this.filterMass()
        },

        filterMass()
        {
            this.search = this.search.trim();
            this.searchMass=this.search.split(" "); 

            this.rebornMass(); 

            this.orders.splice(0, this.orders.length);
            this.orders=this.ordersCopy.filter(f=>f["Название группы"].toString().toLowerCase().includes(this.search.toLowerCase()) || f["Артикул"].toString().toLowerCase().includes(this.search.toLowerCase()) || f["Наименование"].toString().toLowerCase().includes(this.search.toLowerCase()) || f["ОПТ"].toString().toLowerCase().includes(this.search.toLowerCase()) || f["ОПТ-Мастер"].toString().toLowerCase().includes(this.search.toLowerCase()))

            if(this.orders.length ===0)
            {
                this.rebornMass(); 
                for(let i=0;i<this.orders.length;i++)
                {
                   var isCurent = false;

                    for(let j = 0; j<this.searchMass.length;j++)                    {
                        
                        if(this.orders[i]["Название группы"].toString().toLowerCase().includes(this.searchMass[j].toLowerCase()))
                        {                                                
                            isCurent = true;
                        }

                        if(this.orders[i]["Артикул"].toString().toLowerCase().includes(this.searchMass[j].toLowerCase()))
                        {
                                isCurent = true;
                        }

                        if(this.orders[i]["Наименование"].toString().toLowerCase().includes(this.searchMass[j].toLowerCase()))
                        {
                                isCurent = true;
                        }

                        if(this.orders[i]["ОПТ"].toString().toLowerCase().includes(this.searchMass[j].toLowerCase()))
                        {
                                isCurent = true;
                        }

                        if(this.orders[i]["ОПТ-Мастер"].toString().toLowerCase().includes(this.searchMass[j].toLowerCase()))
                        {
                                isCurent = true;
                        }
                    }

                    if(!isCurent)
                    {
                       this.orders.splice(i,1);
                       i--;
                    }
                        
                }
            }           
        },

        initialization(){
           if(this.city.Link===""){
                this.city.Link=this.listOfCities[0].Link 
                this.city.Сity=this.listOfCities[0].Сity
                this.downloadFileFromSite (this.city)
            }
        },

        downloadFileListOfCitiesFromSite () {
            this.loading=true;
            console.log("Разработчик Роман Дробязкин")
            //fetch('/listOfCities.xlsx', 
            fetch('https://skynet-service.com/price/listOfCities.xlsx', 
            {
              method: 'GET', // *GET, POST, PUT, DELETE, etc.             
            }).then(response => response.blob()).then(blob => {
              this.fileToJsonCity(this.blobToFile(blob, "City"))
            })
            
          },

          fileToJsonCity(e) {
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
                          this.listOfCities = JSON.parse(jsonObject)                                                
                        });
                    };
                    fileReader.readAsBinaryString(file);
                }
                setTimeout(this.initialization, 1000); //this.initialization(); 
            },


          downloadFileFromSite (e) {
            this.loading=true;
            if(e.Link!=="")
            {
                fetch(e.Link, 
                {
                method: 'GET', // *GET, POST, PUT, DELETE, etc.             
                }).then(response => response.blob()).then(blob => {
                this.fileToDevExtreme(this.blobToFile(blob, "test"))
                })              
            }
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
                          this.ordersCopy =  JSON.parse(jsonObject)          
                        });
                    };
                    fileReader.readAsBinaryString(file);
                }      
                setTimeout(() => this.loading=false, 3000);
            },


        /*Формирование файла в эксель*/
      onExporting(e) {
      const workbook = new Workbook();
      const worksheet = workbook.addWorksheet('Companies');

      worksheet.columns = [
        { width: 15 }, { width: 100 }, { width: 14 }, { width: 18 }
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
            excelCell.border = {
                top: {style:'thin', color: {argb:'000000'}},
                left: {style:'thin', color: {argb:'000000'}},
                bottom: {style:'thin', color: {argb:'000000'}},
                right: {style:'thin', color: {argb:'000000'}}
            };
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
          this.downloadFileListOfCitiesFromSite();     
          //this.filterMass();        
        },

        created() {
            loadMessages(ruMessages);
            locale(navigator.language);    
                  
        },

        computed: {

        }
    }


</script>

<style>
.dx-datagrid-nowrap, .dx-datagrid-nowrap .dx-header-row>td>.dx-datagrid-text-content {
    white-space: normal !important;
}

    /*.v-application .primary--text:hover {
        border-bottom: 2px solid;
    }*/

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


        .dx-button-mode-contained-copy {
        background-color: #0353B4;
        border-color: #0353B4;
        color: #fff !important;
        width: 200px;
        padding: 5px;
        border-radius: 5px;
        text-align: center;
        text-transform: capitalize;
    }

    .dx-button-mode-contained-copy:hover {
    background-color: #033e7c;
    border-color: #033e7c;
    color: #fff !important;
    
    }

    .dx-button-mode-contained-copy:focus {
    background-color: #033e7c;
    border-color: #033e7c;
    color: #fff !important;
    }

    .dx-button-mode-contained {
    background-color: #0353B4;
    border-color: #0353B4;
    color: #fff !important;
    width: 204px !important;
    position: relative;
    top: -130px;
    z-index: 3!important;
    opacity: 0;
    }

    .dx-button-mode-contained:hover {
    background-color: #033e7c;
    border-color: #033e7c;
    color: #fff !important;
    }

    .dx-button-mode-contained:focus {
    background-color: #033e7c;
    border-color: #033e7c;
    color: #fff !important;
    }

    .dx-button-mode-contained .dx-icon {
    color: #fff !important;
    }   

    .dx-datagrid-group-closed{
        color: #fff !important;
    }

    .select-text{
        font-size: 17px !important;
        font-weight: 500;
        color: #bfbfbf;
        padding-top: 8px !important;
         /*   min-width: 300px;*/
        /*text-align: right;*/
    }

    .load-card{
        width: 160px;
        text-align: center;
        height: 160px;
        padding: 33px;
        margin: 0 auto;
    }

    .v-dialog {
        width: 160px !important;
        text-align: center !important;
        height: 160px !important;
        padding: 33px !important;
        margin: 0 auto !important;
        padding: 0px !important;
        box-shadow:none !important;
    }

    .v-overlay--active {
        z-index: 3 !important;
    }

    .no-style{
        background: #fff;
        color: #000;
    }

    .dx-datagrid-group-panel .dx-group-panel-item {
        display: none;
    }

    .dx-selection-disabled{
        display: none;
    }

         @media screen and (min-width: 638px){ 
         .no-margin{
             margin-top: -19px !important;
            }
     }

     @media screen and (max-width: 803px) and (min-width: 616px){ 
         .dx-button-mode-contained {
             top: -135px;
            }
     }

    @media screen and (max-width: 637px) and (min-width: 616px){ 
         .dx-button-mode-contained {
             top: -154px;
            }
     }


    @media screen and (max-width: 615px) and (min-width: 459px){ 
        .dx-button-mode-contained {
            top: -195px;
            left: -46%;
            width: 404px !important;
        }

        .select-text {
            min-width: 177px;
        }
     }

    @media screen and (max-width: 459px) { 
        .select-text{
         min-width: 300px;
        }

    .dx-button-mode-contained-copy {
    width: 300px;
    margin: 0 auto;
    }

    .dx-toolbar-item-content > .dx-texteditor{
        width: 296px !important;
    }

    .dx-button-mode-contained {
    width: 646px !important;
    top: -195px;
    left: 9px;
    }

    .select-text{
            text-align: center;
    }

    .no-spacer{
        display: none;
    }

    .v-select {
        width: 300px;
        margin: 0 auto !important;
    }

    }

    </style>
