<script>
  import { onMount } from "svelte";
  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (!authorization) {
    push("/login");
  }

  let totals = {};
  let borderInfo = [];
  let tableHeaders = [];

  // Variables ends here
  onMount(async () => {
    loadTotals();
    loadBorderInfo();
  });
  // saving of types

  async function loadTotals() {
    let response = await fetch("http://localhost:9090/home/v1/totals", {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    totals = await response.json();
  }

  async function loadBorderInfo() {
    let response = await fetch("http://localhost:9090/home/v1/border-info", {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    borderInfo = await response.json();
    if (borderInfo.length !== 0) {
      let info = borderInfo[0];
      tableHeaders = Object.keys(info);
    }
  }
</script>

<div class="h-100 overflow-y-auto p-3">
  <div class="row d-flex justify-content-between">
    <div class="card col-md-2 m-1 text-center p-2">
      <h4>Total Income</h4>
      <h5>{totals.income}</h5>
    </div>
    <div class="card col-md-2 m-1 text-center p-2">
      <h4>Total Cost</h4>
      <h5>{totals.cost}</h5>
    </div>
    <div class="card col-md-2 m-1 text-center p-2">
      <h4>Total Active Borders</h4>
      <h5>{totals.active_user}</h5>
    </div>
    <div class="card col-md-2 m-1 text-center p-2">
      <h4>Total Inactive Borders</h4>
      <h5>{totals.inactive_user}</h5>
    </div>
  </div>
  <div class="row card p-3 mt-4">
    <table class="table">
      <thead>
        <tr>
          {#each tableHeaders as header, index (index)}
            <th>{header}</th>
          {/each}
        </tr>
      </thead>
      <tbody>
        {#each borderInfo as info, index (index)}
          <tr>
            {#each tableHeaders as header, ind (ind)}
              <td>{info[header]}</td>
            {/each}
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>
