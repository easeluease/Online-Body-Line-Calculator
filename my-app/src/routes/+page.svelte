<script>
  import { onMount } from "svelte";

  let bust = "";
  let waist = "";
  let hips = "";
  let result = "";
  let error = "";
  let unit = "cm";
  let darkMode = false;

  // Load dark mode preference on mount
  onMount(() => {
    const stored = localStorage.getItem("darkMode");
    if (stored !== null) {
      darkMode = stored === "true";
    }
  });

  function toggleDarkMode() {
    darkMode = !darkMode;
    localStorage.setItem("darkMode", String(darkMode));
  }

  function calculate(e) {
    e.preventDefault();
    error = "";
    result = "";

    const bustNum = parseFloat(bust);
    const waistNum = parseFloat(waist);
    const hipsNum = parseFloat(hips);

    if (
      !bustNum ||
      !waistNum ||
      !hipsNum ||
      bustNum <= 0 ||
      waistNum <= 0 ||
      hipsNum <= 0
    ) {
      error = "Please enter valid positive numbers for all measurements.";
      return;
    }

    const hipsBust = (hipsNum / bustNum) * 100;
    const waistBust = (waistNum / bustNum) * 100;

    let shape = ""; 

    if (hipsBust >= 106) { // Hips are more than 106% of Bust
      shape = "Triangle Body Line";
    } else if (
      hipsBust >= 100 && //and Hips are between 100% and 106% of Bust
      hipsBust < 106 &&
      waistBust >= 80 && //and Waist is more than 80% of Bust
      waistBust < 89.99
    ) {
      shape = "Rectangle Body Line";
    } else if (hipsBust >= 100 && hipsBust <= 105.99 && waistBust <= 79.99) { //hipsBust between 100%–105.99% AND waistBust < 79.99%
      shape = "Hourglass";
    } else if (hipsBust < 100 && waistBust < 89.99) { //hipsBust < 100% AND waistBust < 89.99%
      shape = "Inverted Triangle Body Line";
    } else if (hipsBust < 100 && waistBust >= 90) { //hipsBust < 100% AND waistBust >= 90%
      shape = "Oval Body Line";
    } else { // If none of the above conditions are met
      shape = "Error - Please check your measurements.";
    }

    result = `
      <h3>Result</h3>
      <p><strong>Hips/Bust Ratio:</strong> ${hipsBust.toFixed(2)}%</p>
      <p><strong>Waist/Bust Ratio:</strong> ${waistBust.toFixed(2)}%</p>
      <p><strong>Body Line:</strong> ${shape}</p>
    `;
  }

  function clearForm() {
    bust = "";
    waist = "";
    hips = "";
    result = "";
    error = "";
  }

  // This will automatically update the logo path when darkMode changes
  $: oshLogoSrc = darkMode ? "/OSH WHITE.png" : "/OSH BLACK.png";

  $: {
    if (typeof window !== "undefined" && typeof document !== "undefined") {
      if (darkMode) {
        document.body.style.background = "#181a20";
        document.documentElement.style.background = "#181a20";
      } else {
        document.body.style.background = "#fff";
        document.documentElement.style.background = "#fff";
      }
    }
  }
</script>

<div class="app-bg {darkMode ? 'dark' : ''}">
  <div class="app-wrapper">
    <!-- Attach header back to the card and center -->
    <header class="main-header">
      <h1>OSH Image Academy Body Line Calculator</h1>
    </header>
    <main class="container">
      <div class="steps">
        <strong>How it works:</strong>
        <ol>
          <li>Select your measurement unit.</li>
          <li>Enter your <b>Bust</b>, <b>Waist</b>, and <b>Hips</b> circumferences in the same unit.</li>
          <li>Click <b>Calculate</b> to see your body line type, based on the ratios of your measurements.</li>
        </ol>
      </div>
      <div style="margin-bottom: 18px;">
        <label for="unit"><strong>Unit:</strong></label>
        <select id="unit" bind:value={unit}>
          <option value="cm">Centimeters (cm)</option>
          <option value="in">Inches (in)</option>
        </select>
      </div>
      {#if error}
        <div class="error">{error}</div>
      {/if}
      <form on:submit={calculate} autocomplete="off" class="form-row">
        <label for="bust">Bust Circumference ({unit}):</label>
        <input
          type="number"
          id="bust"
          bind:value={bust}
          min="0"
          step="any"
          inputmode="decimal"
          pattern="[0-9]*"
          required
        />

        <label for="waist">Waist Circumference ({unit}):</label>
        <input
          type="number"
          id="waist"
          bind:value={waist}
          min="0"
          step="any"
          inputmode="decimal"
          pattern="[0-9]*"
          required
        />

        <label for="hips">Hips Circumference ({unit}):</label>
        <input
          type="number"
          id="hips"
          bind:value={hips}
          min="0"
          step="any"
          inputmode="decimal"
          pattern="[0-9]*"
          required
        />

        <div class="button-row">
          <button type="submit">Calculate</button>
          <button type="button" class="clear-btn" on:click={clearForm}>Clear</button>
        </div>
      </form>

      {#if result}
        <hr />
        <div id="result">
          {@html result}
        </div>
      {/if}
    </main>
    <!-- Dark mode toggle button -->
    <button class="darkmode-fab" on:click={toggleDarkMode} aria-label="Toggle dark mode">
      {#if darkMode}
        <!-- Minimalistic sun icon -->
        <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="5"/><line x1="12" y1="1" x2="12" y2="3"/><line x1="12" y1="21" x2="12" y2="23"/><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/><line x1="1" y1="12" x2="3" y2="12"/><line x1="21" y1="12" x2="23" y2="12"/><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/></svg>
      {:else}
        <!-- Minimalistic moon icon -->
        <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12.79A9 9 0 1 1 11.21 3a7 7 0 0 0 9.79 9.79z"/></svg>
      {/if}
    </button>
  </div>

  <!-- Add this just before the closing </div> of your .app-bg (after .app-wrapper) -->
  <footer class="osh-footer">
    <div class="osh-footer-content">
      <img
        src={oshLogoSrc}
        alt="OSH Logo"
        class="osh-logo"
      />
      <span>Copyright ©2025 All rights reserved, OSH Image Academy Sdn. Bhd. </span>
      <div class="osh-footer-socials">
        <a href="https://www.facebook.com/stylebyosh/" target="_blank" rel="noopener" aria-label="Facebook" class="osh-social-link">
          <!-- Minimalistic Facebook SVG -->
          <svg viewBox="0 0 24 24" width="26" height="26" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <rect x="2" y="2" width="20" height="20" rx="5" fill="none"/>
            <path d="M16 8h-2a2 2 0 0 0-2 2v2h4" />
            <path d="M12 16v-4" />
          </svg>
        </a>
        <a href="https://www.instagram.com/stylebyosh/" target="_blank" rel="noopener" aria-label="Instagram" class="osh-social-link">
          <!-- Minimalistic Instagram SVG -->
          <svg viewBox="0 0 24 24" width="26" height="26" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <rect x="2" y="2" width="20" height="20" rx="5" fill="none"/>
            <circle cx="12" cy="12" r="4"/>
            <circle cx="17" cy="7" r="1"/>
          </svg>
        </a>
      </div>
    </div>
  </footer>
</div>

<style>
  @import url('https://fonts.googleapis.com/css?family=Roboto:400,700&display=swap');

  /* .app-bg container */
.app-bg {
  min-height: 100vh;
  width: 100%;
  min-width: 0;
  max-width: 100%;
  height: auto;
  background: #fff;
  color: #222;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start; /* allow content to grow downward */
  transition: background 0.3s, color 0.3s;
  position: relative;
  top: 0; left: 0;
  z-index: 0;
  font-family: 'Roboto', sans-serif !important;
  overflow-x: hidden;
  padding-bottom: 90px;
}
.app-bg.dark {
  background: #181a20 !important;
  color: #f4f6fa !important;
}

/* Center the main header */
.main-header {
  text-align: center;
  width: 100%;
  margin-bottom: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 32px; /* Add space from the top of the card */
}
.main-header h1 {
  margin: 0;
  font-size: 2.4rem;
  font-weight: 700;
  text-align: center;
  width: 100%;
}

/* Footer stays at the bottom but allows scrolling above it */
.osh-footer {
  width: 100%;
  padding: 24px 0 18px 0;
  font-family: 'Roboto', sans-serif;
  border-top: 1.5px solid #23272f;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 0;
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 10;
  background: #f9f9f9;
  color: #181a20;
  transition: background 0.3s, color 0.3s;
}
.app-bg.dark .osh-footer {
  background: #181a20 !important;
  color: #f4f6fa !important;
  border-top: 1.5px solid #23272f;
}

  .app-wrapper {
    width: 100%;
    max-width: 700px;
    margin: 40px auto; /* This centers it horizontally */
    border-radius: 24px;
    box-shadow: 0 2px 24px 0 rgba(0,0,0,0.10);
    background: #f9f9f9;
    display: flex;
    flex-direction: column;
    align-items: stretch;
    min-height: 0;
    z-index: 1;
    font-family: 'Roboto', sans-serif !important;
    justify-content: center;
    border: 1.5px solid #ccc;
    background: #f9f9f9;
    position: relative;
    padding-bottom: 40px;      /* Remove extra bottom padding */
    margin-bottom: 160px;    /* Reduce margin, or set to 0 if you want it flush */
  }
  .app-bg.dark .app-wrapper {
    border: 1.5px solid #363a45;
    background: #23272f;
  }

  /* Allow scrolling when content overflows, especially for results */
  main.container {
    width: 100%;
    max-width: 700px;
    margin: 0 auto;
    padding: 40px 48px 40px 48px;
    border: none;
    border-radius: 0 0 24px 24px;
    background-color: transparent;
    transition: background 0.3s, color 0.3s, border-color 0.3s;
    color: inherit;
    box-sizing: border-box;
    font-family: 'Roboto', sans-serif !important;
    min-height: 600px;
    margin-top: 0;
    overflow: visible;
    padding-bottom: 0;      /* Remove extra bottom padding */
  }

  .steps {
    background-color: #e9ecef;
    padding: 16px;
    border-radius: 7px;
    margin-bottom: 24px;
    color: inherit;
    font-size: 1.1rem;
    font-family: 'Roboto', sans-serif !important;
  }
  .app-bg.dark .steps {
    background: #363a45 !important; /* match results box in dark mode */
    color: #f4f6fa !important;
    border: 1.5px solid #4a4f5c !important; /* match results outline */
  }

  label {
    font-weight: 500;
    margin-top: 16px;
    display: block;
    color: inherit;
    font-size: 1.1rem;
    font-family: 'Roboto', sans-serif !important;
  }
  .app-bg.dark label {
    color: #f4f6fa !important;
  }


  input[type="number"],
  select {
    width: 100%;
    padding: 14px;
    margin-top: 7px;
    border: 1.5px solid #ccc;
    border-radius: 7px;
    background: #fff;
    color: #222;
    font-size: 1.1rem;
    transition: background 0.3s, color 0.3s, border-color 0.3s;
    font-family: 'Roboto', sans-serif !important;
    box-sizing: border-box;
  }
  .app-bg.dark input[type="number"],
  .app-bg.dark select {
    background: #23272f !important;
    color: #f4f6fa !important;
    border-color: #444 !important;
  }

  button {
    width: 100%;
    padding: 16px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 7px;
    font-size: 1.2rem;
    cursor: pointer;
    transition: background 0.2s, color 0.2s;
    margin-top: 18px;
    font-family: 'Roboto', sans-serif !important;
  }
  button:hover {
    background-color: #0056b3;
  }
  .app-bg.dark button {
    background: #007bff !important;
    color: #fff !important;
  }
  .app-bg.dark button:hover {
    background: #0056b3 !important;
  }

  .error {
    color: #e74c3c;
    margin-bottom: 18px;
    font-size: 1.1rem;
    font-family: 'Roboto', sans-serif !important;
  }

  /* Remove the blue line under the Calculate button */
  hr {
    display: none;
  }

  /* Results box: match .steps background in light mode, lighter in dark mode */
  #result {
    background-color: #e9ecef; /* same as .steps */
    padding: 18px;
    border-radius: 7px;
    margin-top: 22px;
    color: inherit;
    font-size: 1.1rem;
    font-family: 'Roboto', sans-serif !important;
  }
  .app-bg.dark #result {
    background: #363a45 !important; /* much lighter than #23272f for strong contrast */
    color: #f4f6fa !important;
    border: 1.5px solid #4a4f5c !important; /* outline for results */
  }

  /* Floating Action Button for dark mode - bigger */
  .darkmode-fab {
    position: fixed;
    left: 32px;
    /* Place just above the footer: */
    bottom: 110px; /* Footer height (about 80px) + 30px gap */
    width: 70px;
    height: 70px;
    border-radius: 50%;
    background: #23272f;
    color: #f4f6fa;
    border: none;
    box-shadow: 0 2px 12px rgba(0,0,0,0.18);
    font-size: 2.2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    z-index: 1001;
    transition: background 0.2s, color 0.2s;
    font-family: 'Roboto', sans-serif !important;
  }
  .darkmode-fab:hover {
    background: #181a20;
    color: #fff;
  }
  .app-bg.dark .darkmode-fab {
    background: #444 !important;
    color: #fff !important;
  }
  .app-bg.dark .darkmode-fab:hover {
    background: #666 !important;
    color: #fff !important;
  }

  /* Add outline for light mode for consistency (optional, remove if not wanted) */
  #result {
    border: 1.5px solid #d1d5db;
  }
  .steps {
    border: 1.5px solid #d1d5db;
  }

  /* Make sure the darkmode-fab SVG icons are centered and inherit color */
  .darkmode-fab svg {
    width: 36px;
    height: 36px;
    display: block;
    margin: auto;
    color: inherit;
  }

  .osh-footer {
    width: 100vw;
    padding: 24px 0 18px 0;
    font-family: 'Roboto', sans-serif;
    border-top: 1.5px solid #23272f;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 0;
    position: fixed;
    left: 0;
    bottom: 0;
    z-index: 10;
    background: #f9f9f9;      /* Light mode background */
    color: #181a20;           /* Light mode text */
    transition: background 0.3s, color 0.3s;
  }

  .app-bg.dark .osh-footer {
    background: #181a20 !important; /* Dark mode background */
    color: #f4f6fa !important;      /* Dark mode text */
    border-top: 1.5px solid #23272f;
  }

  .osh-footer-content {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    max-width: 1200px;
    padding: 0 40px;
    font-size: 1.08rem;
    font-weight: 500;
    letter-spacing: 0.2px;
  }

  .osh-footer-content > span {
    text-align: left;
  }

  .osh-footer-socials {
    display: flex;
    gap: 16px;
  }

  .osh-social-link {
    color: #181a20; /* dark text for light mode */
    opacity: 0.8;
    transition: opacity 0.2s, color 0.2s;
    display: flex;
    align-items: center;
  }
  .app-bg.dark .osh-social-link {
    color: #f4f6fa; /* white for dark mode */
  }
  .osh-social-link:hover {
    color: #007bff;
    opacity: 1;
  }
  .osh-social-link svg {
    display: block;
  }
  .osh-footer-socials svg {
    width: 32px !important;
    height: 32px !important;
    display: block;
  }

  /* Make sure the darkmode-fab is above the footer */
  .darkmode-fab {
    position: fixed;
    left: 32px;
    bottom: 110px; /* Footer height (about 80px) + 30px gap */
    width: 70px;
    height: 70px;
    border-radius: 50%;
    background: #23272f;
    color: #f4f6fa;
    border: none;
    box-shadow: 0 2px 12px rgba(0,0,0,0.18);
    font-size: 2.2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    z-index: 1001;
    transition: background 0.2s, color 0.2s;
    font-family: 'Roboto', sans-serif !important;
  }

  .osh-logo {
    height: 48px;   /* Increased from 28px */
    width: auto;
    margin-right: 16px;
    vertical-align: middle;
    transition: filter 0.2s;
  }

  .button-row {
    display: flex;
    gap: 12px;
    margin-top: 18px;
    justify-content: flex-start;
  }

  .button-row button {
    width: auto;
    min-width: 120px;
    margin-top: 0;
  }

  button[type="submit"] {
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 7px;
    font-size: 1.6rem;      /* Bigger font */
    font-weight: bold;
    padding: 26px 56px;     /* Bigger padding */
    min-width:440px;       /* Wider button */
    width: auto;
    margin-top: 0;
    transition: background 0.2s, color 0.2s;
  }
  button[type="submit"]:hover {
    background-color: #0056b3;
  }

  .clear-btn {
    background: #bdbdbd !important;
    color: #222 !important;
    border: none;
    border-radius: 9px;
    font-size: 1.4rem;
    font-weight: 600;
    padding: 22px 48px;
    min-width: 180px;
    width: auto;
    margin-top: 0;
    transition: background 0.2s, color 0.2s;
  }
  .clear-btn:hover {
    background: #9e9e9e !important;
    color: #181a20 !important;
  }

  /* Ensure dark mode does not override the clear button color */
  .app-bg.dark .clear-btn {
    background: #bdbdbd !important;
    color: #222 !important;
  }

  /* Make dark mode background stretch to the edge of the screen */
.app-bg {
  background: #fff;
  color: #222;
  width: 100%;
  min-width: 0;
  max-width: 100%;
  overflow-x: hidden !important;
  margin: 0;
  padding: 0;
  transition: background 0.3s, color 0.3s;
}
</style>
