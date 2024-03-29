<script>
  import { onMount } from "svelte";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";

  import {
    domain_api,
    delete_report_api,
    get_all_report_api,
  } from "../util/apis";

  let authorization = {};
  let reports = [];

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push("/login");
  }

  onMount(async () => {
    getAllReports();
  });

  async function deleteOneReport(event, id) {
    let response = await fetch(domain_api + delete_report_api + id, {
      method: "DELETE",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    let data = await response.json();
    console.log(data);
    getAllReports();
  }

  async function getAllReports() {
    let response = await fetch(domain_api + get_all_report_api, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    reports = await response.json();
  }
</script>

<div class="h-100 overflow-y-auto p-3">
  <h1 class="mb-2">Old Report</h1>
  {#each reports as report (report.id)}
    <div class="card p-2 mb-2">
      <div class="d-flex justify-content-end">
        <button
          class="btn btn-danger"
          on:click={(e) => deleteOneReport(e, report.id)}
          ><i class="bi bi-trash3-fill"></i></button
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
            <td>{report.report.date}</td>
            <td>{report.report.mealRate}</td>
            <td>{report.report.other_cost}</td>
            <td>{report.report.total_cost}</td>
            <td>{report.report.total_income}</td>
            <td>{report.report.total_borders}</td>
            <td>{report.report.total_meals}</td>
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
          {#each report.report.borders as border, index (index)}
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
  {/each}
</div>
