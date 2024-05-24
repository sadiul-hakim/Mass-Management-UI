<script>
  import { push } from "svelte-spa-router";
</script>

<div class="container mt-5 card p-3">
  <div class="d-flex justify-content-end">
    <button class="btn btn-primary" on:click={() => push("/")}
      ><i class="bi bi-box-arrow-in-left"></i> Home</button
    >
  </div>

  <div class="row">
    <h1>Change Admin Password</h1>
    <form class="col-md-7 mt-2">
      <div>
        <label for="current_password">Current Password</label>
        <input type="password" name="current_password" class="form-control" />
      </div>
      <br />
      <div>
        <label for="new_password">New Password</label>
        <input type="password" name="new_password" class="form-control" />
      </div>
      <br />
      <div>
        <label for="confirm_password">Confirm Password</label>
        <input type="password" name="confirm_password" class="form-control" />
      </div>
      <br />
      <input type="submit" value="Change" class="btn btn-primary" />
    </form>
  </div>

  <!-- Change border password -->

  <div class="row mt-4">
    <h1>Change Border Password</h1>
    <form class="col-md-7 mt-2">
      <div>
        <label for="border">Border</label>
        <select name="border" class="form-control">
          <option>Border One</option>
          <option>Border Two</option>
        </select>
      </div>
      <br />
      <div>
        <label for="current_password">Current Password</label>
        <input type="password" name="current_password" class="form-control" />
      </div>
      <br />
      <div>
        <label for="new_password">New Password</label>
        <input type="password" name="new_password" class="form-control" />
      </div>
      <br />
      <div>
        <label for="confirm_password">Confirm Password</label>
        <input type="password" name="confirm_password" class="form-control" />
      </div>
      <br />
      <input type="submit" value="Change" class="btn btn-primary" />
    </form>
  </div>
</div>

<style></style>
