<script lang="ts">
    import { onMount } from 'svelte';

    let entries: Array<{
        id: number;
        title: string;
        content: string;
        date: string;
        media: { photo?: string; video?: string; audio?: string };
    }> = [];
    let title = '';
    let content = '';
    let photo: File | null = null;
    let video: File | null = null;
    let audio: File | null = null;
    let editId: number | null = null;

    function addEntry() {
        if (title.trim() === '' || content.trim() === '') return;

        let media = {
            photo: photo ? URL.createObjectURL(photo) : undefined,
            video: video ? URL.createObjectURL(video) : undefined,
            audio: audio ? URL.createObjectURL(audio) : undefined,
        };

        if (editId !== null) {
            // Edit an existing entry
            entries = entries.map((entry) =>
                entry.id === editId
                    ? { ...entry, title, content, date: new Date().toLocaleString(), media }
                    : entry
            );
            editId = null;
        } else {
            // Add a new entry
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

        // Reset inputs
        title = '';
        content = '';
        photo = null;
        video = null;
        audio = null;
    }

    function editEntry(id: number) {
        const entry = entries.find((e) => e.id === id);
        if (entry) {
            title = entry.title;
            content = entry.content;
            editId = id;
        }
    }

    function deleteEntry(id: number) {
        entries = entries.filter((entry) => entry.id !== id);
    }
</script>

<main class="max-w-2xl mx-auto p-5">
    <h1 class="text-4xl font-bold text-center mb-6">Diary</h1>

    <div class="bg-white p-6 rounded-lg shadow-lg mb-6">
        <h2 class="text-2xl font-semibold mb-4">{editId ? 'Edit Entry' : 'New Entry'}</h2>
        <input
            type="text"
            bind:value={title}
            placeholder="Title"
            class="w-full p-3 border rounded mb-4"
        />
        <textarea
            bind:value={content}
            placeholder="Write your thoughts..."
            class="w-full p-3 border rounded h-28 resize-none"
        ></textarea>
        <label class="block mb-2">Attach Photo:</label>
        <input type="file" accept="image/*" on:change={(e) => (photo = e.target.files[0])} />
        <label class="block mt-4 mb-2">Attach Video:</label>
        <input type="file" accept="video/*" on:change={(e) => (video = e.target.files[0])} />
        <label class="block mt-4 mb-2">Attach Audio:</label>
        <input type="file" accept="audio/*" on:change={(e) => (audio = e.target.files[0])} />
        <button
            on:click={addEntry}
            class="w-full bg-blue-500 text-white py-2 rounded hover:bg-blue-600 mt-4"
        >
            {editId ? 'Update' : 'Post'}
        </button>
    </div>

    <div class="space-y-4">
        {#if entries.length === 0}
            <p class="text-gray-500 text-center">No entries yet. Start writing!</p>
        {/if}

        {#each entries as entry}
            <div class="bg-gray-100 p-5 rounded-lg shadow-md">
                <div class="flex justify-between items-start mb-3">
                    <div>
                        <h3 class="text-xl font-bold">{entry.title}</h3>
                        <p class="text-sm text-gray-500">{entry.date}</p>
                    </div>
                    <div class="space-x-2">
                        <button
                            class="text-blue-500 hover:text-blue-700"
                            on:click={() => editEntry(entry.id)}
                        >
                            Edit
                        </button>
                        <button
                            class="text-red-500 hover:text-red-700"
                            on:click={() => deleteEntry(entry.id)}
                        >
                            Delete
                        </button>
                    </div>
                </div>
                <p>{entry.content}</p>
                {#if entry.media.photo}
                    <img src={entry.media.photo} alt="Photo" class="mt-4 max-w-full rounded" />
                {/if}
                {#if entry.media.video}
                    <video controls src={entry.media.video} class="mt-4 w-full rounded"></video>
                {/if}
                {#if entry.media.audio}
                    <audio controls src={entry.media.audio} class="mt-4 w-full"></audio>
                {/if}
            </div>
        {/each}
    </div>
</main>

<style>
    body {
        font-family: 'Inter', sans-serif;
        background: linear-gradient(to bottom, #f9fafb, #e5e7eb);
        min-height: 100vh;
    }
</style>