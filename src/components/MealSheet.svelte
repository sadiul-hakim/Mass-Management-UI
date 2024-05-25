<script>
  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import { domain_api, login_url, get_meal_sheet } from "../util/apis";
  import { onMount } from "svelte";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  let mealSheet = {};
  let settings = {
    dates: [],
  };

  onMount(async () => {
    getMealSheet();
    generateSettings();
  });

  function generateSettings() {
    let date = new Date();
    let today = date.getDate();

    for (let i = 0; i < today; i++) {
      settings.dates[i] = i + 1;
    }
  }

  async function getMealSheet() {
    let response = await fetch(`${domain_api}${get_meal_sheet}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    mealSheet = await response.json();
    console.log(mealSheet);
  }
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">Meal Sheet</h3>
  <table class="mt-2 text-center sheet-table">
    <tr>
      <th class="p-2">Name</th>
      {#each settings.dates as date, index (index)}
        <th class="p-2">{date}</th>
      {/each}
    </tr>
    {#each Object.entries(mealSheet) as [key, values]}
      <tr class="p-2">
        <td class="p-2">{key}</td>
        {#each Object.entries(mealSheet[key]) as [newKey, newValues]}
          <th class="p-2">{newValues}</th>
        {/each}
      </tr>
    {/each}
  </table>
</div>
