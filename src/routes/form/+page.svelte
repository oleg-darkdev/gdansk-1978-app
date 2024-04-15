<script>
	import { onMount } from 'svelte';
	import { Octokit } from '@octokit/rest';

	let description = '';

	// const accessToken = import.meta.env.GITHUB;
	const owner = 'oleg-darkdev';
	const repo = ' gdansk-1978-app';
	const branch = 'master';
	const title = 'test 2';
	const body = 'test body 0';

	async function submitForm() {
		const octokit = new Octokit({ auth: import.meta.env.GITHUB });

		try {
			// Получаем содержимое файла
			const {
				data: { sha }
			} = await octokit.repos.getContent({
				owner,
				repo,
				path: filename,
				ref: branch
			});

			// Обновляем содержимое файла
			await octokit.repos.createOrUpdateFileContents({
				owner,
				repo,
				path: filename,
				branch,
				sha,
				message: 'Обновление файла через форму на сайте',
				content: btoa(content)
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
		//   console.log('Pull Request создан:', response.data);
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
