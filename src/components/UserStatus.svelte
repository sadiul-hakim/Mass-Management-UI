<script>
  import { onMount } from "svelte";
  let statuses = [];
  let statusData = {
    id: 0,
    status: "",
    description: "",
  };
  // Variables ends here
  onMount(async () => {
    loadAll();
  });
  // on page load functionality
  async function handleSubmit() {
    let response = await fetch("http://localhost:9090/user-status/v1/add", {
      method: "POST",
      body: JSON.stringify(statusData),
      headers: {
        "Content-Type": "application/json",
      },
    });
    await loadAll();
    clearData();
  }
  // saving of types

  async function loadAll() {
    let response = await fetch("http://localhost:9090/user-status/v1/get-all");
    statuses = await response.json();
  }
  // loading all types
  async function deleteOne(event, id) {
    let response = await fetch(
      `http://localhost:9090/user-status/v1/delete/${id}`,
      {
        method: "DELETE",
      }
    );
    await loadAll();
  }
  // delete type
  function clearData() {
    statusData = {
      id: 0,
      status: "",
      description: "",
    };
  }
  // clearing local form data
</script>

<div class="card h-100 p-3">
  <h3 class="mb-2">User Status</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Status</th>
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
      {#each statuses as status, index (status.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{status.status}</td>
          <td>{status.description}</td>
          <td
            ><i class="bi bi-pencil-square"></i>&nbsp;
            <i class="bi bi-trash3" on:click={(e) => deleteOne(e, status.id)}
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
            <label for="title">Status</label>
            <input
              type="text"
              name="status"
              id="status"
              class="form-control"
              bind:value={statusData.status}
            />
          </div>
          <br />
          <div>
            <label for="description">Description</label>
            <textarea
              name="description"
              id="description"
              class="form-control"
              bind:value={statusData.description}
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
