<script>
  import { onMount } from "svelte";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import {
    add_meal,
    add_meal_in_range,
    delete_meal,
    domain_api,
    get_all_meal,
    get_all_meal_type,
    get_all_period,
    get_all_user,
    login_url,
  } from "../util/apis";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  let meals = [];
  let users = [];
  let mealTypes = [];
  let periods = [];
  let offType = 0;

  let mealData = {
    id: 0,
    userId: 0,
    type: 0,
    amount: 0,
    period: 0,
    date: "",
  };

  let rangeMealData = {
    id: 0,
    userId: 0,
    type: 0,
    amount: 0,
    startDate: "",
    endDate: "",
    period: 0,
  };

  onMount(async () => {
    loadAllMeals();
    loadAllMealTypes();
    loadAllUsers();
    loadAllPeriods();
  });

  async function handleSubmit() {
    let response = await fetch(`${domain_api}${add_meal}`, {
      method: "POST",
      body: JSON.stringify(mealData),
      headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + authorization.token,
      },
    });
    await loadAllMeals();
    clearData();
  }

  async function handleRangeMealSubmit() {
    let response = await fetch(`${domain_api}${add_meal_in_range}`, {
      method: "POST",
      body: JSON.stringify(rangeMealData),
      headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + authorization.token,
      },
    });
    await loadAllMeals();
    clearRangeData();
  }

  async function deleteOne(event, id) {
    let response = await fetch(`${domain_api}${delete_meal}${id}`, {
      method: "DELETE",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    await loadAllMeals();
  }
  // delete type
  function clearData() {
    mealData = {
      id: 0,
      userId: 0,
      type: 0,
      amount: 1,
      period: 0,
    };
  }

  function clearRangeData() {
    rangeMealData = {
      id: 0,
      userId: 0,
      type: 0,
      amount: 1,
      startDate: "",
      endDate: "",
      period: 0,
    };
  }

  // load all user;
  async function loadAllMeals() {
    let response = await fetch(`${domain_api}${get_all_meal}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    meals = await response.json();
  }

  async function loadAllUsers() {
    let response = await fetch(`${domain_api}${get_all_user}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    users = await response.json();
  }

  async function loadAllMealTypes() {
    let response = await fetch(`${domain_api}${get_all_meal_type}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    mealTypes = await response.json();

    mealTypes.forEach((type) => {
      if (type.name == "Off") {
        offType = type.id;
        console.log(offType);
      }
    });
  }

  async function loadAllPeriods() {
    let response = await fetch(`${domain_api}${get_all_period}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    periods = await response.json();
  }
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">Meal</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Border</th>
        <th scope="col">Type</th>
        <th scope="col">Amount</th>
        <th scope="col">Period</th>
        <th scope="col">Date</th>
        <th scope="col">
          <button
            class="btn btn-primary me-2"
            data-bs-toggle="modal"
            data-bs-target="#exampleModal2">Range</button
          >
          <button
            class="btn btn-primary"
            data-bs-toggle="modal"
            data-bs-target="#exampleModal"><i class="bi bi-plus-lg"></i></button
          ></th
        >
      </tr>
    </thead>
    <tbody>
      {#each meals as meal, index (meal.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{meal.user.name}</td>
          <td>{meal.type.name}</td>
          <td>{meal.amount}</td>
          <td>{meal.period.name}</td>
          <td>{meal.date}</td>
          <td>
            <button
              class="bi bi-trash3 btn btn-danger"
              on:click={(event) => deleteOne(event, meal.id)}
            ></button></td
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
        <h1 class="modal-title fs-5" id="exampleModalLabel">Saving Meal</h1>
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
            <label for="user">Border</label>
            <select
              name="user"
              class="form-control"
              bind:value={mealData.userId}
            >
              {#each users as user (user.id)}
                <option value={user.id}>{user.name}</option>
              {/each}
            </select>
          </div>
          <br />
          <div>
            <label for="type">Type</label>
            <select name="type" class="form-control" bind:value={mealData.type}>
              {#each mealTypes as type (type.id)}
                <option value={type.id}>{type.name}</option>
              {/each}
            </select>
          </div>
          <br />
          <div>
            <label for="amount">Amount</label>
            <input
              type="text"
              name="amount"
              id="amount"
              class="form-control"
              bind:value={mealData.amount}
              disabled={offType == mealData.type}
            />
          </div>
          <br />
          <div>
            <label for="date">Date</label>
            <input
              type="date"
              name="date"
              id="date"
              class="form-control"
              bind:value={mealData.date}
            />
          </div>
          <br />
          <div>
            <label for="period">Period</label>
            <select
              name="period"
              class="form-control"
              bind:value={mealData.period}
            >
              {#each periods as period (period.id)}
                <option value={period.id}>{period.name}</option>
              {/each}
            </select>
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

<!-- Modal Two -->

<div
  class="modal fade"
  id="exampleModal2"
  tabindex="-1"
  aria-labelledby="exampleModalLabel"
  aria-hidden="true"
>
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h1 class="modal-title fs-5" id="exampleModalLabel">
          Saving Meal In Range
        </h1>
        <button
          type="button"
          class="btn-close"
          data-bs-dismiss="modal"
          aria-label="Close"
        ></button>
      </div>
      <div class="modal-body">
        <form on:submit|preventDefault={handleRangeMealSubmit}>
          <div>
            <label for="user">Border</label>
            <select
              name="range-user"
              class="form-control"
              bind:value={rangeMealData.userId}
            >
              {#each users as user (user.id)}
                <option value={user.id}>{user.name}</option>
              {/each}
            </select>
          </div>
          <br />
          <div>
            <label for="type">Type</label>
            <select
              name="range-type"
              class="form-control"
              bind:value={rangeMealData.type}
            >
              {#each mealTypes as type (type.id)}
                <option value={type.id}>{type.name}</option>
              {/each}
            </select>
          </div>
          <br />
          <div>
            <label for="amount">Amount</label>
            <input
              type="text"
              name="range-amount"
              id="amount"
              class="form-control"
              bind:value={rangeMealData.amount}
              disabled={offType == rangeMealData.type}
            />
          </div>
          <br />
          <div>
            <label for="startDate">Start Date</label>
            <input
              type="date"
              name="startDate"
              id="startDate"
              class="form-control"
              bind:value={rangeMealData.startDate}
            />
          </div>
          <br />
          <div>
            <label for="endDate">End Date</label>
            <input
              type="date"
              name="endDate"
              id="endDate"
              class="form-control"
              bind:value={rangeMealData.endDate}
            />
          </div>
          <br />
          <div>
            <label for="period">Period</label>
            <select
              name="range-period"
              class="form-control"
              bind:value={rangeMealData.period}
            >
              {#each periods as period (period.id)}
                <option value={period.id}>{period.name}</option>
              {/each}
            </select>
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
