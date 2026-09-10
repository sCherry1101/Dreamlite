<script>
  import Square from './square.svelte'
  import Rectangle from './rectangle.svelte'
  import Triangle from './triangle.svelte'

  const views = {
    square: "Square",
    rectangle: "Rectangle",
    triangle: "Triangle"
  }

  let active = $state('square')

  const info = {
    square: {
      title: "Square",
      description:
        "A square is a quadrilateral with four equal sides and four right angles.",
      formula: "P = 4a  ·  A = a²",
      facts: [
        "All four sides of a square have equal length.",
        "All four interior angles are 90°.",
        "The perimeter is four times the length of one side.",
        "The area is the square of the side length."
      ]
    },

    rectangle: {
      title: "Rectangle",
      description:
        "A rectangle is a quadrilateral with four right angles and opposite sides equal in length.",
      formula: "P = 2(l + w)  ·  A = l × w",
      facts: [
        "Opposite sides of a rectangle are equal.",
        "All four interior angles are 90°.",
        "The perimeter is twice the sum of its length and width.",
        "The area is the product of its length and width."
      ]
    },

    triangle: {
      title: "Triangle",
      description:
        "A triangle is a polygon with three sides and three angles. Its area depends on its base and perpendicular height.",
      formula: "P = a + b + c  ·  A = ½ × b × h",
      facts: [
        "A triangle has three sides and three interior angles.",
        "The perimeter is the sum of all three side lengths.",
        "The area is half the product of the base and perpendicular height.",
        "The height must be perpendicular to the chosen base."
      ]
    }
  }

  let currentInfo = $derived(info[active])
</script>

<section class="mensuration">
  <article class="mensuration__view">

    <div class="mensuration__tabs">
      {#each Object.entries(views) as [key, label]}
        <button
          class:active={active === key}
          onclick={() => active = key}
        >
          {label}
        </button>
      {/each}
    </div>

    {#if active === 'square'}
      <Square />
    {:else if active === 'rectangle'}
      <Rectangle />
    {:else if active === 'triangle'}
      <Triangle />
    {/if}

  </article>

  <aside class="mensuration__info">

    <h1 class="mensuration__title">
      {currentInfo.title}
    </h1>

    <p class="mensuration__description">
      {currentInfo.description}
    </p>

    <div class="mensuration__formula">
      {currentInfo.formula}
    </div>

    <ul class="mensuration__list">
      {#each currentInfo.facts as fact}
        <li>{fact}</li>
      {/each}
    </ul>

  </aside>
</section>

<style>
  .mensuration {
    display: grid;
    grid-template-columns: minmax(0, 1fr) 320px;
    gap: 2rem;
    width: 100%;
  }

  .mensuration__view {
    min-width: 0;
  }

  .mensuration__tabs {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1rem;
  }

  .mensuration__tabs button {
    padding: 0.65rem 1rem;
    border: 1px solid var(--border);
    border-radius: 0.7rem;
    background: var(--bg-secondary);
    color: var(--text-primary);
    font: inherit;
    cursor: pointer;
    transition: 0.2s ease;
  }

  .mensuration__tabs button:hover {
    border-color: var(--text-primary);
  }

  .mensuration__tabs button.active {
    background: var(--text-primary);
    color: var(--bg-primary);
    border-color: var(--text-primary);
  }

  .mensuration__info {
    padding: 1.5rem;
    border: 1px solid var(--border);
    border-radius: 1rem;
    background: var(--bg-secondary);
    height: fit-content;
  }

  .mensuration__title {
    margin: 0 0 0.75rem;
    color: var(--text-primary);
    font-size: 1.8rem;
  }

  .mensuration__description {
    margin: 0 0 1.25rem;
    color: var(--text-secondary);
    line-height: 1.6;
  }

  .mensuration__formula {
    margin-bottom: 1.25rem;
    padding: 0.9rem 1rem;
    border: 1px solid var(--border);
    border-radius: 0.75rem;
    color: var(--text-primary);
    font-size: 1.15rem;
    font-weight: 600;
    text-align: center;
  }

  .mensuration__list {
    margin: 0;
    padding-left: 1.25rem;
    color: var(--text-secondary);
    line-height: 1.7;
  }

  .mensuration__list li {
    margin-bottom: 0.5rem;
  }

  @media (max-width: 900px) {
    .mensuration {
      grid-template-columns: 1fr;
    }

    .mensuration__info {
      order: -1;
    }
  }

  @media (max-width: 600px) {
    .mensuration__tabs {
      overflow-x: auto;
    }

    .mensuration__tabs button {
      white-space: nowrap;
    }

    .mensuration__info {
      padding: 1.2rem;
    }

    .mensuration__title {
      font-size: 1.5rem;
    }
  }
</style>

