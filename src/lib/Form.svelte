<script lang="ts">
  import Result from "./Result.svelte";
  import buttonIcon from "../assets/icon-calculator.svg";

  let currency = "£";
  let amount: string = "";
  let term: string = "";
  let rate: string = "";
  let type = "";
  let result: { monthly: number; total: number } | null = null;
  let errors: { amount?: string; term?: string; rate?: string; type?: string } =
    {};
  const currencies = ["€", "$", "£"];

  function validate() {
    errors = {};
    if (!amount || isNaN(Number(amount)) || Number(amount) <= 0) {
      errors.amount = "This field is required";
    }
    if (!term || isNaN(Number(term)) || Number(term) <= 0) {
      errors.term = "This field is required";
    }
    if (!rate || isNaN(Number(rate)) || Number(rate) <= 0) {
      errors.rate = "This field is required";
    }
    if (!type) {
      errors.type = "This field is required";
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

  function resetForm() {
    currency = "£";
    amount = "";
    term = "";
    rate = "";
    type = "";
    result = null;
    errors = {};
  }
</script>

<form on:submit|preventDefault={calculate} class="mortgage-form">
  <div class="form-header">
    <h2>Mortgage Calculator</h2>
    <a class="clear-all" on:click|preventDefault={resetForm} href={null}
      >Clear all
    </a>
  </div>
  <div class="form-group">
    <label class="label" for="amount">Mortgage Amount</label>
    <div class="input" class:error={!!errors.amount}>
      <select
        bind:value={currency}
        aria-label="Currency"
        class="input-type input-type-select"
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
        on:input={() => {
          if (amount && String(amount).startsWith("-"))
            amount = String(amount).replace(/^-+/, "");
        }}
      />
    </div>
    {#if errors.amount}
      <div class="error">{errors.amount}</div>
    {/if}
  </div>

  <div class="years-percentage-row">
    <div class="form-group">
      <label class="label" for="term">Mortgage Term</label>
      <div class="input" class:error={!!errors.term}>
        <input
          id="term"
          type="number"
          min="1"
          step="1"
          bind:value={term}
          placeholder="e.g. 25"
          on:input={() => {
            if (term && String(term).startsWith("-"))
              term = String(term).replace(/^-+/, "");
          }}
        />
        <span class="input-type">years</span>
      </div>
      {#if errors.term}
        <div class="error">{errors.term}</div>
      {/if}
    </div>

    <div class="form-group">
      <label class="label" for="rate">Interest Rate</label>
      <div class="input" class:error={!!errors.rate}>
        <input
          id="rate"
          type="number"
          min="1"
          step="0.01"
          bind:value={rate}
          placeholder="e.g. 2.5"
          on:input={() => {
            if (rate && String(rate).startsWith("-"))
              rate = String(rate).replace(/^-+/, "");
          }}
        />
        <span class="input-type">%</span>
      </div>
      {#if errors.rate}
        <div class="error">{errors.rate}</div>
      {/if}
    </div>
  </div>

  <div class="form-group">
    <p class="label">Mortgage Type</p>
    <div class="form-group">
      <label class="check-option">
        <input type="radio" name="type" value="repayment" bind:group={type} /> Repayment
      </label>
      <label class="check-option">
        <input
          type="radio"
          name="type"
          value="interest-only"
          bind:group={type}
        />
        Interest only
      </label>
    </div>
    {#if errors.type}
      <div class="error">{errors.type}</div>
    {/if}
  </div>

  <button type="submit" class="button">
    <img src={buttonIcon} alt="icon" /> Calculate Repayments
  </button>
</form>

<Result {result} {currency}></Result>

<style>
  .mortgage-form {
    max-width: 400px;
    padding: 2em;
    border-radius: 16px 0 0 16px;
    display: flex;
    flex-direction: column;
    gap: 1.5em;

    @media (max-width: 768px) {
      box-sizing: border-box;
      width: 100%;
      margin: 0 2em;
      max-width: none;
      border-radius: none;

      align-items: center;
    }
  }

  .button {
    border-radius: 24px;
    background-color: var(--yellow);
    font-weight: 600;
    display: flex;
    gap: 0.5rem;
    align-items: center;
    width: fit-content;
    padding: 8px 24px;
    border: none;
  }

  .form-header {
    display: flex;
    justify-content: space-between;
    width: 100%;

    @media (max-width: 768px) {
      flex-direction: column;
      justify-content: space-evenly;
      align-items: flex-start;
    }
  }

  .clear-all {
    color: var(--slate-700);
    text-decoration: underline;
    cursor: pointer;
  }

  .form-group {
    display: flex;
    flex-direction: column;
    gap: 0.5em;
    align-items: flex-start;
    width: 100%;
  }

  .years-percentage-row {
    display: flex;
    gap: 0.5em;
    flex-direction: row;

    @media (max-width: 768px) {
      flex-direction: column;
      width: 100%;
    }
  }

  .input-type {
    white-space: nowrap;
    background-color: var(--slate-100);
    padding: 2px;
    padding: 0.5em 0.75em;
    border-radius: 0 4px 4px 0;
  }

  .input-type-select {
    border-radius: 4px 0 0 4px;
  }

  .label {
    color: var(--slate-700);
    font-size: 14px;
  }

  .input {
    display: flex;
    border-radius: 4px;
    border: 1px solid var(--slate-700);
    width: 100%;

    &:focus-within {
      border: 1px solid var(--yellow);
    }
  }

  .input:focus-within select,
  .input:focus-within .input-type {
    background-color: var(--yellow);
  }

  input[type="number"],
  select {
    padding: 0.5em;
    border: none;
    font-size: 1em;
    font-weight: 600;
  }

  input[type="number"] {
    width: 100%;
    border-radius: inherit;
  }

  .input-select {
    border-radius: inherit;
  }

  select {
    appearance: none;
  }

  input:focus,
  input:focus-visible,
  select:focus-visible,
  select:focus {
    border: none;
    outline: none;
  }

  input[type="number"]::-webkit-inner-spin-button,
  input[type="number"]::-webkit-outer-spin-button {
    opacity: 0;
  }

  .error {
    color: var(--red);
    font-size: 0.95em;
  }

  .input.error {
    border: 1px solid var(--red);
  }

  .input.error select,
  .input.error .input-type {
    background-color: var(--red);
    color: var(--white);
  }

  .check-option {
    border: 1px solid var(--slate-700);
    border-radius: 4px;
    padding: 8px;
    width: 100%;
    box-sizing: border-box;
    align-self: flex-start;
    font-weight: 600;
    display: flex;
    gap: 0.5em;
    align-items: center;
  }

  .check-option:has(input[type="radio"]:checked) {
    background-color: #fafae0;
  }

  input[type="radio"]:checked {
    appearance: none;
    width: 0.8em;
    height: 0.8em;
    border-radius: 50%;
    border: 0.15em solid #fafae0;
    box-shadow: 0 0 0 0.1em var(--yellow);
    background-color: var(--yellow);
  }
</style>
