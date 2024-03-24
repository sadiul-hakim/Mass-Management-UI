<script>
  import { push } from "svelte-spa-router";
  import { Authorization } from "./store/stores";

  let loginData = {
    username: "",
    password: "",
  };

  async function login() {
    let formBody = [];
    for (let property in loginData) {
      let encodedKey = encodeURIComponent(property);
      let encodedValue = encodeURIComponent(loginData[property]);
      formBody.push(encodedKey + "=" + encodedValue);
    }
    formBody = formBody.join("&");

    let response = await fetch("http://localhost:9090/login", {
      method: "POST",
      body: formBody,
      headers: {
        "Content-Type": "application/x-www-form-urlencoded;charset=UTF-8",
      },
    });

    let data = await response.json();

    if (response.status === 200) {
      Authorization.set(data);
      localStorage.setItem("authorization", JSON.stringify(data));
      push("/");
    }
  }
</script>

<main class="container mt-4">
  <div class="row mt-4">
    <div class="col-md-5 mx-auto card p-3">
      <h1 class="text-primary text-center">
        <i class="bi bi-person-bounding-box"></i> Login Page
      </h1>
      <form on:submit|preventDefault={login}>
        <div>
          <label for="username">Email</label>
          <input
            type="email"
            name="username"
            class="form-control"
            bind:value={loginData.username}
          />
        </div>
        <div>
          <label for="password">Password</label>
          <input
            type="password"
            name="password"
            class="form-control"
            bind:value={loginData.password}
          />
        </div>
        <br />
        <button class="btn btn-primary">Login</button>
      </form>
    </div>
  </div>
</main>
