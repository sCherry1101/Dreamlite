<script>
  import '/src/lib/assets/stylesheet/global.css'
  import Button from '../Button'

  let {
    brand = 'Anvesha',
    brandUrl = '/',
    links = [
      {
        label: 'Science',
        href: '/science'
      },
      {
        label: 'Maths',
        href: '/maths'
      },
      {
        label: 'About',
        href: '/about',
        icon: 'lucide:info'
      }
    ]
  } = $props()

  let theme = $state('default')
  let themeOpen = $state(false)

  function setTheme(newTheme) {
    theme = newTheme
    themeOpen = false

    if (newTheme === 'default') {
      document.documentElement.removeAttribute('data-theme')
    } else {
      document.documentElement.dataset.theme = newTheme
    }
  }
</script>

<header class="navbar">
  <div class="navbar__container">

    <a href={brandUrl} class="navbar__brand">
      <span class="navbar__brand-text">{brand}</span>
    </a>

    <nav class="navbar__nav-desktop">
      <div class="theme-switcher">

        <button
          class="theme-button"
          class:active={themeOpen}
          onclick={() => themeOpen = !themeOpen}
        >
          Themes
          <span class="theme-arrow">⌄</span>
        </button>

        {#if themeOpen}
          <div class="theme-menu">

            <button
              class:active={theme === 'default'}
              onclick={() => setTheme('default')}
            >
              Default
            </button>

            <button
              class:active={theme === 'light'}
              onclick={() => setTheme('light')}
            >
              Light
            </button>

            <button
              class:active={theme === 'stardust'}
              onclick={() => setTheme('stardust')}
            >
              Stardust
            </button>

          </div>
        {/if}

      </div>

      <span class="navbar__divider"></span>

      <ul class="navbar__links">
        {#each links as link}
          <li>
            <Button
              href={link.href}
              variant="ghost"
              size="sm"
              target={link.external ? '_blank' : undefined}
              rel={link.external ? 'noopener noreferrer' : undefined}
              icon={link.icon}
            >
              {link.label}
            </Button>
          </li>
        {/each}
      </ul>

    </nav>

  </div>
</header>