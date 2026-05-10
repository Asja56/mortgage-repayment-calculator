<script lang="ts">
  import Result from "./Result.svelte";
  let currency = "USD";
  let amount: string = "";
  let term: string = "";
  let rate: string = "";
  let type = "";
  let result: { monthly: number; total: number } | null = null;
  let errors: { amount?: string; term?: string; rate?: string; type?: string } =
    {};
  const currencies = ["USD", "EUR", "GBP", "JPY", "AUD"];

  function validate() {
    errors = {};
    if (!amount || isNaN(Number(amount)) || Number(amount) <= 0) {
      errors.amount = "Please enter a valid mortgage amount.";
    } else if (Number(amount) < 0) {
      errors.amount = "Negative numbers are not allowed.";
    }
    if (!term || isNaN(Number(term)) || Number(term) <= 0) {
      errors.term = "Please enter a valid mortgage term.";
    } else if (Number(term) < 0) {
      errors.term = "Negative numbers are not allowed.";
    }
    if (!rate || isNaN(Number(rate)) || Number(rate) <= 0) {
      errors.rate = "Please enter a valid interest rate.";
    } else if (Number(rate) < 0) {
      errors.rate = "Negative numbers are not allowed.";
    }
    if (!type) {
      errors.type = "Please select a mortgage type.";
    }
    return Object.keys(errors).length === 0;
  }

  function calculate() {
    if (!validate()) {
      result = null;
      return;
    }
    const principal = Number(amount);
    const n = Number(term) * 12;
    const monthlyRate = Number(rate) / 100 / 12;
    let monthlyPayment;
    if (type === "repayment") {
      if (monthlyRate === 0) {
        monthlyPayment = principal / n;
      } else {
        monthlyPayment =
          (principal * (monthlyRate * Math.pow(1 + monthlyRate, n))) /
          (Math.pow(1 + monthlyRate, n) - 1);
      }
    } else {
      // Interest only
      monthlyPayment = principal * monthlyRate;
    }
    result = {
      monthly: monthlyPayment,
      total:
        type === "repayment"
          ? monthlyPayment * n
          : monthlyPayment * n + principal,
    };
  }
</script>

<form on:submit|preventDefault={calculate} class="mortgage-form">
  <div class="form-group">
    <label for="amount">Mortgage Amount</label>
    <div style="display: flex; align-items: center; gap: 0.5em;">
      <select
        bind:value={currency}
        aria-label="Currency"
        style="white-space: nowrap;"
      >
        {#each currencies as c}
          <option value={c}>{c}</option>
        {/each}
      </select>
      <input
        id="amount"
        type="number"
        min="0"
        step="0.01"
        bind:value={amount}
        placeholder="Enter amount"
        style="flex: 1;"
        on:input={() => { if (amount && amount.startsWith('-')) amount = amount.replace(/^-+/, ''); }}
      />
    </div>
    {#if errors.amount}
      <div class="error">{errors.amount}</div>
    {/if}
  </div>

  <div class="form-group">
    <label for="term">Mortgage term</label>
    <div style="display: flex; align-items: center; gap: 0.5em;">
      <input
        id="term"
        type="number"
        min="1"
        step="1"
        bind:value={term}
        placeholder="e.g. 25"
        style="flex: 1;"
        on:input={() => { if (term && term.startsWith('-')) term = term.replace(/^-+/, ''); }}
      />
      <span style="white-space: nowrap;">years</span>
    </div>
    {#if errors.term}
      <div class="error">{errors.term}</div>
    {/if}
  </div>

  <div class="form-group">
    <label for="term">Interest rate</label>
    <div style="display: flex; align-items: center; gap: 0.5em;">
      <input
        id="rate"
        type="number"
        min="1"
        step="1"
        bind:value={rate}
        placeholder="e.g. 2.5"
        style="flex: 1;"
        on:input={() => { if (rate && rate.startsWith('-')) rate = rate.replace(/^-+/, ''); }}
      />
      <span style="white-space: nowrap;">%</span>
    </div>
    {#if errors.rate}
      <div class="error">{errors.rate}</div>
    {/if}
  </div>

  <div class="form-group">
    <p>Type</p>
    <div style="display: flex; gap: 1em;">
      <label>
        <input type="radio" name="type" value="repayment" bind:group={type} /> Repayment</label
      >
      <label
        ><input
          type="radio"
          name="type"
          value="interest-only"
          bind:group={type}
        /> Interest only</label
      >
    </div>
    {#if errors.type}
      <div class="error">{errors.type}</div>
    {/if}
  </div>

  <button type="submit">Calculate repayments</button>
</form>


  <Result {result} {currency}></Result>


<style>
  .mortgage-form {
    max-width: 400px;
    margin: 2em auto;
    padding: 2em;
    border: 1px solid var(--border);
    border-radius: 8px;
    background: var(--bg);
    box-shadow: var(--shadow);
    display: flex;
    flex-direction: column;
    gap: 1.5em;
  }
  .form-group {
    display: flex;
    flex-direction: column;
    gap: 0.5em;
  }
  label {
    font-weight: 500;
    color: var(--text-h);
  }
  input[type="number"],
  select {
    padding: 0.5em;
    border: 1px solid var(--border);
    border-radius: 4px;
    font-size: 1em;
  }
  .error {
    color: #d32f2f;
    font-size: 0.95em;
  }
</style>
