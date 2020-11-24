<script>
  import { flip } from 'svelte/animate';
  import orderBy from 'lodash/orderBy';
  import instancetypes from './instances.js';
  import Th from './Th.svelte';

  let instances = instancetypes.map((i) => {
    i.numberOfInstances = 1;
    i.monthly = i.price * 24 * 30;
    i.total_h = i.price;
    i.total_m = i.price * 24 * 30;
    return i;
  });

  let newInstance = {
    type: '',
    vcpu: 0,
    ecu: '',
    mem: 0.0,
    price: 0.0,
  };

  let sortAsc = true;
  let selectedField = 'type';

  const parsed = (data) => (selectedField === 'type' ? data : parseFloat(data));

  async function sortInstances() {
    instances = orderBy(instances, (o) => parsed(o[selectedField]), [
      sortAsc ? 'asc' : 'desc',
    ]);
  }

  function sortBy(field) {
    if (field === selectedField) {
      sortAsc = !sortAsc;
    } else {
      selectedField = field;
    }

    sortInstances();
  }

  function addInstance() {
    const price = Number.parseFloat(newInstance.price);
    const newInt = {
      ...newInstance,
      numberOfInstances: 1,
      mem: Number.parseFloat(newInstance.mem),
      price,
      monthly: price * 24 * 30,
      total_h: price,
      total_m: price * 24 * 30,
    };

    console.log(newInt);
    instances = [...instances, newInt];
  }

  $: isSelected = (field) => field === selectedField;
  $: instanceCalc = instances.map((i) => {
    i.total_h = i.numberOfInstances * i.price;
    i.total_m = i.numberOfInstances * i.monthly;
    return i;
  });
  $: disabled =
    newInstance.type.length === 0 ||
    instances.findIndex((i) => i.type === newInstance.type) >= 0;

  const options = { duration: 200 };
</script>

<style>

  .container {
    max-width: 1200px;
    margin: 1rem auto;
  }
  table {
    width: 100%;
    border: 1px solid #ccc;
    padding: 1rem;
  }

  td {
    padding: 0 1rem;
  }

  th {
    text-align: left;
    padding: 0 1rem;
  }

  tbody {
    border: 1px solid #ccc;
  }
  input {
    width: 75px;
  }

  form {
    margin: 2rem 0;
  }
  form input {
    width: 100%;
  }

  .add-new {
    margin: 1rem 0;
    width: 100%;
  }
</style>

<div class="container">
  <h1>Calculate AWS instance prices</h1>
  <table>
    <thead>
      <tr>
        <th>Nr of instances</th>
        <Th
          on:select={() => sortBy('type')}
          selected={isSelected('type')}
          {sortAsc}>
          Type
        </Th>
        <Th
          on:select={() => sortBy('vcpu')}
          selected={isSelected('vcpu')}
          {sortAsc}>
          vCPU
        </Th>
        <Th
          on:select={() => sortBy('ecu')}
          selected={isSelected('ecu')}
          {sortAsc}>
          ECU
        </Th>
        <Th
          on:select={() => sortBy('mem')}
          selected={isSelected('mem')}
          {sortAsc}>
          Mem GiB
        </Th>
        <Th
          on:select={() => sortBy('price')}
          selected={isSelected('price')}
          {sortAsc}>
          Price / h
        </Th>
        <Th
          on:select={() => sortBy('monthly')}
          selected={isSelected('monthly')}
          {sortAsc}>
          Price / month
        </Th>
        <Th
          on:select={() => sortBy('total_h')}
          selected={isSelected('total_h')}
          {sortAsc}>
          Total price / h
        </Th>
        <Th
          on:select={() => sortBy('total_m')}
          selected={isSelected('total_m')}
          {sortAsc}>
          Total price / month
        </Th>
      </tr>
    </thead>
    <tbody>
      {#each instanceCalc as i (i.type)}
        <tr animate:flip={options}>
          <td>
            <input
              type="number"
              min="1"
              max="1000"
              bind:value={i.numberOfInstances} />
          </td>
          <td>{i.type}</td>
          <td>{i.vcpu}</td>
          <td>{i.ecu}</td>
          <td>{i.mem}</td>
          <td>${i.price}</td>
          <td>${i.monthly}</td>
          <td>${Number.parseFloat(i.total_h).toPrecision(3)}</td>
          <td>${Number.parseFloat(i.total_m).toPrecision(6)}</td>
        </tr>
      {/each}
    </tbody>
  </table>

  <h3>Add new instance to list</h3>
  <form on:submit|preventDefault={addInstance}>
    <table>
      <thead>
        <tr>
          <th>Type</th>
          <th>vCPU</th>
          <th>ECU</th>
          <th>Mem GiB</th>
          <th>Price / h</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><input type="text" bind:value={newInstance.type} /></td>
          <td><input type="text" bind:value={newInstance.vcpu} /></td>
          <td><input type="text" bind:value={newInstance.ecu} /></td>
          <td><input type="text" bind:value={newInstance.mem} /></td>
          <td><input type="text" bind:value={newInstance.price} /></td>
        </tr>
      </tbody>
    </table>
    <input type="submit" value="Add" class="add-new" {disabled} />
  </form>
</div>
