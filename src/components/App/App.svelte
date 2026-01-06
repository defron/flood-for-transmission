<script>
  import TopBar from '~components/TopBar';
  import Panel from '~components/Panel';
  import TorrentList from '~components/TorrentList';
  import Modal from '~components/Modal';
  import ContextMenu from '~components/ContextMenu';
  import Alerts from '~components/Alerts';
  import {
    panel,
    darkMode,
    switchSpeedColors,
    mobileView,
  } from '~helpers/stores';

  let touchStartX = 0;
  let touchStartY = 0;

  function checkDirection(touchEndX, touchEndY) {
    if (!$mobileView.grid) {
      return;
    }
    const minSwipeDistance = 60; // Set a minimum distance for a valid swipe
    const distance = Math.abs(touchEndX - touchStartX);
    const isMainlyHorizontalSwipe =
      distance > Math.abs(touchEndY - touchStartY);

    if (distance >= minSwipeDistance && isMainlyHorizontalSwipe) {
      if (touchEndX < touchStartX) {
        panel.close();
      } else if (touchEndX > touchStartX) {
        panel.open();
      }
    }
  }

  const handleTouchStart = (e) => {
    touchStartX = e.changedTouches[0].clientX;
    touchStartY = e.changedTouches[0].clientY;
  };
  const handleTouchEnd = (e) => {
    checkDirection(e.changedTouches[0].clientX, e.changedTouches[0].clientY);
  };

  const hidePanelOnMobile = () => {
    if (window.innerWidth > 550) return;

    panel.close();
  };
</script>

<main
  class:panel={$panel}
  class:mobile-grid={$mobileView.grid}
  class:dark-mode={$darkMode}
  class:switch-speed-colors={$switchSpeedColors}
  on:touchstart={handleTouchStart}
  on:touchend={handleTouchEnd}
>
  <Panel />
  <div class="panel-backdrop" on:click={hidePanelOnMobile}></div>
  <div class="content">
    <TopBar />
    <TorrentList />
    <Modal />
    <ContextMenu />
    <Alerts />
  </div>
</main>

<style>
  main {
    display: grid;
    grid-template: 'panel content' 1fr / 0 1fr;
  }

  main.panel {
    grid-template: 'panel content' 1fr / 240px 1fr;
  }

  .content {
    grid-area: content;
    display: grid;
    height: 100dvh;
    grid-template:
      'header' auto
      'torrentlist' 1fr / 1fr;
  }

  .panel-backdrop {
    display: none;
    position: absolute;
    top: 0;
    left: 0;
  }

  @media (max-width: 550px) {
    .mobile-grid {
      touch-action: pan-y;
    }

    main,
    main.panel {
      grid-template: 'content' 1fr / 100vw;
    }

    main > :global(.panel) {
      position: absolute;
      left: -240px;
      top: 0;
      width: 240px;
      z-index: 3;
      transition: left 300ms ease-in;
    }

    main.panel > :global(.panel) {
      position: absolute;
      left: 0px;
    }

    main.panel .panel-backdrop {
      display: block;
      z-index: 2;
      width: 100vw;
      height: 100dvh;
    }
  }
</style>
