<template>
  <div>
    <h2 class="content-block">Provinsi</h2>
    <div class="content-block">
      <div class="dx-card responsive-paddings">
        <div id="app-container">
          <DxDataGrid id="dataGrid" :data-source="customDataSource">
            <DxRemoteOperations :group-paging="true" />
          </DxDataGrid>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DxDataGrid, { DxRemoteOperations } from "devextreme-vue/data-grid";

import CustomStore from "devextreme/data/custom_store";
import "whatwg-fetch";

const isNotEmpty = (value) =>
  value !== undefined && value !== null && value !== "";

function handleErrors(response) {
  if (!response.ok) {
    throw Error(response.statusText);
  }
  return response;
}

const customDataSource = new CustomStore({
  key: "ID",
  load: (loadOptions) => {
    let params = "?";
    [
      "filter",
      "group",
      "groupSummary",
      "parentIds",
      "requireGroupCount",
      "requireTotalCount",
      "searchExpr",
      "searchOperation",
      "searchValue",
      "select",
      "sort",
      "skip",
      "take",
      "totalSummary",
      "userData",
    ].forEach(function (i) {
      if (i in loadOptions && isNotEmpty(loadOptions[i])) {
        params += `${i}=${JSON.stringify(loadOptions[i])}&`;
      }
    });
    params = params.slice(0, -1);

    return fetch(`http://localhost:4000/api/provinsi/${params}`)
      .then(handleErrors)
      .then((response) => response.json())
      .then((response) => {
        return {
          data: response.data,
          totalCount: response.totalCount,
          summary: response.summary,
          groupCount: response.groupCount,
        };
      })
      .catch(() => {
        throw "Network error";
      });
  },
  // Needed to process selected value(s) in the SelectBox, Lookup, Autocomplete, and DropDownBox
  // byKey: (key) => {
  //     return fetch(`https://mydomain.com/MyDataService?id=${key}`)
  //         .then(handleErrors);
  // }
  insert: (values) => {
    return fetch("http://localhost:4000/api/provinsi/create/", {
      method: "POST",
      body: JSON.stringify(values),
      headers: {
        "Content-Type": "application/json",
      },
    })
      .then(handleErrors)
      .catch(() => {
        throw "Network error";
      });
  },
  remove: (key) => {
    return fetch(
      `http://localhost:4000/api/provinsi/delete/${encodeURIComponent(key)}`,
      {
        method: "DELETE",
      }
    )
      .then(handleErrors)
      .catch(() => {
        throw "Network error";
      });
  },
  update: (key, values) => {
    return fetch(
      `http://localhost:4000/api/provinsi/update/${encodeURIComponent(key)}`,
      {
        method: "PUT",
        body: JSON.stringify(values),
        headers: {
          "Content-Type": "application/json",
        },
      }
    )
      .then(handleErrors)
      .catch(() => {
        throw "Network error";
      });
  },
});

export default {
  components: {
    DxDataGrid,
    DxRemoteOperations,
  },
  data() {
    return {
      customDataSource,
    };
  },
};
</script>

<style lang="scss"></style>
