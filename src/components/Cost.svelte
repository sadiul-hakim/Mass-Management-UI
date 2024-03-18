<script>
  import { onMount } from "svelte";
  let types = [];
  let costs = [];
  let costData = {
    id: 0,
    type: "",
    amount: 0,
  };
  // Variables ends here
  onMount(async () => {
    loadAll();
    loadAllTypes();
  });
  // on page load functionality
  async function handleSubmit() {
    let response = await fetch("http://localhost:9090/cost/v1/add", {
      method: "POST",
      body: JSON.stringify(costData),
      headers: {
        "Content-Type": "application/json",
      },
    });
    await loadAll();
    clearData();
    console.log(response.status);
  }
  // saving of types

  async function loadAll() {
    let response = await fetch("http://localhost:9090/cost/v1/get-all");
    costs = await response.json();
    console.log(costs);
  }

  async function loadAllTypes() {
    let response = await fetch(
      "http://localhost:9090/transaction-type/v1/get-all"
    );
    types = await response.json();
  }
  // loading all types
  async function deleteOne(event, id) {
    let response = await fetch(`http://localhost:9090/cost/v1/delete/${id}`, {
      method: "DELETE",
    });
    await loadAll();
  }
  // delete type
  function clearData() {
    costData = {
      id: 0,
      type: "",
      amount: 0,
    };
  }
  // clearing local form data
</script>

<div class="card h-100 p-3">
  <h3 class="mb-2">Cost</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Type</th>
        <th scope="col">Amount</th>
        <th scope="col">Date</th>
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
      {#each costs as cost, index (cost.id)}
        <tr>
          <th scope="row">{index + 1}</th>
          <td>{cost.type.title}</td>
          <td>{cost.amount}</td>
          <td>{cost.date}</td>
          <td
            ><i class="bi bi-pencil-square"></i>&nbsp;
            <i
              class="bi bi-trash3"
              on:click={(event) => deleteOne(event, cost.id)}
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
        <h1 class="modal-title fs-5" id="exampleModalLabel">Saving Costs</h1>
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
            <label for="type">Type</label>
            <select
              name="costType"
              class="form-control"
              bind:value={costData.type}
            >
              {#each types as type (type.id)}
                <option value={type.id}>{type.title}</option>
              {/each}
            </select>
          </div>
          <br />
          <div>
            <label for="amount">Amount</label>
            <input
              type="number"
              name="amount"
              id="amount"
              class="form-control"
              bind:value={costData.amount}
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
