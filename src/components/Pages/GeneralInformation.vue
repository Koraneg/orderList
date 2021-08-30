<template>
  <div>
    <v-container fluid style="width:100%;">
      <v-row>
        <v-col>
          <DxDataGrid
            :allow-column-reordering="true"
            :data-source="customers"
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
            <DxPaging :page-size="10"/>
            <DxSearchPanel :visible="true"/>
          </DxDataGrid>
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
export default {
components: {
DxColumn,
DxGroupPanel,
DxGrouping,
DxPaging,
DxSearchPanel,
DxDataGrid,
DxHeaderFilter,
DxFilterRow
},
name: "GeneralInformation",
  data: () => ({
    autoExpandAll: true,
  }),
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