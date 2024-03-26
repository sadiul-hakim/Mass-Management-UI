<script>
  import { onMount } from "svelte";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push("/login");
  }

  let report = {};
  let bordersInfo = [];

  onMount(async () => {
    loadReport();
  });

  async function loadReport() {
    let response = await fetch("http://localhost:9090/report/v1/generate", {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    report = await response.json();
    console.log(report);
    bordersInfo = report.borders;
  }
</script>

<div class="card h-100 overflow-y-auto p-3">
  <div class="my-1 d-flex justify-content-end">
    <button class="btn btn-dark me-2">Save And Clean</button>
    <button class="btn btn-dark" title="Download PDF"
      ><i class="bi bi-filetype-pdf"></i></button
    >
  </div>
  <h3 class="mb-2">Report</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">Date</th>
        <th scope="col">Meal Rate</th>
        <th scope="col">Extra Cost</th>
        <th scope="col">Total Cost</th>
        <th scope="col">Total Income</th>
        <th scope="col">Borders</th>
        <th scope="col">Total Meals</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>{report.date}</td>
        <td>{report.mealRate}</td>
        <td>{report.other_cost}</td>
        <td>{report.total_cost}</td>
        <td>{report.total_income}</td>
        <td>{report.total_borders}</td>
        <td>{report.total_meals}</td>
      </tr>
    </tbody>
  </table>
  <!-- Borders section -->
  <h3 class="my-2">Borders Report</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">Border</th>
        <th scope="col">Meals</th>
        <th scope="col">Deposite</th>
        <th scope="col">Meal Cost</th>
        <th scope="col">Extra Cost</th>
        <th scope="col">Total Cost</th>
        <th scope="col">Balance</th>
      </tr>
    </thead>
    <tbody>
      {#each bordersInfo as border, index (index)}
        <tr>
          <td>{border.border.name}</td>
          <td>{border.meals}</td>
          <td>{border.deposit}</td>
          <td>{border.meal_cost}</td>
          <td>{border.other_cost}</td>
          <td>{border.total_cost}</td>
          <td>{border.balance}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>
