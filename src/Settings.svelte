<script>
  import { toasts, ToastContainer, FlatToast } from "svelte-toasts";
  import {
    domain_api,
    login_url,
    get_setting_api,
    get_all_transaction_type_api,
    get_all_user_status_api,
    get_all_meal_types_api,
    get_all_user_role_api,
    save_setting,
    delete_setting,
  } from "./util/apis";
  import { onMount } from "svelte";

  import { Authorization } from "./store/stores";
  import { push } from "svelte-spa-router";

  let authorization = {};
  let setting = {
    id: 0,
    name: "setting",
    properties: {
      TransactionTypeMarket: 0,
      TransactionTypeElectricityBill: 0,
      TransactionTypeBorderDeposit: 0,
      UserStatusActive: 0,
      MealTypeOff: 0,
      MealTypeExtra: 0,
      UserRoleBorder: 0,
      UserRoleManager: 0,
    },
    excludeTransactionTypes: [],
  };
  let transactionTypes = [];
  let userStatus = [];
  let mealType = [];
  let userRole = [];

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  onMount(async () => {
    getSetting();
    loadAllTransactionTypes();
    loadAllUserStatus();
    loadAllMealTypes();
    loadAllUserRole();
  });

  async function getSetting() {
    let response = await fetch(domain_api + get_setting_api + "?name=setting", {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });

    if (response.status === 200) {
      setting = await response.json();
      console.log(setting);
    }
  }

  async function saveSetting() {
    let response = await fetch(domain_api + save_setting, {
      method: "POST",
      headers: {
        Authorization: "Bearer " + authorization.token,
        "Content-Type": "application/json",
      },
      body: JSON.stringify(setting),
    });

    console.log(response.status);

    if (response === 200) {
      showToast();
    }

    setting = await response.json();
  }

  async function deleteSetting() {
    let response = await fetch(domain_api + delete_setting + "?name=setting", {
      method: "DELETE",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });

    getSetting();
  }

  // loading
  async function loadAllTransactionTypes() {
    let response = await fetch(domain_api + get_all_transaction_type_api, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    transactionTypes = await response.json();
  }

  async function loadAllUserStatus() {
    let response = await fetch(domain_api + get_all_user_status_api, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    userStatus = await response.json();
  }

  async function loadAllMealTypes() {
    let response = await fetch(domain_api + get_all_meal_types_api, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    mealType = await response.json();
  }

  async function loadAllUserRole() {
    let response = await fetch(domain_api + get_all_user_role_api, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    userRole = await response.json();
  }

  // Toast Message
  const showToast = () => {
    const toast = toasts.add({
      title: "Setting",
      description: "Setting is saved successfully.",
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

<div class="container h-100 mt-4 p-3 card">
  <div class="d-flex justify-content-end">
    <button class="btn btn-primary" on:click={() => push("/")}
      ><i class="bi bi-box-arrow-in-left"></i> Home</button
    >
  </div>
  <h2>Setting</h2>
  <form class="row mt-2" on:submit|preventDefault={saveSetting}>
    <div class="col-md-6">
      <!-- Market -->
      <div>
        <label for="market">Market Type</label>
        <select
          class="form-control"
          bind:value={setting.properties.TransactionTypeMarket}
        >
          {#each transactionTypes as type (type.id)}
            <option value={type.id}>{type.title}</option>
          {/each}
        </select>
      </div>
      <br />
      <!-- Electricity Bill -->
      <div>
        <label for="electricity-bill">Electricity Bill Type</label>
        <select
          class="form-control"
          bind:value={setting.properties.TransactionTypeElectricityBill}
        >
          {#each transactionTypes as type (type.id)}
            <option value={type.id}>{type.title}</option>
          {/each}
        </select>
      </div>
      <br />
      <!-- Border Deposite -->
      <div>
        <label for="border-deposite">Border Deposite Type</label>
        <select
          class="form-control"
          bind:value={setting.properties.TransactionTypeBorderDeposit}
        >
          {#each transactionTypes as type (type.id)}
            <option value={type.id}>{type.title}</option>
          {/each}
        </select>
      </div>
      <br />
      <!-- Active User Status -->
      <div>
        <label for="active-user">Active User Status</label>
        <select
          class="form-control"
          bind:value={setting.properties.UserStatusActive}
        >
          {#each userStatus as status (status.id)}
            <option value={status.id}>{status.status}</option>
          {/each}
        </select>
      </div>
      <br />
      <!-- Exclude Option -->
      <div>
        <label for="exclude Types">Exculde From Extra Cost</label>
        <select
          class="form-control"
          bind:value={setting.excludeTransactionTypes}
          multiple
        >
          {#each transactionTypes as type (type.id)}
            <option value={type.id}>{type.title}</option>
          {/each}
        </select>
      </div>
      <br />
    </div>
    <div class="col-md-6">
      <!-- Off Meal -->
      <div>
        <label for="off-meal">Meal Type Off</label>
        <select
          class="form-control"
          bind:value={setting.properties.MealTypeOff}
        >
          {#each mealType as type (type.id)}
            <option value={type.id}>{type.name}</option>
          {/each}
        </select>
      </div>
      <br />
      <!-- Extra Meal -->
      <div>
        <label for="off-meal">Meal Type Extra</label>
        <select
          class="form-control"
          bind:value={setting.properties.MealTypeExtra}
        >
          {#each mealType as type (type.id)}
            <option value={type.id}>{type.name}</option>
          {/each}
        </select>
      </div>
      <br />
      <!-- User Role Border -->
      <div>
        <label for="role-border">User Role Border</label>
        <select
          class="form-control"
          bind:value={setting.properties.UserRoleBorder}
        >
          {#each userRole as role (role.id)}
            <option value={role.id}>{role.role}</option>
          {/each}
        </select>
      </div>
      <br />
      <!-- User Role Manager -->
      <div>
        <label for="role-manager">User Role Manager</label>
        <select
          class="form-control"
          bind:value={setting.properties.UserRoleManager}
        >
          {#each userRole as role (role.id)}
            <option value={role.id}>{role.role}</option>
          {/each}
        </select>
      </div>
      <br />
    </div>

    <div>
      <button class="btn btn-primary">Save</button>
      <button class="btn btn-danger" type="button" on:click={deleteSetting}
        >Reset Setting</button
      >
    </div>
  </form>
</div>

<!-- Toast Message -->

<ToastContainer placement="bottom-right" let:data>
  <FlatToast {data} />
  <!-- Provider template for your toasts -->
</ToastContainer>
