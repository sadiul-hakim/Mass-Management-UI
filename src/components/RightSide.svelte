<script>
  import TransactionType from "./TransactionType.svelte";
  import MealType from "./MealType.svelte";
  import UserRole from "./UserRole.svelte";
  import UserStatus from "./UserStatus.svelte";
  import Period from "./Period.svelte";
  import Income from "./Income.svelte";
  import Cost from "./Cost.svelte";

  export let navigation = "Home";

  export let manager = "Hakim";
</script>

<div class="header p-4 bg-light">
  <h2>Manager, {manager}</h2>
</div>
<div class="rightSide__content p-4">
  {#if navigation === "Home"}
    <h1>Home</h1>
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
  {:else}
    <h1>Home</h1>
  {/if}
</div>
