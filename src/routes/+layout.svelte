<script>
	import '../app.css';
</script>

<div class="min-h-screen relative flex flex-col text-slate-900">
	<header
		class="sticky top-0 flex text-white bg-gradient-to-r from-cyan-500 shadow-lg to-violet-500"
	>
		<div
			class="flex items-center justify-between text-2xl sm:text-4xl font-bold max-w-[80ch] w-full p-8 mx-auto"
		>
			<h1 class="cursor-pointer">
				<i class="fa-solid pr-2 fa-signature" />
				eavesdrop
			</h1>
			<i
				id="meenu"
				on:mouseenter={() => {
					const m = document.getElementById('meenu');
					m.style.position = 'absolute';
					m.style.left = `${Math.random() * 96}%`;
					m.style.top = `${Math.random() * 96}%`;
				}}
				on:click={() => {
					const m = document.getElementById('meenu');
					m.style.position = 'absolute';
					m.style.left = `${Math.random() * 96}%`;
					m.style.top = `${Math.random() * 96}%`;
				}}
				class="fa-solid fa-bars text-2xl cursor-pointer duration-300 hover:text-blue-200"
			/>
		</div>
	</header>

	<main class="flex-1 flex flex-col mx-auto w-full max-w-[80ch] p-4 sm:p-8">
		<slot />
	</main>
</div>
<footer />
