<script>
	import { onMount } from 'svelte';
	import { Octokit } from '@octokit/rest';

	let description = '';

	const owner = 'oleg-darkdev';
	const repo = 'gdansk-1978-app';
	const branch = 'master';
	const title = 'test 2';
	const body = 'test body 0';
	const filename = 'src/lib/shared/data/locations.js';

	const content = `const locations = [
	{
		title: 'Барановичи, городская площадь',
		desc: '29 июня 1941 г. в Барановичах на городской площади (ныне стадион «Локоматив») расстреляны 90 ромов, пригнанных из района Ганцевичи – Мальковичи',
		coordinates: ['26.03197452729094', '53.13112440012875'],
		link: '/articles/fuck-the-strapicms'
	}
];


export default locations;`;

	async function submitForm() {
		const octokit = new Octokit({ auth: import.meta.env.VITE_GITHUB });

		try {
			const {
				data: { sha }
			} = await octokit.repos.getContent({
				owner,
				repo,
				path: filename,
				ref: branch
			});

			const contentString = JSON.stringify(content).replace(/^"|"$/g, '');
			const encoder = new TextEncoder('utf-16');
			const utf16EncodedContent = encoder.encode(contentString);

			// Преобразование в формат Base64
			const encodedContent = btoa(String.fromCharCode(...utf16EncodedContent));
			// console.log(contentString);
			await octokit.repos.createOrUpdateFileContents({
				owner,
				repo,
				path: filename,
				branch,
				sha,
				message: 'Обновление файла через форму на сайте',
				content: encodedContent
			});

			console.log('Файл обновлен успешно');
		} catch (error) {
			console.error('Ошибка обновления файла:', error);
		}

		// try {
		//   const response = await octokit.issues.create({
		//     owner: owner,
		//     repo: repo,
		//     title,
		//     body: body,
		//     head: 'test',
		//   });
		//   console.log('issues создан:', response.data);
		// } catch (error) {
		//   console.error('Ошибка создания Pull Request:', error);
		// }
	}
</script>

<form on:submit|preventDefault={submitForm}>
	<!-- <label>
    Заголовок:
    <input type="text" bind:value={title} />
  </label>
  <label>
    Описание:
    <textarea bind:value={description} />
  </label>
  <label>
    Ветка:
    <input type="text" bind:value={branch} />
  </label> -->
	<button class="btn-primary btn" type="submit">Отправить Pull Request</button>
</form>
