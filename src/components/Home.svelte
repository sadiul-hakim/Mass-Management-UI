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
  let bills = [];
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
    console.log(borderInfo);
    bills = borderInfo.bills;

    console.log(tableHeaders);
    if (borderInfo.length !== 0) {
      let info = borderInfo[0];
      tableHeaders = Object.keys(info.bills);
    }

    borderDepositeChart(borderInfo);
  }

  function borderDepositeChart(borderInfo) {
    let dataArray = [];
    let info;
    for (let i = 0; i < borderInfo.length; i++) {
      info = borderInfo[i];
      dataArray[i] = {
        label: info.name,
        y: info["Border Deposit"],
      };
    }

    var chart = new CanvasJS.Chart("chart", {
      animationEnabled: true,
      title: {
        text: "Border Deposite Chart",
      },
      axisX: {
        interval: 1,
      },
      axisY2: {
        interlacedColor: "rgba(1,77,101,.2)",
        gridColor: "rgba(1,77,101,.1)",
        title: "",
      },
      data: [
        {
          type: "bar",
          name: "companies",
          color: "#014D65",
          axisYType: "secondary",
          dataPoints: dataArray,
        },
      ],
    });

    chart.render();
  }
</script>

<div class="h-100 overflow-y-auto p-3">
  <div class="row d-flex justify-content-between">
    <div class="card col-md-2 m-1 text-center p-2">
      <h4>Total Deposit</h4>
      <h5>{totals.deposit}</h5>
    </div>
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
      <h4>Other Borders</h4>
      <h5>{totals.other_users}</h5>
    </div>
  </div>

  <!-- Deposite Section -->
  <div class="row mt-4">
    <div class="col-12" style="height: 370px; width:100%;" id="chart"></div>
  </div>
  <!-- Bills Section -->

  <div class="row card p-3 mt-4">
    <table class="table">
      <thead>
        <tr>
          <th>Name</th>
          {#each tableHeaders as header, index (index)}
            <th>{header}</th>
          {/each}
        </tr>
      </thead>
      <tbody>
        {#each borderInfo as info, index (index)}
          <tr>
            <td>{info["name"]}</td>
            {#each tableHeaders as header, ind (ind)}
              <td>{info.bills[header]}</td>
            {/each}
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>
