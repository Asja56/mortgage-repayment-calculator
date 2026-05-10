<script lang="ts">
  let { result, currency } = $props();
</script>

<div class="result">
  {#if result === null}
    <h3>Results shown here</h3>

    <p>
      Complete the form and click "calculate repayments" to see what your
      monthly repayments would be.
    </p>
  {:else}
    <h3>Your results</h3>

    <p>
      Your results are shown below based on the information you provided. To
      adjust the results, edit the form and click "calculate repayments" again.
    </p>

    <p>Your monthly repayments</p>

    <strong
      >{result.monthly.toLocaleString(undefined, {
        style: "currency",
        currency,
      })}</strong
    >

    Total you'll repay over the term
    <strong
      >{result.total.toLocaleString(undefined, {
        style: "currency",
        currency,
      })}</strong
    >
  {/if}
</div>

<style>
  .result {
    margin: 2em auto;
    max-width: 400px;
    padding: 1.5em;
    border: 1px solid var(--accent-border);
    border-radius: 8px;
    background: var(--accent-bg);
    color: var(--text-h);
  }
</style>
