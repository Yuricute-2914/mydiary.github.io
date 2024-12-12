<script lang="ts">
    import { onMount } from 'svelte';

    let entries = [];
    let title = '';
    let content = '';
    let photo = null;
    let video = null;
    let audio = null;
    let editId = null;

    function addEntry() {
        if (title.trim() === '' || content.trim() === '') return;

        let media = {
            photo: photo ? URL.createObjectURL(photo) : undefined,
            video: video ? URL.createObjectURL(video) : undefined,
            audio: audio ? URL.createObjectURL(audio) : undefined,
        };

        if (editId !== null) {
            entries = entries.map((entry) =>
                entry.id === editId
                    ? { ...entry, title, content, date: new Date().toLocaleString(), media }
                    : entry
            );
            editId = null;
        } else {
            entries = [
                ...entries,
                {
                    id: Date.now(),
                    title,
                    content,
                    date: new Date().toLocaleString(),
                    media,
                },
            ];
        }

        title = '';
        content = '';
        photo = null;
        video = null;
        audio = null;
    }

    function editEntry(id) {
        const entry = entries.find((e) => e.id === id);
        if (entry) {
            title = entry.title;
            content = entry.content;
            editId = id;
        }
    }

    function deleteEntry(id) {
        entries = entries.filter((entry) => entry.id !== id);
    }
</script>

<main class="max-w-3xl mx-auto p-5">
    <h1 class="text-5xl font-extrabold text-center mb-8 gradient-text">My Diary</h1>

    <div class="entry-form shadow-lg p-6 rounded-lg mb-6 bg-opacity-90">
        <h2 class="text-2xl font-bold mb-4">{editId ? 'Edit Entry' : 'New Entry'}</h2>
        <input
            type="text"
            bind:value={title}
            placeholder="Entry Title"
            class="input-field mb-4"
        />
        <textarea
            bind:value={content}
            placeholder="Your thoughts..."
            class="input-field mb-4"
        ></textarea>
        <label class="block mb-2 text-sm">Attach Photo:</label>
        <input type="file" accept="image/*" on:change={(e) => (photo = e.target.files[0])} class="mb-4"/>
        <label class="block mb-2 text-sm">Attach Video:</label>
        <input type="file" accept="video/*" on:change={(e) => (video = e.target.files[0])} class="mb-4"/>
        <label class="block mb-2 text-sm">Attach Audio:</label>
        <input type="file" accept="audio/*" on:change={(e) => (audio = e.target.files[0])} class="mb-4"/>
        <button
            on:click={addEntry}
            class="btn-primary mt-4 w-full"
        >
            {editId ? 'Update Entry' : 'POST'}
        </button>
    </div>

    <div class="entries space-y-6">
        {#if entries.length === 0}
            <p class="text-center text-gray-500">No entries yet. Start writing!</p>
        {/if}
        {#each entries as entry}
            <div class="entry bg-white rounded-lg shadow-lg p-5">
                <div class="flex justify-between items-center mb-4">
                    <div>
                        <h3 class="text-xl font-bold">{entry.title}</h3>
                        <p class="text-sm text-gray-500">{entry.date}</p>
                    </div>
                    <div class="space-x-3">
                        <button
                            on:click={() => editEntry(entry.id)}
                            class="btn-link"
                        >
                            Edit
                        </button>
                        <button
                            on:click={() => deleteEntry(entry.id)}
                            class="btn-link text-red-500"
                        >
                            Delete
                        </button>
                    </div>
                </div>
                <p>{entry.content}</p>
                {#if entry.media.photo}
                    <img src={entry.media.photo} alt="Attached photo" class="media mt-4" />
                {/if}
                {#if entry.media.video}
                    <video controls src={entry.media.video} class="media mt-4"></video>
                {/if}
                {#if entry.media.audio}
                    <audio controls src={entry.media.audio} class="mt-4"></audio>
                {/if}
            </div>
        {/each}
    </div>
</main>

<style>
    body {
        font-family: 'Poppins', sans-serif;
        background: linear-gradient(to bottom, #f3e7e9, #e3eeff);
        margin: 0;
        padding: 0;
        color: #333;
    }

    h1.gradient-text {
        background: linear-gradient(90deg, #ff8a00, #e52e71);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
    }

    .input-field {
        width: 100%;
        padding: 12px;
        border: 1px solid #ddd;
        border-radius: 8px;
        font-size: 1rem;
        transition: all 0.3s ease;
    }

    .input-field:focus {
        border-color: #6c63ff;
        outline: none;
        box-shadow: 0 0 5px rgba(108, 99, 255, 0.5);
    }

    .btn-primary {
        background: linear-gradient(to right, #6c63ff, #9066ff);
        color: white;
        border: none;
        padding: 12px 20px;
        font-weight: bold;
        border-radius: 8px;
        cursor: pointer;
        transition: all 0.3s ease;
    }

    .btn-primary:hover {
        background: linear-gradient(to right, #9066ff, #6c63ff);
        transform: scale(1.02);
    }

    .btn-link {
        background: none;
        border: none;
        color: #6c63ff;
        font-weight: bold;
        cursor: pointer;
        transition: color 0.3s ease;
    }

    .btn-link:hover {
        color: #9066ff;
    }

    .media {
        max-width: 100%;
        border-radius: 8px;
    }
</style>
