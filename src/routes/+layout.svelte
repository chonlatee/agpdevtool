<script lang="ts">
  import "../app.css";
  import { page } from "$app/state";
  import { goto } from "$app/navigation";

  let { children } = $props();

  // Derive state reactively from the page URL
  let isOAuthPath = $derived(page.url.pathname.startsWith("/oauth"));
  // showSidebar is derived to reactively control the sidebar's visibility
  // based on whether the current path is within the OAuth section.
  let showSidebar = $derived(isOAuthPath);

  // showClientsSubMenu is a mutable state initialized based on isOAuthPath.
  // It allows the submenu to be open by default for OAuth paths,
  // but can then be independently toggled by user interaction.
  let showClientsSubMenu = $state(false);
  let hasUserInteractedWithClientsSubMenu = $state(false);

  $effect(() => {
    if (!hasUserInteractedWithClientsSubMenu) {
      showClientsSubMenu = isOAuthPath;
    }
  });

  function navigateTo(path: string) {
    goto(path);
  }
  function toggleClientsSubMenu() {
    showClientsSubMenu = !showClientsSubMenu;
    hasUserInteractedWithClientsSubMenu = true;
  }
</script>

<div class="flex flex-col min-h-screen">
  <!-- Header -->
  <header
    class="bg-primary text-white p-4 shadow-md flex justify-between items-center"
  >
    <a href="/" class="text-2xl font-bold text-white hover:underline">AGP Dev Tools</a>
    <nav>
      <ul class="flex space-x-6">
        <li>
          <a href="/oauth/clients" class="hover:underline">OAuth</a>
        </li>
        <li><a href="/payment" class="hover:underline">Payment</a></li>
        <li><a href="/dryrun" class="hover:underline">Dryrun</a></li>
      </ul>
    </nav>
  </header>

  <div class="flex flex-1">
    <!-- Sidebar -->
    <aside
      class="bg-gray-800 text-white w-64 p-4 transform transition-transform duration-300 ease-in-out"
      class:translate-x-0={showSidebar}
      class:-translate-x-full={!showSidebar}
      class:absolute={!showSidebar}
      class:relative={showSidebar}
      class:z-20={showSidebar}
      class:h-full={true}
      class:min-h-screen={true}
    >
      <nav>
        <ul>
          <li class="mb-2">
            <button
              onclick={toggleClientsSubMenu}
              class="w-full text-left py-2 px-4 rounded hover:bg-gray-700 flex justify-between items-center"
            >
              Clients
              <span
                class="transform transition-transform duration-200"
                class:rotate-90={showClientsSubMenu}>></span
              >
            </button>
            {#if showClientsSubMenu}
              <ul class="ml-4 mt-1">
                <li class="mb-1">
                  <a
                    href="/oauth/clients"
                    class="block py-2 px-4 rounded hover:bg-gray-700"
                    onclick={() => navigateTo("/oauth/clients")}>Create</a
                  >
                </li>
                <li class="mb-1">
                  <a
                    href="/oauth/clients/list"
                    class="block py-2 px-4 rounded hover:bg-gray-700"
                    onclick={() => navigateTo("/oauth/clients/list")}>List</a
                  >
                </li>
              </ul>
            {/if}
          </li>
          <li class="mb-2">
            <a
              href="/oauth/users"
              class="block py-2 px-4 rounded hover:bg-gray-700"
              onclick={() => navigateTo("/oauth/users")}>Users</a
            >
          </li>
        </ul>
      </nav>
    </aside>

    <!-- Main Content -->
    <main class="flex-1 p-6 bg-gray-100">
      {@render children()}
    </main>
  </div>
</div>

<style>
  /* Custom styles for primary color */
  .bg-primary {
    background-color: #df00a9;
  }
</style>
