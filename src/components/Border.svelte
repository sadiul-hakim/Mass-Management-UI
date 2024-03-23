<script>
  import { onMount } from "svelte";
  let users = [];
  let role = {};
  let statuses = [];
  let userData = {
    id: 0,
    name: "",
    phone: "",
    password: "",
    address: "",
    role: 0,
    status: 0,
  };
  // Variables ends here
  onMount(async () => {
    loadAll();
    loadBorderRole();
    loadAllStatus();
  });
  // on page load functionality
  async function handleSubmit() {
    let response = await fetch("http://localhost:9090/user/v1/add", {
      method: "POST",
      body: JSON.stringify(userData),
      headers: {
        "Content-Type": "application/json",
      },
    });
    await loadAll();
    clearData();
  }
  // saving of types

  async function loadAll() {
    let response = await fetch("http://localhost:9090/user/v1/get-all");
    users = await response.json();
  }

  async function loadBorderRole() {
    let response = await fetch(
      "http://localhost:9090/user-role/v1/get-by-role/Border"
    );
    role = await response.json();
    userData.role = role.id;
  }

  async function loadAllStatus() {
    let response = await fetch("http://localhost:9090/user-status/v1/get-all");
    statuses = await response.json();
  }
  // loading all types
  async function deleteOne(event, id) {
    let response = await fetch(`http://localhost:9090/user/v1/delete/${id}`, {
      method: "DELETE",
    });
    await loadAll();
  }
  // delete type
  function clearData() {
    userData = {
      id: 0,
      name: "",
      phone: "",
      password: "",
      address: "",
      role: userData.role,
      status: 0,
    };
  }
  // clearing local form data
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">Border</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Name</th>
        <th scope="col">Phone</th>
        <th scope="col">Role</th>
        <th scope="col">Status</th>
        <th scope="col">Address</th>
        <th scope="col">Joining Date</th>
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
      {#each users as user, index (user.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{user.name}</td>
          <td>{user.phone}</td>
          <td>{user.role.role}</td>
          <td>{user.status.status}</td>
          <td>{user.address}</td>
          <td>{user.joiningDate}</td>
          <td
            ><i class="bi bi-pencil-square"></i>&nbsp;
            <i
              class="bi bi-trash3"
              on:click={(event) => deleteOne(event, user.id)}
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
            <label for="name">Name</label>
            <input
              type="text"
              name="name"
              id="name"
              class="form-control"
              bind:value={userData.name}
            />
          </div>
          <br />
          <div>
            <label for="phone">Phone</label>
            <input
              type="text"
              name="phone"
              id="phone"
              class="form-control"
              bind:value={userData.phone}
            />
          </div>
          <br />
          <div>
            <label for="password">Password</label>
            <input
              type="password"
              name="password"
              id="password"
              class="form-control"
              bind:value={userData.password}
            />
          </div>
          <br />
          <div>
            <label for="role">Role</label>
            <input
              type="test"
              name="role"
              id="role"
              class="form-control"
              value={role.role}
              disabled
            />
          </div>
          <br />
          <div>
            <label for="status">Status</label>
            <select
              name="status"
              class="form-control"
              bind:value={userData.status}
            >
              {#each statuses as status}
                <option value={status.id}>{status.status}</option>
              {/each}
            </select>
          </div>
          <br />
          <div>
            <label for="address">Address</label>
            <textarea
              name="address"
              class="form-control"
              bind:value={userData.address}
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
