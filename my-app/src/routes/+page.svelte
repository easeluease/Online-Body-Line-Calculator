<script>
  let bust = '';
  let waist = '';
  let hips = '';
  let result = '';
  let error = '';
  let unit = 'cm'; // default unit

  function calculate(e) {
    e.preventDefault();
    error = '';
    result = '';

    const bustNum = parseFloat(bust);
    const waistNum = parseFloat(waist);
    const hipsNum = parseFloat(hips);

    if (!bustNum || !waistNum || !hipsNum || bustNum <= 0 || waistNum <= 0 || hipsNum <= 0) {
      error = "Please enter valid positive numbers for all measurements.";
      return;
    }

    const hipsBust = (hipsNum / bustNum) * 100;
    const waistBust = (waistNum / bustNum) * 100;

    let shape = "";

    if (hipsBust > 106) {
      shape = "Triangle Body Line";
    } else if (hipsBust >= 100 && hipsBust <= 106 && waistBust >= 80 && waistBust <= 90) {
      shape = "Rectangle Body Line";
    } else if (hipsBust >= 100 && hipsBust <= 106 && waistBust < 79) {
      shape = "Hourglass Body Line";
    } else if (hipsBust < 100 && waistBust < 90) {
      shape = "Inverted Triangle Body Line";
    } else if (hipsBust < 100 && waistBust > 90) {
      shape = "Oval Body Line";
    } else {
      shape = "Unclassified - Please check your measurements.";
    }

    result = `
      <h3>Result</h3>
      <p><strong>Hips/Bust Ratio:</strong> ${hipsBust.toFixed(2)}%</p>
      <p><strong>Waist/Bust Ratio:</strong> ${waistBust.toFixed(2)}%</p>
      <p><strong>Body Line:</strong> ${shape}</p>
      <hr>
      <h4>How is this calculated?</h4>
      <ul>
        <li><strong>Triangle:</strong> Hips/Bust &gt; 106%</li>
        <li><strong>Rectangle:</strong> Hips/Bust 100-106% AND Waist/Bust 80-90%</li>
        <li><strong>Hourglass:</strong> Hips/Bust 100-106% AND Waist/Bust &lt; 79%</li>
        <li><strong>Inverted Triangle:</strong> Hips/Bust &lt; 100% AND Waist/Bust &lt; 90%</li>
        <li><strong>Oval:</strong> Hips/Bust &lt; 100% AND Waist/Bust &gt; 90%</li>
      </ul>
    `;
  }
</script>

<style>
  .container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 8px;
    background-color: #f9f9f9;
  }

  h2 {
    text-align: center;
    color: #333;
  }

  .steps {
    background-color: #e9ecef;
    padding: 10px;
    border-radius: 5px;
    margin-bottom: 20px;
  }

  label {
    font-weight: bold;
    margin-top: 10px;
    display: block;
  }

  input[type="number"], select {
    width: 100%;
    padding: 10px;
    margin: 5px 0 15px 0;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  button {
    width: 100%;
    padding: 10px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 16px;
    cursor: pointer;
  }

  button:hover {
    background-color: #0056b3;
  }

  .error {
    color: red;
    margin-bottom: 15px;
  }

  #result {
    background-color: #d1e7dd;
    padding: 15px;
    border-radius: 5px;
    margin-top: 20px;
  }

  hr {
    border: 0;
    height: 1px;
    background: #007bff;
    margin: 10px 0;
  }
</style>

<div class="container">
  <h2>Online Body Line Calculator</h2>
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
  <form on:submit={calculate} autocomplete="off">
    <label for="bust">Bust Circumference ({unit}):</label>
    <input type="number" id="bust" bind:value={bust} min="1" required />

    <label for="waist">Waist Circumference ({unit}):</label>
    <input type="number" id="waist" bind:value={waist} min="1" required />

    <label for="hips">Hips Circumference ({unit}):</label>
    <input type="number" id="hips" bind:value={hips} min="1" required />

    <button type="submit">Calculate</button>
  </form>

  {#if result}
    <hr>
    <div id="result">
      {@html result}
    </div>
  {/if}
</div>