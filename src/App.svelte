<script>
	
	import {flip} from 'svelte/animate';
	
	let baskets = [
    {
      "name": "Applied candidates",
      "items": ["Sai", "Chandu" , "Dilli" , "Charan" , "Gopi" , "Koti" , "Vinoth"]
    },
    {
      "name": "Selected Candiadates",
      "items": []
    },
		{
      "name": "Rejected Candidates",
      "items": []
    }
    
  ];
	
	let hoveringOverBasket;
	let dropIndex = null;
	
	function dragStart(event, basketIndex, itemIndex) {
		// The data we want to make available when the element is dropped
    // is the index of the item being dragged and
    // the index of the basket from which it is leaving.
		const data = {basketIndex, itemIndex};
   	event.dataTransfer.setData('text/plain', JSON.stringify(data));
	}
	
	function drop(event, basketIndex) {
		event.preventDefault();
    const json = event.dataTransfer.getData("text/plain");
		const data = JSON.parse(json);
		
		// Remove the item from one basket.
		// Splice returns an array of the deleted elements, just one in this case.
		const [item] = baskets[data.basketIndex].items.splice(data.itemIndex, 1);
		
    // Add the item to the drop target basket.
		const items = baskets[basketIndex].items;
		items.splice(dropIndex ?? items.length, 0, item);
		baskets = baskets;
		
		hoveringOverBasket = null;
	}
	
	function dragOver(e) {
		dropIndex = null;
		const itemElement = e.target.closest('[data-item]');
		if (itemElement == null)
			return;
		
		const { basket, item } = itemElement.dataset;
		const targetBasket = baskets.find(b => b.name == basket);
		dropIndex = targetBasket.items.indexOf(item);
	}
</script>

<p >Candidates Status</p>

{#each baskets as basket, basketIndex (basket)}
  <div animate:flip>
    <b>{basket.name}</b>
    <ul
	  	class:hovering={hoveringOverBasket === basket.name}
	    on:dragenter={() => hoveringOverBasket = basket.name}
      on:dragleave={() => hoveringOverBasket = null}
  		on:drop={event => drop(event, basketIndex)}
  		on:dragover|preventDefault={dragOver}>
	    {#each basket.items as item, itemIndex (item)}
			  <div class="item" data-basket={basket.name} data-item={item} animate:flip>
	      	<li
	    	    draggable={true}
		  		  on:dragstart={event => dragStart(event, basketIndex, itemIndex)}
		    	>
		      	{item}
	    	  </li>
			  </div>
	    {/each}
    </ul>
  </div>
{/each}

<style>
	.hovering {
		border-color: orange;
	}
	.item {
		display: inline; /* required for flip to work */
	}
	li {
		background-color: lightgray;
		cursor: pointer;
		display: inline-block;
		margin-right: 17px;
		padding: 10px;
	}
	li:hover {
		background: orange;
		color: rgb(224, 15, 15);
	}
  ul {
		border: solid lightgray 1px;
		display: flex; /* required for drag & drop to work when .item display is inline */
		height: 50px; /* needed when empty */
		padding: 10px;
	}
</style>