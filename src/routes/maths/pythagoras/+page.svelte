<script>

  import Pythagoras from './pythagoras_shapes.svelte'

  const views = {
    pt: "Pythagoras Theorem",
  }

  let active = $state('pt')

  const info = {
    pt: {
      title: "Pythagoras Theorem",
      description:
        "In a right-angled triangle, the square of the hypotenuse is equal to the sum of the squares of the other two sides.",
      formula: "a² + b² = c²",
      facts: [
        "The hypotenuse is the side opposite the right angle.",
        "The hypotenuse is always the longest side of a right-angled triangle.",
        "A square constructed on a side has an area equal to the square of that side's length.",
        "Therefore, the areas of the two smaller squares add up to the area of the square on the hypotenuse."
      ]
    }
  }

  let currentInfo = $derived(info[active])
</script>

<section class="pythagoras">

  <article class="pythagoras__view">


<div class="pythagoras__tabs">
  {#each Object.entries(views) as [key, label]}
    <button
      class:active={active === key}
      onclick={() => active = key}
    >
      {label}
    </button>
  {/each}
</div>

{#if active === 'pt'}
  <Pythagoras />
{/if}


  </article>

  <aside class="pythagoras__info">


<h1 class="pythagoras__title">
  {currentInfo.title}
</h1>

<p class="pythagoras__description">
  {currentInfo.description}
</p>

<div class="pythagoras__formula">
  {currentInfo.formula}
</div>

<ul class="pythagoras__list">
  {#each currentInfo.facts as fact}
    <li>{fact}</li>
  {/each}
</ul>


  </aside>

</section>
