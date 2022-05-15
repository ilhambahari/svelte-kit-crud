<script>
    import { onMount } from 'svelte';
    import { page } from '$app/stores';
    import { goto } from '$app/navigation';
    import axios from 'axios';

    let files = '';
    let title = '';
    let content = '';
    let validation = {};

    const fetchDataPost = async() => {
        await axios.get(`http://localhost:8000/api/posts/${$page.params.id}`)
        .then(response => {
            title = response.data.data.title;
            content = response.data.data.content;
        })
    }

    onMount(async() => {
        fetchDataPost();
    })

    const updatePost = (async() => {
        const formData = new FormData();
        formData.append('image', files[0]);
        formData.append('title', title);
        formData.append('content', content);
        formData.append('_method', 'PATCH');

        await axios.post(`http://localhost:8000/api/posts/${$page.params.id}`, formData)
        .then(() => {
            goto("/posts");
        }).catch((error) => {
            validation = error.response.data;
        })
    })
</script>

<div class="card border-0 rounded-3 shadow-sm">
    <div class="card-body">
        <form on:submit|preventDefault={updatePost}>

            <div class="form-group mb-3">
                <!-- svelte-ignore a11y-label-has-associated-control -->
                <label class="form-label fw-bold">Image</label>
                <input type="file" accept="image/png, image/jpeg" bind:files class="form-control"/>
            </div>
            {#if validation.image}
                <!-- svelte-ignore invalid-html-attribute -->
                <div class="alert alert-danger">
                    {validation.image}
                </div>
            {/if}
            

            <div class="form-group mb-3">
                <!-- svelte-ignore a11y-label-has-associated-control -->
                <label class="form-label fw-bold">TITLE</label>
                <input class="form-control" bind:value={title} type="text" placeholder="Input Title" />
            </div>
            {#if validation.title}
                <!-- svelte-ignore invalid-html-attribute -->
                <div class="alert alert-danger">
                    {validation.title}
                </div>
            {/if}


            <div class="form-group mb-3">
                <!-- svelte-ignore a11y-label-has-associated-control -->
                <label class="form-label fw-bold">CONTENT</label>
                <textarea class="form-control" rows={3} placeholder="Input Content" bind:value={content} />
            </div>
            {#if validation.content}
                <!-- svelte-ignore invalid-html-attribute -->
                <div class="alert alert-danger">
                    {validation.content}
                </div>
            {/if}


            <button class="btn btn-primary border-0 shadow-sm" type="submit">
                UPDATE
            </button>
        </form>
    </div>
</div>
