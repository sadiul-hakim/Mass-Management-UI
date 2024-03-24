<script>
  import { onMount } from "svelte";
  import Router from "svelte-spa-router";
  import MassManagement from "./MassManagement.svelte";
  import Login from "./Login.svelte";
  import NotFount from "./NotFount.svelte";
  import { push } from "svelte-spa-router";

  import { Authorization } from "./store/stores";

  onMount(() => {
    let authorization = localStorage.getItem("authorization");
    if (
      authorization !== undefined ||
      authorization !== null ||
      authorization.length !== 0
    ) {
      Authorization.set(JSON.parse(authorization));
    } else {
      push("/login");
    }
  });

  let routes = {
    "/": MassManagement,
    "/login": Login,
    "*": NotFount,
  };
</script>

<main>
  <Router {routes} />
</main>
