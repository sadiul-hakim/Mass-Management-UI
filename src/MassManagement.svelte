<script>
  import SideBar from "./components/SideBar.svelte";
  import RightSide from "./components/RightSide.svelte";

  let navigation = "Home";

  function handleNavigation(nav) {
    navigation = nav;
  }
</script>

<main class="container-fluid main-content">
  <div class="row h-100">
    <div class="col-md-2 bg-dark text-white h-100 p-0 m-0 d-flex flex-column">
      <SideBar
        on:home={() => handleNavigation("Home")}
        on:transactionType={() => handleNavigation("Transaction Type")}
        on:mealType={() => handleNavigation("Meal Type")}
        on:userRole={() => handleNavigation("User Role")}
        on:userStatus={() => handleNavigation("User Status")}
        on:period={() => handleNavigation("Period")}
        on:income={() => handleNavigation("Income")}
        on:cost={() => handleNavigation("Cost")}
        on:border={() => handleNavigation("Border")}
        on:manager={() => handleNavigation("Manager")}
        on:meal={() => handleNavigation("Meal")}
        on:mealSheet={() => handleNavigation("MealSheet")}
        on:report={() => handleNavigation("Report")}
        on:mail={() => handleNavigation("Mail")}
        on:oldReport={() => handleNavigation("OldReport")}
        on:rules={() => handleNavigation("Rules")}
      />
    </div>
    <div class="col-md-10 p-0 m-0 d-flex flex-column h-100">
      <RightSide {navigation} />
    </div>
  </div>
</main>
