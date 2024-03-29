<script>
  import { onMount } from "svelte";
  import { toasts, ToastContainer, FlatToast } from "svelte-toasts";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push("/login");
  }

  let types = [];
  let typeData = {
    id: 0,
    name: "",
    description: "",
  };
  // Variables ends here
  onMount(async () => {
    loadAll();
  });
  // on page load functionality
  async function handleSubmit() {
    let response = await fetch("http://localhost:9090/meal-type/v1/add", {
      method: "POST",
      body: JSON.stringify(typeData),
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
    let response = await fetch("http://localhost:9090/meal-type/v1/get-all", {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    types = await response.json();
  }
  // loading all types
  async function deleteOne(event, id) {
    let response = await fetch(
      `http://localhost:9090/meal-type/v1/delete/${id}`,
      {
        method: "DELETE",
        headers: {
          Authorization: "Bearer " + authorization.token,
        },
      }
    );

    if (response.status !== 200) {
      let msg = await response.json();
      showToast(msg.error);
    }
    await loadAll();
  }
  // delete type
  function clearData() {
    typeData = {
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
  <h3 class="mb-2">Meal Type</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Name</th>
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
      {#each types as type, index (type.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{type.name}</td>
          <td>{type.description}</td>
          <td
            ><i class="bi bi-pencil-square"></i>&nbsp;
            <i
              class="bi bi-trash3"
              on:click={(event) => deleteOne(event, type.id)}
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
          Saving Meal Type
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
            <label for="title">Name</label>
            <input
              type="text"
              name="name"
              id="name"
              class="form-control"
              bind:value={typeData.name}
            />
          </div>
          <br />
          <div>
            <label for="description">Description</label>
            <textarea
              name="description"
              id="description"
              class="form-control"
              bind:value={typeData.description}
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
