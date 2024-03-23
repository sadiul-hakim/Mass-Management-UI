<script>
  import { onMount } from "svelte";
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

  export let navigation = "Home";

  let manager = {};

  onMount(async () => {
    loadManager();
  });

  async function managerChangedEvent() {
    await loadManager();
  }

  // loading section
  async function loadManager() {
    let roleResponse = await fetch(
      "http://localhost:9090/user-role/v1/get-by-role/Manager"
    );
    let managerRole = await roleResponse.json();
    let response = await fetch(
      `http://localhost:9090/user/v1/get-by-role/${managerRole.id}`
    );
    let managerArr = await response.json();
    manager = managerArr[0];
  }
</script>

<div class="header p-4 bg-light">
  <h2>Manager, {manager.name}</h2>
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
    <Manager {manager} on:managerChanged={managerChangedEvent} />
  {:else if navigation === "Meal"}
    <Meal />
  {:else if navigation === "Report"}
    <Report />
  {:else}
    <Home />
  {/if}
</div>
