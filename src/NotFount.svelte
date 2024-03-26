<script>
  import { push } from "svelte-spa-router";
  import { home_url } from "./util/apis";

  function redirect() {
    push(home_url);
  }
</script>

<div class="container mt-4">
  <div class="row d-flex justify-content-center align-items-center mt-4">
    <div
      class="col-md-8 d-flex flex-column justify-content-center align-items-center mt-4"
    >
      <!-- <img src="image/404.png" alt="Page not found." /> -->
      <h1 class="">404</h1>
      <p>This page is not found.</p>
      <button class="btn btn-primary" on:click={redirect}>Home</button>
    </div>
  </div>
</div>
