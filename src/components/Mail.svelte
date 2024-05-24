<script>
  import { toasts, ToastContainer, FlatToast } from "svelte-toasts";

  import { Authorization } from "../store/stores";
  import { push } from "svelte-spa-router";
  import { domain_api, login_url, send_mail } from "../util/apis";

  let authorization = {};

  Authorization.subscribe((auth) => {
    authorization = auth;
  });

  if (authorization === undefined) {
    push(login_url);
  }

  let toMail = "";
  let mailData = {
    subject: "",
    mail: "",
  };

  async function sendMail() {
    let response = await fetch(`${domain_api}${send_mail}${toMail}`, {
      method: "POST",
      body: JSON.stringify(mailData),
      headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + authorization.token,
      },
    });

    let data = await response.json();
    console.log(data);
    if (response.status === 200) {
      showToast();
    }
  }

  // Toast Message
  const showToast = () => {
    const toast = toasts.add({
      title: "Mail Status",
      description: "Mail send successfully.",
      duration: 10000, // 0 or negative to avoid auto-remove
      placement: "top-right",
      theme: "dark",
      type: "success",
      onClick: () => {},
      onRemove: () => {},
      // component: BootstrapToast, // allows to override toast component/template per toast
    });

    // toast.remove()
  };
</script>

<div class="card h-100 overflow-y-auto p-3">
  <h3 class="mb-2">Mail</h3>
  <div>
    <form on:submit|preventDefault={sendMail}>
      <div>
        <label for="toMail">To</label>
        <input
          type="email"
          name="to"
          id="to"
          class="form-control"
          bind:value={toMail}
        />
      </div>
      <br />
      <div>
        <label for="subject">Subject</label>
        <input
          type="text"
          name="subject"
          id="subject"
          class="form-control"
          bind:value={mailData.subject}
        />
      </div>
      <br />
      <div>
        <label for="mail">Mail</label>
        <textarea class="form-control" rows="10" bind:value={mailData.mail}
        ></textarea>
      </div>
      <br />
      <button class="btn btn-primary">Send</button>
    </form>
  </div>
</div>

<!-- Toast Message -->

<ToastContainer placement="bottom-right" let:data>
  <FlatToast {data} />
  <!-- Provider template for your toasts -->
</ToastContainer>
