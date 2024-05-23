<script>
  import { onMount } from "svelte";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import { login_url } from "../util/apis";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push("/login");
  }

  export let manager = {};
  let borders = [];
  let plans = [];
  let periods = [];
  let planDate = {
    id: 0,
    item: "",
    period: 0,
    date: "",
  };

  onMount(async () => {
    loadBorders();
    loadPlans();
    loadAllPeriods();
  });

  // action section
  async function deleteOne(event, id) {
    console.log("delete " + id);
    let response = await fetch(
      `http://localhost:9090/meal-plan/v1/delete/${id}`,
      {
        method: "DELETE",
        headers: {
          Authorization: "Bearer " + authorization.token,
        },
      }
    );
    let data = await response.json();
    console.log(data);
    await loadPlans();
  }
  async function changeManager(event, borderId) {
    let response = await fetch(
      `http://localhost:9090/user/v1/change-manager?managerId=${manager.id}&userId=${borderId}`,
      {
        method: "GET",
        headers: {
          Authorization: "Bearer " + authorization.token,
        },
      }
    );

    let data = await response.json();
    if (data.status === 200) {
      logout();
    }

    console.log(data);
  }

  async function handleSubmit() {
    let response = await fetch("http://localhost:9090/meal-plan/v1/add", {
      method: "POST",
      body: JSON.stringify(planDate),
      headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + authorization.token,
      },
    });
    await loadPlans();
    clearData();
  }

  function clearData() {
    planDate = {
      id: 0,
      item: "",
      period: 0,
    };
  }

  // load section
  async function loadAllPeriods() {
    let response = await fetch("http://localhost:9090/period/v1/get-all", {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    periods = await response.json();
  }
  async function loadPlans() {
    let response = await fetch("http://localhost:9090/meal-plan/v1/get-all", {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    plans = await response.json();
  }
  async function loadBorders() {
    let roleResponse = await fetch(
      "http://localhost:9090/user-role/v1/get-by-role/Border",
      {
        method: "GET",
        headers: {
          Authorization: "Bearer " + authorization.token,
        },
      }
    );
    let borderRole = await roleResponse.json();
    let response = await fetch(
      `http://localhost:9090/user/v1/get-by-role/${borderRole.id}`,
      {
        method: "GET",
        headers: {
          Authorization: "Bearer " + authorization.token,
        },
      }
    );
    borders = await response.json();
  }

  // logout
  function logout() {
    Authorization.set(undefined);
    localStorage.setItem("authorization", "");
    push(login_url);
  }
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">Meal Plan</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Date</th>
        <th scope="col">Period</th>
        <th scope="col">Item</th>
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
      {#each plans as plan, index (plan.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{plan.date}</td>
          <td>{plan.period.name}</td>
          <td>{plan.item}</td>
          <td>
            <button
              class="btn btn-danger"
              on:click={(event) => deleteOne(event, plan.id)}
              ><i class="bi bi-trash3"></i></button
            >
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
  <!-- plan section ends -->
  <div class="mt-2">
    <h3 class="mb-2">Change Manager</h3>
    <table class="table">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Name</th>
          <th scope="col">Phone</th>
          <th scope="col">Role</th>
          <th scope="col">Status</th>
          <th scope="col"> Actions</th>
        </tr>
      </thead>
      <tbody>
        {#each borders as border, index (border.id)}
          <tr>
            <th scope="row">{index + 1}</th>
            <td>{border.name}</td>
            <td>{border.phone}</td>
            <td>{border.role.role}</td>
            <td>{border.status.status}</td>
            <td
              ><button
                class="btn btn-primary"
                on:click|preventDefault={(event) =>
                  changeManager(event, border.id)}>Make Manager</button
              ></td
            >
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
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
            <label for="title">Date</label>
            <input
              type="date"
              name="status"
              id="status"
              class="form-control"
              bind:value={planDate.date}
            />
          </div>
          <br />
          <div>
            <label for="period">Period</label>
            <select
              name="period"
              class="form-control"
              bind:value={planDate.period}
            >
              {#each periods as period (period.id)}
                <option value={period.id}>{period.name}</option>
              {/each}
            </select>
          </div>
          <br />
          <div>
            <label for="item">Item</label>
            <input
              type="text"
              name="item"
              id="item"
              class="form-control"
              bind:value={planDate.item}
            />
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
