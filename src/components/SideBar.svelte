<script>
  import { createEventDispatcher } from "svelte";
  let dispatch = createEventDispatcher();
</script>

<div class="brand-container p-4">
  <a href="/" class="navbar-brand fs-3 text-light"
    ><i class="bi bi-calendar-day-fill"></i> MMS</a
  >
</div>
<div class="sidebar__menu mt-5">
  <ul class="list-group">
    <li class="list-group-item text-light" on:click={() => dispatch("home")}>
      <i class="bi bi-house-door-fill"></i> Home
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("income")}>
      <i class="bi bi-calendar-plus-fill"></i> Income
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("cost")}>
      <i class="bi bi-clipboard-minus-fill"></i> Cost
    </li>
    <li
      class="list-group-item text-light"
      on:click={() => dispatch("transactionType")}
    >
      <i class="bi bi-card-checklist"></i> Transaction Type
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("meal")}>
      <i class="bi bi-basket-fill"></i> Meal
    </li>
    <li
      class="list-group-item text-light"
      on:click={() => dispatch("mealType")}
    >
      <i class="bi bi-card-list"></i> Meal Type
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("manager")}>
      <i class="bi bi-person-circle"></i> Manager
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("border")}>
      <i class="bi bi-people-fill"></i> Border
    </li>
    <li
      class="list-group-item text-light"
      on:click={() => dispatch("userRole")}
    >
      <i class="bi bi-person-check-fill"></i> User Role
    </li>
    <li
      class="list-group-item text-light"
      on:click={() => dispatch("userStatus")}
    >
      <i class="bi bi-person-check"></i> User Status
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("period")}>
      <i class="bi bi-alarm-fill"></i> Period
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("report")}>
      <i class="bi bi-file-earmark-excel-fill"></i> Report
    </li>
    <li class="list-group-item text-light" on:click={() => dispatch("mail")}>
      <i class="bi bi-chat-left-text"></i> Mail
    </li>
  </ul>
</div>
