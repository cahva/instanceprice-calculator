<script>
	import instancetypes from './instances.js';
	let instances = instancetypes.map(i => {
		i.numberOfInstances = 1;
		i.monthly = i.price * 24 * 30;
		return i;
	})
	
</script>

<style>
	table {
		width: 100%;
		border: 1px solid #ccc;
		padding: 1rem;
	}
	
	th {
		text-align: left;
	}
	
	tbody {
		border: 1px solid #ccc;
	}
	input {
		width: 75px;
	}
</style>

<table>
	<thead>
		<tr>
			<th>Nr of instances</th>
			<th>Type</th>
			<th>vCPU</th>
			<th>ECU</th>
			<th>Mem GiB</th>
			<th>Price / h</th>
			<th>Price / month</th>
			<th>Total price / h</th>
			<th>Total price / month</th>
		</tr>
	</thead>
	<tbody>
	{#each instances as i}
		<tr>
			<td><input type="number" min="1" max="1000" bind:value={i.numberOfInstances}></td>
			<td>{i.type}</td>
			<td>{i.vcpu}</td>
			<td>{i.ecu}</td>
			<td>{i.mem}</td>
			<td>${i.price}</td>
			<td>${i.monthly}</td>
			<td>${Number.parseFloat(i.price * i.numberOfInstances).toPrecision(3)}</td>
			<td>${Number.parseFloat(i.monthly * i.numberOfInstances).toPrecision(6)}</td>
		</tr>
	{/each}
	</tbody>
</table>




