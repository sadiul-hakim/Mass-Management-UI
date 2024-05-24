<script>
  import { onMount } from "svelte";
  import { toasts, ToastContainer, FlatToast } from "svelte-toasts";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import {
    domain_api,
    export_pdf_api,
    report_save_and_clean_api,
    login_url,
    generate_report,
  } from "../util/apis";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  let report = {};
  let bordersInfo = [];
  let reportData = {
    report: {},
  };

  onMount(async () => {
    loadReport();
  });

  async function loadReport() {
    let response = await fetch(`${domain_api}${generate_report}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    report = await response.json();
    bordersInfo = report.borders;
    reportData = {
      report,
    };
  }

  // actions

  async function saveAndClean() {
    let response = await fetch(domain_api + report_save_and_clean_api, {
      method: "POST",
      body: JSON.stringify(reportData),
      headers: {
        Authorization: "Bearer " + authorization.token,
        "Content-Type": "application/json",
      },
    });

    if (response.status === 200) {
      showToast();
    }
  }

  async function exportPDF() {
    let response = await fetch(domain_api + export_pdf_api, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    let blob = await response.blob();

    // Create a new URL for the blob
    const url = window.URL.createObjectURL(blob);
    // Create a link element
    const a = document.createElement("a");
    // Set the href and download attributes for the link
    a.href = url;
    a.download = "report.pdf"; // Provide the file name here
    // Append the link to the body
    document.body.appendChild(a);
    // Trigger the download by simulating a click on the link
    a.click();
    // Clean up by revoking the object URL and removing the link
    window.URL.revokeObjectURL(url);
    a.remove();
  }

  // Toast
  // Toast Message
  const showToast = () => {
    const toast = toasts.add({
      title: "Report",
      description: "This report is saved and Database is cleaned.",
      duration: 10000, // 0 or negative to avoid auto-remove
      placement: "top-right",
      theme: "dark",
      type: "success",
      onClick: () => {},
      onRemove: () => {},
      // component: BootstrapToast, // allows to override toast component/template per toast
    });

    // toast.remove()
  };
</script>

<div class="card h-100 overflow-y-auto p-3">
  <div class="my-1 d-flex justify-content-end">
    <button
      class="btn btn-dark me-2"
      data-bs-toggle="modal"
      data-bs-target="#exampleModal">Save And Clean</button
    >
    <button class="btn btn-dark" title="Download PDF" on:click={exportPDF}
      ><i class="bi bi-filetype-pdf"></i></button
    >
  </div>
  <h3 class="mb-2">Report</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">Date</th>
        <th scope="col">Meal Rate</th>
        <th scope="col">Market Cost</th>
        <th scope="col">Extra Cost</th>
        <th scope="col">Total Cost</th>
        <th scope="col">Total Deposit</th>
        <th scope="col">Total Income</th>
        <th scope="col">Borders</th>
        <th scope="col">Total Meals</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>{report.date}</td>
        <td>{report.mealRate}</td>
        <td>{report.market}</td>
        <td>{report.extra_cost}</td>
        <td>{report.total_cost}</td>
        <td>{report.deposit}</td>
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
          <td>{border.extra_cost}</td>
          <td>{border.total_cost}</td>
          <td>{border.balance}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>

<!-- Toast Message -->

<ToastContainer placement="bottom-right" let:data>
  <FlatToast {data} />
  <!-- Provider template for your toasts -->
</ToastContainer>

<!-- Modal -->
<div
  class="modal fade"
  id="exampleModal"
  tabindex="-1"
  aria-labelledby="exampleModalLabel"
  aria-hidden="true"
>
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h1 class="modal-title fs-5" id="exampleModalLabel">Warning</h1>
        <button
          type="button"
          class="btn-close"
          data-bs-dismiss="modal"
          aria-label="Close"
        ></button>
      </div>
      <div class="modal-body">Are you sure that you want to clean?</div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal"
          >Close</button
        >
        <button
          type="button"
          class="btn btn-primary"
          on:click={saveAndClean}
          data-bs-dismiss="modal">Yes</button
        >
      </div>
    </div>
  </div>
</div>
