<script lang="ts">
  import { goto } from "$app/navigation";

  interface Client {
    id: string;
    name: string;
    description: string;
    status: "active" | "inactive";
    updated_at: string;
    created_at: string;
  }

  // Mock data for demonstration
  let clients: Client[] = [
    {
      id: "client-1",
      name: "Client A",
      description: "Description for Client A",
      status: "active",
      updated_at: "2023-01-15T10:00:00Z",
      created_at: "2023-01-01T08:00:00Z",
    },
    {
      id: "client-2",
      name: "Client B",
      description: "Description for Client B",
      status: "inactive",
      updated_at: "2023-02-20T14:30:00Z",
      created_at: "2023-02-05T11:00:00Z",
    },
    {
      id: "client-3",
      name: "Client C",
      description: "Description for Client C",
      status: "active",
      updated_at: "2023-03-25T09:15:00Z",
      created_at: "2023-03-10T07:00:00Z",
    },
  ];

  // Function to fetch clients from a real API (placeholder)
  async function fetchClientsFromApi(): Promise<Client[]> {
    try {
      // Replace with your actual API endpoint
      // const response = await fetch('/internal/clients.list');
      // if (!response.ok) {
      //   throw new Error(`HTTP error! status: ${response.status}`);
      // }
      // const data = await response.json();
      // return data.clients; // Assuming your API returns an object with a 'clients' array
      console.log("Fetching clients from API (mocked)");
      return clients; // Returning mock data for now
    } catch (error) {
      console.error("Error fetching clients:", error);
      return []; // Return empty array on error
    }
  }

  // Call the mock fetch function on component load
  // clients = await fetchClientsFromApi(); // Uncomment and use if you want to simulate async load

  function handleEdit(clientId: string) {
    goto(`/oauth/clients/edit/${clientId}`);
  }

  function handleCreate() {
    goto("/oauth/clients"); // Redirect to the create client page
  }
</script>

<div class="container">
  <div class="header">
    <h1>Client List</h1>
    <button class="create-button" onclick={handleCreate}
      >Create New Client</button
    >
  </div>

  {#if clients.length > 0}
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Description</th>
          <th>Status</th>
          <th>Updated At</th>
          <th>Created At</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        {#each clients as client (client.id)}
          <tr>
            <td>{client.name}</td>
            <td>{client.description}</td>
            <td>{client.status}</td>
            <td>{new Date(client.updated_at).toLocaleString()}</td>
            <td>{new Date(client.created_at).toLocaleString()}</td>
            <td>
              <button class="edit-button" onclick={() => handleEdit(client.id)}
                >Edit</button
              >
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
  {:else}
    <p>No clients found.</p>
  {/if}
</div>

<style>
  .container {
    max-width: 1000px;
    margin: 50px auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    background-color: #fff;
  }

  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 30px;
  }

  h1 {
    color: #333;
    margin: 0;
  }

  .create-button {
    padding: 10px 20px;
    background-color: #df00a9;
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .create-button:hover {
    background-color: #a3007a;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }

  th,
  td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
  }

  th {
    background-color: #f2f2f2;
    font-weight: bold;
    color: #555;
  }

  tr:nth-child(even) {
    background-color: #f9f9f9;
  }

  .edit-button {
    padding: 8px 12px;
    background-color: #df00a9;
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 14px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .edit-button:hover {
    background-color: #a3007a;
  }
</style>
