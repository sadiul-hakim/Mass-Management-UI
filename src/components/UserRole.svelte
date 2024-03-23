<script>
  import { onMount } from "svelte";
  let roles = [];
  let roleData = {
    id: 0,
    role: "",
    description: "",
  };
  // Variables ends here
  onMount(async () => {
    loadAll();
  });
  // on page load functionality
  async function handleSubmit() {
    let response = await fetch("http://localhost:9090/user-role/v1/add", {
      method: "POST",
      body: JSON.stringify(roleData),
      headers: {
        "Content-Type": "application/json",
      },
    });
    await loadAll();
    clearData();
  }
  // saving of types

  async function loadAll() {
    let response = await fetch("http://localhost:9090/user-role/v1/get-all");
    roles = await response.json();
  }
  // loading all types
  async function deleteOne(event, id) {
    let response = await fetch(
      `http://localhost:9090/user-role/v1/delete/${id}`,
      {
        method: "DELETE",
      }
    );
    await loadAll();
  }
  // delete type
  function clearData() {
    roleData = {
      id: 0,
      role: "",
      description: "",
    };
  }
  // clearing local form data
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">User Role</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Role</th>
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
      {#each roles as role, index (role.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{role.role}</td>
          <td>{role.description}</td>
          <td
            ><i class="bi bi-pencil-square"></i>&nbsp;
            <i
              class="bi bi-trash3"
              on:click={(event) => deleteOne(event, role.id)}
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
        <h1 class="modal-title fs-5" id="exampleModalLabel">Saving Role</h1>
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
            <label for="title">Role</label>
            <input
              type="text"
              name="role"
              id="role"
              class="form-control"
              bind:value={roleData.role}
            />
          </div>
          <br />
          <div>
            <label for="description">Description</label>
            <textarea
              name="description"
              id="description"
              class="form-control"
              bind:value={roleData.description}
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
