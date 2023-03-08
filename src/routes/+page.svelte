<script>
	import Eliza from 'elizabot';
	import { beforeUpdate, afterUpdate } from 'svelte';

	let div;
	let autoscroll;
	let textV;

	beforeUpdate(() => {
		autoscroll = div && div.offsetHeight + div.scrollTop > div.scrollHeight - 20;
	});

	afterUpdate(() => {
		if (autoscroll) div.scrollTo(0, div.scrollHeight);
	});

	const eliza = new Eliza();

	let comments = [{ author: 'eliza', text: eliza.getInitial() }];

	const handleKeydown = (event) => {
		if (event.key === 'Enter') {
			testSv(textV);
		}
	};

	const handleSubmit = () => {
		testSv(textV);
	};

	const testSv = (value) => {
		if (!textV) return;
		comments = comments.concat({
			author: 'user',
			text: value
		});

		textV = '';

		const reply = eliza.transform(value);

		setTimeout(() => {
			comments = comments.concat({
				author: 'eliza',
				text: '...',
				placeholder: true
			});

			setTimeout(() => {
				comments = comments
					.filter((comment) => !comment.placeholder)
					.concat({
						author: 'eliza',
						text: reply
					});
			}, 500 + Math.random() * 500);
		}, 200 + Math.random() * 200);
	};
</script>

<div class="chat">
	<h1>Eliza</h1>

	<div class="scrollable" bind:this={div}>
		{#each comments as comment}
			<article class={comment.author}>
				<span>{comment.text}</span>
			</article>
		{/each}
	</div>
	<div class="msg">
		<input on:keydown={handleKeydown} bind:value={textV} class="input" />
		<button on:click={handleSubmit}
			><svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="1.5"
				stroke="currentColor"
				class="w-6 h-6"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M6 12L3.269 3.126A59.768 59.768 0 0121.485 12 59.77 59.77 0 013.27 20.876L5.999 12zm0 0h7.5"
				/>
			</svg>
		</button>
	</div>
</div>
