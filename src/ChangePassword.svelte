<script>
  import { push } from "svelte-spa-router";

  import { Authorization } from "./store/stores";

  import { change_password_api, domain_api, login_url } from "./util/apis";

  let authorization = {};
  let adminPasswordData = {
    userId: 0,
    currentPassword: "",
    newPassword: "",
    confirmPassword: "",
  };

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  // state

  async function change() {
    let response = await fetch(`${domain_api}${change_password_api}`, {
      method: "POST",
      body: JSON.stringify(adminPasswordData),
      headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + authorization.token,
      },
    });

    let data = await response.json();
    console.log(data);

    if (response.status === 200) {
      logout();
    }
  }

  function logout() {
    Authorization.set(undefined);
    localStorage.setItem("authorization", "");
    push(login_url);
  }
</script>

<div class="container mt-5 card p-3">
  <div class="d-flex justify-content-end">
    <button class="btn btn-primary" on:click={() => push("/")}
      ><i class="bi bi-box-arrow-in-left"></i> Home</button
    >
  </div>

  <div class="row">
    <h1>Change Admin Password</h1>
    <form class="col-md-7 mt-2" on:submit|preventDefault={change}>
      <div>
        <label for="current_password">Current Password</label>
        <input
          type="password"
          name="current_password"
          class="form-control"
          bind:value={adminPasswordData.currentPassword}
        />
      </div>
      <br />
      <div>
        <label for="new_password">New Password</label>
        <input
          type="password"
          name="new_password"
          class="form-control"
          bind:value={adminPasswordData.newPassword}
        />
      </div>
      <br />
      <div>
        <label for="confirm_password">Confirm Password</label>
        <input
          type="password"
          name="confirm_password"
          class="form-control"
          bind:value={adminPasswordData.confirmPassword}
        />
      </div>
      <br />
      <input type="submit" value="Change" class="btn btn-primary" />
    </form>
  </div>
</div>

<style></style>
