<script lang="ts">
	import { getRealText } from '$lib/public';
	import { onMount } from 'svelte';
	interface calls {
		[key: string]: number;
	}
	interface tipus {
		[key: string]: calls;
	}
	export let data;
	let aha: tipus = {};
	onMount(() => {
		if (data.date) {
			for (const jana of data.stats) {
				if (new Date(jana.date) > data.date?.prev && new Date(jana.date) < data.date?.next) {
					if (jana.type !== 'számla') {
						if (jana.type === 'pótlék') {
							if (jana.extra === 'éjszakai') {
								if (aha['pótlék_éjszakai']) {
									if (aha['pótlék_éjszakai'][jana.owner]) {
										aha['pótlék_éjszakai'][jana.owner]++;
									} else {
										aha['pótlék_éjszakai'][jana.owner] = 1;
									}
								} else {
									aha['pótlék_éjszakai'] = {};
									aha['pótlék_éjszakai'][jana.owner] = 1;
								}
							}
							if (jana.extra === 'délelőtti') {
								if (aha['pótlék_délelőtti']) {
									if (aha['pótlék_délelőtti'][jana.owner]) {
										aha['pótlék_délelőtti'][jana.owner]++;
									} else {
										aha['pótlék_délelőtti'][jana.owner] = 1;
									}
								} else {
									aha['pótlék_délelőtti'] = {};
									aha['pótlék_délelőtti'][jana.owner] = 1;
								}
							}
						} else {
							if (aha[jana.type]) {
								if (aha[jana.type][jana.owner]) {
									aha[jana.type][jana.owner]++;
								} else {
									aha[jana.type][jana.owner] = 1;
								}
							} else {
								aha[jana.type] = {};
								aha[jana.type][jana.owner] = 1;
							}
						}
					} else {
						if (aha[jana.type]) {
							if (aha[jana.type][jana.owner]) {
								aha[jana.type][jana.owner] += Number(jana.extra);
							} else {
								aha[jana.type][jana.owner] = Number(jana.extra);
							}
						} else {
							aha[jana.type] = {};
							aha[jana.type][jana.owner] = Number(jana.extra);
						}
					}
				}
			}
		}
	});
</script>

<div class="flex">
	<div class="m-auto text-center text-white">
		{#if data.date}
			<div>
				<h1 class="text-3xl font-bold">
					Előző hét ({`${data.date?.prev.getUTCMonth() + 1}.${data.date?.prev.getUTCDate()}. - ${data.date?.next.getUTCMonth() + 1}.${data.date?.next.getUTCDate()}`})
				</h1>
				{#each Object.entries(aha) as [key, value]}
					<h1 class="text-xl font-bold">{getRealText(key)}</h1>
					{#each Object.entries(value) as [key2, value2]}
						<div class="flex gap-2">
							<h2>{key2}: {key === 'számla' ? value2 + '$' : value2 + ' db'}</h2>
							<a
								href={`https://sckk.hu/list/${key2.replace(' ', '_')}/${key}`}
								class="bg-blue-900 px-2 rounded-xl"
								target="_blank">Link</a
							>
						</div>
					{/each}
				{/each}
			</div>
		{/if}
	</div>
</div>
