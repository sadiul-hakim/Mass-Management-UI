<script>
  import { onMount } from "svelte";
  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import {
    change_password_url,
    domain_api,
    get_role_by_role_manager,
    get_user_by_role,
    login_url,
    settings_url,
  } from "../util/apis";

  import TransactionType from "./TransactionType.svelte";
  import MealType from "./MealType.svelte";
  import UserRole from "./UserRole.svelte";
  import UserStatus from "./UserStatus.svelte";
  import Period from "./Period.svelte";
  import Income from "./Income.svelte";
  import Cost from "./Cost.svelte";
  import Border from "./Border.svelte";
  import Home from "./Home.svelte";
  import Manager from "./Manager.svelte";
  import Meal from "./Meal.svelte";
  import Report from "./Report.svelte";
  import Mail from "./Mail.svelte";
  import OldReport from "./OldReport.svelte";
  import Rules from "./Rules.svelte";
  import MealSheet from "./MealSheet.svelte";

  export let navigation = "Home";

  let manager = {};
  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  onMount(async () => {
    loadManager();
  });

  // loading section
  async function loadManager() {
    let roleResponse = await fetch(`${domain_api}${get_role_by_role_manager}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });

    if (roleResponse.status !== 200) {
      return;
    }

    let managerRole = await roleResponse.json();
    let response = await fetch(
      `${domain_api}${get_user_by_role}${managerRole.id}`,
      {
        method: "GET",
        headers: {
          Authorization: "Bearer " + authorization.token,
        },
      }
    );

    if (response.status !== 200) {
      manager = {
        name: "Manager",
      };
      return;
    }

    let managerArr = await response.json();
    manager = managerArr[0];
  }

  function logout() {
    Authorization.set(undefined);
    localStorage.setItem("authorization", "");
    push(login_url);
  }

  function goToSettings() {
    push(settings_url);
  }

  function goToChangePassword() {
    push(change_password_url);
  }
</script>

<div class="header p-4 bg-light d-flex justify-content-between">
  <h2>Manager, {manager.name}</h2>
  <ul class="navbar-nav mb-2 mb-lg-0">
    <li class="nav-item dropdown">
      <a
        class="nav-link dropdown-toggle"
        href="#"
        role="button"
        data-bs-toggle="dropdown"
        aria-expanded="false"
      >
        <i class="bi bi-gear-wide-connected"></i>
      </a>
      <ul class="dropdown-menu">
        <li class="dropdown-item" on:click={goToSettings}>
          <i class="bi bi-gear"></i> Settings
        </li>
        <li class="dropdown-item" on:click={goToChangePassword}>
          <i class="bi bi-file-earmark-lock2"></i> Change Password
        </li>
        <li class="dropdown-item">
          <i class="bi bi-file-earmark-word"></i>
          <a href="resource/MMS.docx">Manual</a>
        </li>
        <li><hr class="dropdown-divider" /></li>
        <li class="dropdown-item" on:click={logout}>
          <i class="bi bi-box-arrow-right"></i> Logout
        </li>
      </ul>
    </li>
  </ul>
</div>
<div class="p-4 h-100 overflow-y-hidden">
  {#if navigation === "Home"}
    <Home />
  {:else if navigation === "Transaction Type"}
    <TransactionType />
  {:else if navigation === "Meal Type"}
    <MealType />
  {:else if navigation === "User Role"}
    <UserRole />
  {:else if navigation === "User Status"}
    <UserStatus />
  {:else if navigation === "Period"}
    <Period />
  {:else if navigation === "Income"}
    <Income />
  {:else if navigation === "Cost"}
    <Cost />
  {:else if navigation === "Border"}
    <Border />
  {:else if navigation === "Manager"}
    <Manager {manager} />
  {:else if navigation === "Meal"}
    <Meal />
  {:else if navigation === "MealSheet"}
    <MealSheet />
  {:else if navigation === "Report"}
    <Report />
  {:else if navigation === "Mail"}
    <Mail />
  {:else if navigation === "OldReport"}
    <OldReport />
  {:else if navigation === "Rules"}
    <Rules />
  {:else}
    <Home />
  {/if}
</div>
