<script lang="ts">
  import { page } from "$app/stores";
  import { goto } from "$app/navigation";

  interface Client {
    id: string;
    name: string;
    description: string;
    auth_redirect_url: string;
    sso_redirect_url: string;
    config_profile: string; // Storing as string for textarea
    status: "active" | "inactive";
    updated_at: string;
    created_at: string;
  }

  let clientId: string;
  let client: Client | undefined;

  let name: string = "";
  let description: string = "";
  let auth_redirect_url: string = "";
  let sso_redirect_url: string = "";
  let config_profile: string = "";
  let status: "active" | "inactive" = "active";

  $: {
    clientId = $page.params.id;
    // In a real application, you would fetch the client data based on clientId
    // For now, we'll use mock data
    client = mockClients.find((c) => c.id === clientId);

    if (client) {
      name = client.name;
      description = client.description;
      auth_redirect_url = client.auth_redirect_url;
      sso_redirect_url = client.sso_redirect_url;
      config_profile = client.config_profile;
      status = client.status;
    }
  }

  // Mock data for demonstration (should match the structure of the list page's mock data)
  const mockClients: Client[] = [
    {
      id: "client-1",
      name: "Client A",
      description: "Description for Client A",
      auth_redirect_url: "http://localhost:3000/auth-a",
      sso_redirect_url: "http://localhost:3000/sso-a",
      config_profile: JSON.stringify(
        { theme: "dark", features: ["chat"] },
        null,
        2,
      ),
      status: "active",
      updated_at: "2023-01-15T10:00:00Z",
      created_at: "2023-01-01T08:00:00Z",
    },
    {
      id: "client-2",
      name: "Client B",
      description: "Description for Client B",
      auth_redirect_url: "http://localhost:3000/auth-b",
      sso_redirect_url: "http://localhost:3000/sso-b",
      config_profile: JSON.stringify(
        { theme: "light", notifications: true },
        null,
        2,
      ),
      status: "inactive",
      updated_at: "2023-02-20T14:30:00Z",
      created_at: "2023-02-05T11:00:00Z",
    },
    {
      id: "client-3",
      name: "Client C",
      description: "Description for Client C",
      auth_redirect_url: "http://localhost:3000/auth-c",
      sso_redirect_url: "http://localhost:3000/sso-c",
      config_profile: JSON.stringify({ analytics: true }, null, 2),
      status: "active",
      updated_at: "2023-03-25T09:15:00Z",
      created_at: "2023-03-10T07:00:00Z",
    },
  ];

  async function handleUpdate() {
    const updatedClientData = {
      id: clientId,
      name,
      description,
      auth_redirect_url,
      sso_redirect_url,
      config_profile: config_profile ? JSON.parse(config_profile) : null,
      status,
    };

    try {
      // Replace with your actual API endpoint for updating a client
      // const response = await fetch(`/internal/clients.update/${clientId}`, {
      //   method: 'PUT', // Or PATCH, depending on your API
      //   headers: {
      //     'Content-Type': 'application/json'
      //   },
      //   body: JSON.stringify(updatedClientData)
      // });

      // if (response.ok) {
      //   alert('Client updated successfully!');
      //   goto('/admin/clients/list'); // Redirect back to the list page
      // } else {
      //   const errorData = await response.json();
      //   alert(`Failed to update client: ${errorData.message || response.statusText}`);
      // }
      console.log("Updating client (mocked):", updatedClientData);
      alert("Client updated successfully (mocked)!");
      goto("/oauth/clients/list"); // Redirect back to the list page
    } catch (error) {
      console.error("Error updating client:", error);
      alert("An error occurred while updating the client.");
    }
  }

  function handleCancel() {
    goto("/oauth/clients/list"); // Go back to the client list page
  }
</script>

<div class="container">
  <h1>Edit Client: {client?.name || "Loading..."}</h1>
  {#if client}
    <form onsubmit={handleUpdate}>
      <div>
        <label for="name">Name:</label>
        <input type="text" id="name" bind:value={name} required />
      </div>

      <div>
        <label for="description">Description:</label>
        <textarea id="description" bind:value={description}></textarea>
      </div>

      <div>
        <label for="auth_redirect_url">Auth Redirect URL:</label>
        <input
          type="text"
          id="auth_redirect_url"
          bind:value={auth_redirect_url}
          required
        />
      </div>

      <div>
        <label for="sso_redirect_url">SSO Redirect URL:</label>
        <input
          type="text"
          id="sso_redirect_url"
          bind:value={sso_redirect_url}
          required
        />
      </div>

      <div>
        <label for="config_profile">Config Profile (JSON):</label>
        <textarea
          id="config_profile"
          bind:value={config_profile}
          placeholder={`e.g., {"key": "value"}`}
        ></textarea>
      </div>

      <div>
        <label for="status">Status:</label>
        <select id="status" bind:value={status}>
          <option value="active">Active</option>
          <option value="inactive">Inactive</option>
        </select>
      </div>

      <div class="button-group">
        <button type="submit" class="update-button">Update Client</button>
        <button type="button" class="cancel-button" onclick={handleCancel}
          >Cancel</button
        >
      </div>
    </form>
  {:else}
    <p>Client not found.</p>
  {/if}
</div>

<style>
  .container {
    max-width: 600px;
    margin: 50px auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    background-color: #fff;
  }

  h1 {
    text-align: center;
    color: #333;
    margin-bottom: 30px;
  }

  form div {
    margin-bottom: 15px;
  }

  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
    color: #555;
  }

  input[type="text"],
  textarea,
  select {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    box-sizing: border-box;
    font-size: 16px;
  }

  textarea {
    resize: vertical;
    min-height: 100px;
  }

  .button-group {
    display: flex;
    justify-content: space-between;
    gap: 10px;
    margin-top: 20px;
  }

  .button-group button {
    flex: 1;
    padding: 12px;
    border: none;
    border-radius: 4px;
    font-size: 18px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .update-button {
    background-color: #df00a9;
    color: white;
  }

  .update-button:hover {
    background-color: #a3007a;
  }

  .cancel-button {
    background-color: #6c757d;
    color: white;
  }

  .cancel-button:hover {
    background-color: #5a6268;
  }
</style>
