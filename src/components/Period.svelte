<script>
  import { onMount } from "svelte";
  import { toasts, ToastContainer, FlatToast } from "svelte-toasts";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import {
    login_url,
    domain_api,
    add_period,
    get_all_period,
    delete_period,
  } from "../util/apis";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  let periods = [];
  let periodData = {
    id: 0,
    name: "",
    periodOrder: 0,
    description: "",
  };
  // Variables ends here
  onMount(async () => {
    loadAll();
  });
  // on page load functionality
  async function handleSubmit() {
    let response = await fetch(`${domain_api}${add_period}`, {
      method: "POST",
      body: JSON.stringify(periodData),
      headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + authorization.token,
      },
    });
    await loadAll();
    clearData();
  }
  // saving of types

  async function loadAll() {
    let response = await fetch(`${domain_api}${get_all_period}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    periods = await response.json();
  }
  // loading all types
  async function deleteOne(event, id) {
    let response = await fetch(`${domain_api}${delete_period}${id}`, {
      method: "DELETE",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });

    if (response.status !== 200) {
      let msg = await response.json();
      showToast(msg.error);
    }
    await loadAll();
  }
  // delete type
  function clearData() {
    periodData = {
      id: 0,
      name: "",
      description: "",
    };
  }
  // clearing local form data

  // Toast Message
  const showToast = (text) => {
    const toast = toasts.add({
      title: "Transaction Type",
      description: text,
      duration: 10000, // 0 or negative to avoid auto-remove
      placement: "top-right",
      theme: "dark",
      type: "danger",
      onClick: () => {},
      onRemove: () => {},
      // component: BootstrapToast, // allows to override toast component/template per toast
    });

    // toast.remove()
  };
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">Period</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Name</th>
        <th scope="col">Order</th>
        <th scope="col">Description</th>
        <th scope="col"
          ><button
            class="btn btn-primary"
            data-bs-toggle="modal"
            data-bs-target="#exampleModal"><i class="bi bi-plus-lg"></i></button
          ></th
        >
      </tr>
    </thead>
    <tbody>
      {#each periods as period, index (period.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{period.name}</td>
          <td>{period.periodOrder}</td>
          <td>{period.description}</td>
          <td
            ><i class="bi bi-pencil-square"></i>&nbsp;
            <i class="bi bi-trash3" on:click={(e) => deleteOne(e, period.id)}
            ></i></td
          >
        </tr>
      {/each}
    </tbody>
  </table>
</div>

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
        <h1 class="modal-title fs-5" id="exampleModalLabel">
          Saving User Status
        </h1>
        <button
          type="button"
          class="btn-close"
          data-bs-dismiss="modal"
          aria-label="Close"
        ></button>
      </div>
      <div class="modal-body">
        <form on:submit|preventDefault={handleSubmit}>
          <div>
            <label for="title">Period</label>
            <input
              type="text"
              name="name"
              id="name"
              class="form-control"
              bind:value={periodData.name}
            />
          </div>
          <br />
          <div>
            <label for="title">Order</label>
            <input
              type="number"
              name="order"
              id="order"
              class="form-control"
              bind:value={periodData.periodOrder}
            />
          </div>
          <br />
          <div>
            <label for="description">Description</label>
            <textarea
              name="description"
              id="description"
              class="form-control"
              bind:value={periodData.description}
            ></textarea>
          </div>
          <br />
          <button type="submit" class="btn btn-success">Save</button>
        </form>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal"
          >Close</button
        >
      </div>
    </div>
  </div>
</div>

<ToastContainer placement="bottom-right" let:data>
  <FlatToast {data} />
  <!-- Provider template for your toasts -->
</ToastContainer>

<style>
  i {
    cursor: pointer;
  }
</style>
