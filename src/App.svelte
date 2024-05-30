<script>
  import { onMount } from "svelte";
  import Router from "svelte-spa-router";
  import MassManagement from "./MassManagement.svelte";
  import Login from "./Login.svelte";
  import NotFount from "./NotFount.svelte";
  import Settings from "./Settings.svelte";
  import { push } from "svelte-spa-router";

  import { Authorization } from "./store/stores";
  import ChangePassword from "./ChangePassword.svelte";
  import { domain_api, login_url, validate_token } from "./util/apis";

  onMount(async () => {
    let authorization = localStorage.getItem("authorization");
    if (
      authorization !== undefined ||
      authorization !== null ||
      authorization.length !== 0
    ) {
      let auth = JSON.parse(authorization);
      validateToken(auth.token);
      Authorization.set(auth);
    } else {
      push("/login");
    }
  });

  async function validateToken(token) {
    let response = await fetch(`${domain_api}${validate_token}`, {
      method: "POST",
      body: JSON.stringify({ token: token }),
      headers: {
        "Content-Type": "application/json",
      },
    });

    let data = await response.json();
    console.log(data);

    if (response.status !== 200) {
      localStorage.setItem("authorization", "");
      push(login_url);
    }
  }

  let routes = {
    "/": MassManagement,
    "/login": Login,
    "/settings": Settings,
    "/change-password": ChangePassword,
    "*": NotFount,
  };
</script>

<main>
  <Router {routes} />
</main>
