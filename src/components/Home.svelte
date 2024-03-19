<script>
  import { onMount } from "svelte";
  let totals = {};
  let borderInfo = [];
  let tableHeaders = [];
  // Variables ends here
  onMount(async () => {
    loadTotals();
    loadBorderInfo();
  });
  // saving of types

  async function loadTotals() {
    let response = await fetch("http://localhost:9090/home/v1/totals");
    totals = await response.json();
  }

  async function loadBorderInfo() {
    let response = await fetch("http://localhost:9090/home/v1/border-info");
    borderInfo = await response.json();
    console.log(borderInfo);
    if (borderInfo.length !== 0) {
      let info = borderInfo[0];
      tableHeaders = Object.keys(info);
    }
  }
</script>

<div class="h-100 p-3">
  <div class="row d-flex justify-content-between">
    <div class="card col-md-3 m-1 text-center p-2">
      <h4>Total Income</h4>
      <h5>{totals.income}</h5>
    </div>
    <div class="card col-md-3 m-1 text-center p-2">
      <h4>Total Cost</h4>
      <h5>{totals.cost}</h5>
    </div>
    <div class="card col-md-3 m-1 text-center p-2">
      <h4>Total Borders</h4>
      <h5>{totals.user}</h5>
    </div>
  </div>
  <div class="row card p-3 mt-4">
    <table class="table">
      <thead>
        <tr>
          {#each tableHeaders as header, index (index)}
            <th>{header}</th>
          {/each}
        </tr>
      </thead>
      <tbody>
        {#each borderInfo as info, index (index)}
          <tr>
            {#each tableHeaders as header, ind (ind)}
              <td>{info[header]}</td>
            {/each}
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>
