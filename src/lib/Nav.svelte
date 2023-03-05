<script>
  import { page } from "$app/stores";
</script>

<nav>
  <ul>
    <li class="nav-item">
      <a href="/" class:active={$page.url.pathname.includes("index")}>Home</a>
    </li>
    <li class="nav-item">
      <a href="/about" class:active={$page.url.pathname.includes("about")}
        >About</a
      >
    </li>
    <li class="nav-item">
      <a href="/tips" class:active={$page.url.pathname.includes("tips")}
        >Tips To Climb</a
      >
    </li>
  </ul>
</nav>

<style>
  :global(.nav-item a.active) {
    color: #5361ff !important;
    border-bottom: 2px solid #5361ff !important;
  }
  :global(.nav-item a:hover) {
    color: #5361ff !important;
  }

  nav {
    background-color: #333;
    color: #fff;
    padding: 1rem;
    width: 100vw;
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  }

  nav ul {
    display: flex;
    list-style: none;
    margin: 0;
    padding: 0;
  }

  nav li {
    margin-right: 1rem;
  }

  nav a {
    color: #fff;
    font-weight: 500;
  }
</style>
