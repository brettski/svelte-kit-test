<script context="module">
	const postFiles = import.meta.glob('../../_blog/posts/**/*.md');
	let body = [];

	for (const path in postFiles) {
		const pathparts = path.split('/');
		const filename = pathparts[pathparts.length - 1];
		// const record = postFiles[path]().then(({ metadata }) => {
		// 	metadata.filename = filename;
		// 	return metadata;
		// });
		const record = postFiles[path]().then((pf) => {
			console.log('metadata', pf.metadata);
			console.log('all of it', pf?.default?.render ? pf.default.render() : 'n/a');
			pf.metadata.filename = filename;
			return pf.metadata;
		});

		body.push(record);
	}
	/**
	 * @type {import('@sveltejs/kit').Load}
	 */
	export async function load({ page, fetch }) {
		let posts = await Promise.all(body);
		posts = posts.map((p) => ({
			tags: [],
			categories: [],
			...p
		}));
		posts.sort((a, b) => {
			if (a.date > b.date) return -1;
			if (a.date < b.date) return 1;
			s;
			return 0;
		});
		const tags = new Set();
		const categories = new Set();
		posts.forEach((p) => {
			p.tags.forEach((t) => tags.add(t));
			p.categories.forEach((c) => categories.add(c));
		});
		return {
			props: {
				posts,
				tags,
				categories
			}
		};
	}
</script>

<script>
	import Counter from '$lib/Counter.svelte';
	import About from '../about.svelte';

	export let posts;
	export let tags;
	export let categories;
	// console.log('Dem posts', posts);
	console.log('first post', posts[0]);
</script>

<h1>Blog</h1>
<h2>Categories</h2>
<div style="margin-bottom:20px">
	{[...categories].sort()}
</div>
<h2>Tags</h2>
<div style="margin-bottom:20px">
	{[...tags].sort()}
</div>
<code>{JSON.stringify(posts)}</code>
<ul class="list-none">
	{#each posts as { title, outline, slug, filename, tags, categories }}
		<li style="list-style: none; border: black solid 1px; margin: 2px 0; padding: 0 4px">
			<!-- <a class="text-blue-500 space-y-3" rel="prefetch" href="blog/posts/{slug}/"> -->
			<h2>
				{title}
			</h2>
			<!-- </a> -->

			<p class="text-white text-base">
				Outline: {outline || ''}<br />
				Filename: {filename}
			</p>

			<p class="text-sm font-normal text-gray-500">
				tags:
				{#each tags as tag}
					{tags} &nbsp;
				{/each}
			</p>
			<p class="text-sm font-normal text-gray-500">
				categories:
				{#each categories as tag}
					{categories} &nbsp;
				{/each}
			</p>
			<div class="post-text" />
		</li>
	{/each}
</ul>
