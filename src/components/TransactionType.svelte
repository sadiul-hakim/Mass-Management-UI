<script>
  import { onMount } from "svelte";
  let types = [];
  let typeData = {
    id: 0,
    title: "",
    description: "",
  };
  // Variables ends here
  onMount(async () => {
    loadAll();
  });
  // on page load functionality
  async function handleSubmit() {
    let response = await fetch(
      "http://localhost:9090/transaction-type/v1/add",
      {
        method: "POST",
        body: JSON.stringify(typeData),
        headers: {
          "Content-Type": "application/json",
        },
      }
    );
    await loadAll();
    clearData();
    console.log(response.status);
  }
  // saving of types

  async function loadAll() {
    let response = await fetch(
      "http://localhost:9090/transaction-type/v1/get-all"
    );
    types = await response.json();
  }
  // loading all types
  async function deleteOne(event, id) {
    let response = await fetch(
      `http://localhost:9090/transaction-type/v1/delete/${id}`,
      {
        method: "DELETE",
      }
    );
    await loadAll();
  }
  // delete type
  function clearData() {
    typeData = {
      id: 0,
      title: "",
      description: "",
    };
  }
  // clearing local form data
</script>

<div class="card h-100 p-3">
  <h3 class="mb-2">Transaction Type</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Title</th>
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
          <td>{type.title}</td>
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
          Saving Transaction Type
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
            <label for="title">Title</label>
            <input
              type="text"
              name="title"
              id="title"
              class="form-control"
              bind:value={typeData.title}
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

<style>
  i {
    cursor: pointer;
  }
</style>
