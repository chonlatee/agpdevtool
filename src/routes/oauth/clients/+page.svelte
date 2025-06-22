<script lang="ts">
    import { goto } from "$app/navigation";

    let name: string = "";
    let description: string = "";
    let auth_redirect_url: string = "";
    let sso_redirect_url: string = "";
    let config_profile: string = "";
    let showSuccessModal: boolean = false;

    async function handleSubmit() {
        const clientData = {
            name,
            description,
            auth_redirect_url,
            sso_redirect_url,
            config_profile: config_profile ? JSON.parse(config_profile) : null,
        };

        try {
            const response = await fetch("/internal/clients.create", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify(clientData),
            });

            if (response.ok) {
                showSuccessModal = true;
                // Clear the form
                name = "";
                description = "";
                auth_redirect_url = "";
                sso_redirect_url = "";
                config_profile = "";
            } else {
                const errorData = await response.json();
                alert(
                    `Failed to create client: ${errorData.message || response.statusText}`,
                );
            }
        } catch (error) {
            console.error("Error submitting form:", error);
            alert("An error occurred while submitting the form.");
        }
    }

    function handleModalOk() {
        showSuccessModal = false;
        goto("/oauth/clients/list"); // Redirect to the client list page
    }
</script>

<div class="container">
    <h1>Create New Client</h1>
    <form onsubmit={handleSubmit}>
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

        <button type="submit">Create Client</button>
    </form>
</div>

{#if showSuccessModal}
    <div class="modal-overlay">
        <div class="modal-content">
            <h2>Success!</h2>
            <p>Client created successfully.</p>
            <button onclick={handleModalOk}>OK</button>
        </div>
    </div>
{/if}

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
    textarea {
        width: 100%;
        padding: 10px;
        border: 1px solid #ddd;
        border-radius: 4px;
        box-sizing: border-box; /* Include padding and border in the element's total width and height */
        font-size: 16px;
    }

    textarea {
        resize: vertical;
        min-height: 100px;
    }

    button {
        display: block;
        width: 100%;
        padding: 12px;
        background-color: #df00a9;
        color: white;
        border: none;
        border-radius: 4px;
        font-size: 18px;
        cursor: pointer;
        transition: background-color 0.3s ease;
    }

    button:hover {
        background-color: #a3007a;
    }

    .modal-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.5);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }

    .modal-content {
        background: white;
        padding: 30px;
        border-radius: 8px;
        text-align: center;
        box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
        max-width: 400px;
        width: 90%;
    }

    .modal-content h2 {
        color: #28a745;
        margin-bottom: 20px;
    }

    .modal-content button {
        background-color: #df00a9;
        color: white;
        padding: 10px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
        transition: background-color 0.3s ease;
    }

    .modal-content button:hover {
        background-color: #a3007a;
    }
</style>
