<script>
	let postVar;
	let fileVar = null;
	let loading = false;
	let err = null;
	let finText = null;
	let downloadData = null;
	//finText = 'banana telephone';
	let resultToDisplay = finText && fileVar && !loading;
	$: if (fileVar) {
		// Note that `files` is of type `FileList`, not an Array:
		// https://developer.mozilla.org/en-US/docs/Web/API/FileList
		console.log(fileVar);

		// for (const file of fileVar) {
		// 	console.log(`${file.name}: ${file.size} bytes`);
		// }
	}

	$: console.log(loading);
	function submitForm(event) {
		event.preventDefault();
		if (!fileVar || fileVar[0].size / (1024 * 1024) > 4) {
			return;
		}
		loading = true;
		const dataArray = new FormData(document.getElementById('banana'));
		// dataArray.append('superHeroName', postVar);
		// console.log(fileVar);
		// // dataArray.append('file_name', 'herny')
		// dataArray.append('file', fileVar);
		// console.log(dataArray);

		for (var pair of dataArray.entries()) {
			console.log(pair[0] + ', ' + pair[1]);
		}
		fetch('https://us-central1-eavesdrop-365323.cloudfunctions.net/eavesdrop', {
			method: 'POST',
			// headers: [['Content-Type', 'multipart/form-data']],
			// headers: {
			// 	'Content-Type': 'multipart/form-data'
			// },
			body: dataArray,
			mode: 'cors'
		})
			.then(async (response) => {
				// Successfully uploaded
				console.log(response);
				const data = await response.json();
				console.log('data', data);

				finText = data.message;
				loading = false;
			})
			.catch((error) => {
				// Upload failed
				err = error.message;
				finText = 'There was an error.'
				loading = false
			});
	}

	console.log('Bool check: ', !finText);

	function makeTextFile(text) {
		if (!finText) {
			return;
		}
		let dataForDownload = new Blob([text], { type: 'text/plain' });

		if (downloadData !== null) {
			window.URL.revokeObjectURL(dataForDownload);
		}

		downloadData = window.URL.createObjectURL(dataForDownload);

		return downloadData;
	}
</script>

<div class="flex flex-col gap-4 sm:gap-8">
	{#if !finText && !loading}
		<div class="flex flex-col gap-2 bg-indigo-50 p-4">
			<h2 class="text-xl font-semibold">Audio to Text Transciption</h2>
			<!-- <p>Whisper is an AI neural net called Whisper that approaches human level robustness and accuracy on English speech recognition. Essentially, you can trust Whisper to accurately transcribe your audio recordings.</p> -->
			<p class="text-slate-600">
				Eavesdrop is an online audio transcription tool that allows you to upload an audio file and
				receive a near perfect text transcription of the file. To get started, use the button below
				and select the audio files from your device that you wish to convert to text!
			</p>
		</div>
	{/if}
	<form on:submit={submitForm} id="banana" enctype="multipart/form-data" class="flex flex-col">
		<label
			for="files"
			class={'px-6 py-4 bg-gradient-to-r from-cyan-500 shadow-lg to-violet-500 shadow mx-auto text-white text-lg cursor-pointer rounded hover:text-blue-100 items-center gap-4 duration-300 sm:text-xl ' +
				(fileVar ? ' hidden' : ' flex')}
			><i class="fa-solid text-2xl fa-file-arrow-up" />
			<h2>Select Audio File</h2>
		</label>
		<input
			type="file"
			id="files"
			style="visibility:hidden;"
			accept="audio/*"
			bind:files={fileVar}
			name="audio-file"
		/>
		<div
			class={'  rounded text-white grid-cols-3  bg-gradient-to-r from-cyan-500 shadow-lg to-violet-500 shadow ' +
				(fileVar ? ' grid -mt-6' : ' hidden')}
		>
			<p class="capitalize truncate pl-4 py-4 text-left">
				<i class="fa-regular fa-file pr-2" />
				{fileVar?.[0]?.name}
			</p>
			<p class="py-4 text-center">{(fileVar?.[0]?.size / (1024 * 1024)).toFixed(2)} MB</p>
			{#if loading}
				<div class="pr-4 py-4 flex items-center justify-end gap-2">
					<h2>Processing</h2>
					<i class="fa-solid fa-spinner animate-spin	" />
				</div>
			{:else if finText}
				<div class="flex pr-4 py-4 justify-end items-center">
					<div
						class="flex items-center duration-300 cursor-pointer gap-2 hover:text-blue-100 "
						on:click={() => {
							const a = document.createElement('a');
							document.body.appendChild(a);
							a.style = 'display: none;';
							a.href = makeTextFile(finText);
							a.download = 'info.txt';
							a.click();
						}}
					>
						<h2>Download</h2>
						<i class="fa-solid fa-file-arrow-down" />
					</div>
				</div>
			{:else}
				<div class="flex pr-4 py-4 justify-end items-center">
					<button
						class="flex items-center duration-300 cursor-pointer gap-2 hover:text-blue-100 "
						type="submit"
						><h2>Convert</h2>
						<i class="fa-solid rotate-90 fa-upload" /></button
					>
				</div>
			{/if}
		</div>
	</form>
	{#if finText}
		<div class="flex flex-col gap-2 bg-indigo-50 p-4">
			<h2 class="text-xl font-semibold">Transcribed Audio File</h2>
			<!-- <p>Whisper is an AI neural net called Whisper that approaches human level robustness and accuracy on English speech recognition. Essentially, you can trust Whisper to accurately transcribe your audio recordings.</p> -->
			<p class="text-slate-600">
				{finText}
			</p>
		</div>
		<button
			type="button"
			on:click={() => {
				finText = null;
				loading = false;
				fileVar = null;
				downloadData = null;
			}}
			class="px-6 py-4 bg-gradient-to-r from-cyan-500 shadow-lg to-violet-500 shadow mx-auto text-white text-lg cursor-pointer rounded hover:text-blue-100 flex items-center gap-4 duration-300 sm:text-xl"
			><i class="fa-solid fa-rotate-right text-2xl" />
			<h2>New Transcription</h2></button
		>
	{/if}
</div>
