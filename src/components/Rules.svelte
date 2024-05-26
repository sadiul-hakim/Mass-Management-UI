<script>
  import { onMount } from "svelte";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import {
    add_rule,
    delete_rule,
    domain_api,
    get_all_rules,
    login_url,
  } from "../util/apis";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  let rules = [];
  let ruleData = {
    id: 0,
    rule: "",
    date: "",
  };

  onMount(async () => {
    loadAll();
  });

  async function loadAll() {
    let response = await fetch(`${domain_api}${get_all_rules}`, {
      method: "GET",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });
    rules = await response.json();
    console.log(rules);
  }

  async function handleSubmit() {
    let response = await fetch(`${domain_api}${add_rule}`, {
      method: "POST",
      body: JSON.stringify(ruleData),
      headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + authorization.token,
      },
    });
    await loadAll();
    clearData();
  }

  function clearData() {
    ruleData = {
      id: 0,
      rule: "",
      date: "",
    };
  }

  async function deleteOne(event, id) {
    let response = await fetch(`${domain_api}${delete_rule}${id}`, {
      method: "DELETE",
      headers: {
        Authorization: "Bearer " + authorization.token,
      },
    });

    let data = await response.json();
    console.log(data);

    await loadAll();
  }
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">Rules</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Rule</th>
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
      {#each rules as rule, index (index)}
        <tr>
          <td>{index + 1}</td>
          <td>{rule.rule}</td>
          <td>{rule.date}</td>
          <td>
            <button
              class="btn btn-danger"
              on:click={(event) => deleteOne(event, rule.id)}
              ><i class="bi bi-trash3"></i></button
            >
          </td>
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
            <label for="amount">Rule</label>
            <input
              type="text"
              name="rule"
              id="rule"
              class="form-control"
              bind:value={ruleData.rule}
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
