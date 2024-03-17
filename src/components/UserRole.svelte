<div class="card h-100 p-3">
  <h3 class="mb-2">User Role</h3>
  <table class="table">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Role</th>
        <th scope="col">Description</th>
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
      <tr>
        <th scope="row">1</th>
        <td>Manager</td>
        <td>This role means the user is a manager.</td>
        <td
          ><i class="bi bi-pencil-square"></i>&nbsp;
          <i class="bi bi-trash3"></i></td
        >
      </tr>
      <tr>
        <th scope="row">2</th>
        <td>Border</td>
        <td>This role means this user is a border.</td>
        <td
          ><i class="bi bi-pencil-square"></i>&nbsp;
          <i class="bi bi-trash3"></i></td
        >
      </tr>
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
        <h1 class="modal-title fs-5" id="exampleModalLabel">Saving Role</h1>
        <button
          type="button"
          class="btn-close"
          data-bs-dismiss="modal"
          aria-label="Close"
        ></button>
      </div>
      <div class="modal-body">
        <form action="#">
          <div>
            <label for="title">Role</label>
            <input type="text" name="role" id="role" class="form-control" />
          </div>
          <br />
          <div>
            <label for="description">Description</label>
            <textarea name="description" id="description" class="form-control"
            ></textarea>
          </div>
          <br />
          <button type="button" class="btn btn-success">Save</button>
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
