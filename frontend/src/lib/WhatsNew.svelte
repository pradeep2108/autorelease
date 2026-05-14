<script>
  import { onMount } from 'svelte';

  let release = null;
  let visible = false;

  onMount(async () => {
    try {
      const res = await fetch(
        'https://api.github.com/repos/pradeep2108/autorelease/releases/latest'
      );
      if (!res.ok) return;
      release = await res.json();

      const dismissed = localStorage.getItem('whats-new-dismissed');
      if (dismissed !== release.tag_name) {
        visible = true;
      }
    } catch {
      // banner is non-critical, fail silently
    }
  });

  function dismiss() {
    localStorage.setItem('whats-new-dismissed', release.tag_name);
    visible = false;
  }
</script>

{#if visible && release}
  <div class="whats-new-banner">
    <div class="content">
      <strong>What's New — {release.tag_name}</strong>
      <p>{release.name}</p>
    </div>
    <a href={release.html_url} target="_blank" rel="noopener noreferrer">
      See release notes
    </a>
    <button on:click={dismiss}>✕</button>
  </div>
{/if}

<style>
  .whats-new-banner {
    position: fixed;
    bottom: 1.5rem;
    right: 1.5rem;
    background: #1a1a2e;
    color: #fff;
    border-radius: 0.5rem;
    padding: 1rem 1.25rem;
    display: flex;
    align-items: center;
    gap: 1rem;
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.3);
    max-width: 360px;
    z-index: 9999;
  }

  .content {
    flex: 1;
  }

  .content strong {
    display: block;
    font-size: 0.9rem;
    margin-bottom: 0.25rem;
  }

  .content p {
    margin: 0;
    font-size: 0.8rem;
    opacity: 0.8;
  }

  a {
    color: #7c9ef8;
    font-size: 0.8rem;
    white-space: nowrap;
    text-decoration: none;
  }

  a:hover {
    text-decoration: underline;
  }

  button {
    background: none;
    border: none;
    color: #fff;
    cursor: pointer;
    font-size: 1rem;
    padding: 0;
    opacity: 0.6;
  }

  button:hover {
    opacity: 1;
  }
</style>
