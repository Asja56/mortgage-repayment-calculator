<script lang="ts">
  import emptyIllustration from "../assets/illustration-empty.svg";
  let { result, currency } = $props();
</script>

<div class="result">
  {#if result === null}
    <div class="result-empty">
      <img src={emptyIllustration} alt="calculator" />
      <h2>Results shown here</h2>

      <p>
        Complete the form and click "calculate repayments" to see what your
        monthly repayments would be.
      </p>
    </div>
  {:else}
    <div class="result-calculated">
      <h2>Your results</h2>

      <p>
        Your results are shown below based on the information you provided. To
        adjust the results, edit the form and click "calculate repayments"
        again.
      </p>

      <div class="result-section">
        <div>
          <p>Your monthly repayments</p>
          <strong style="font-size: 26px; color: var(--yellow);">
            {currency}{result.monthly.toLocaleString(undefined, {
              minimumFractionDigits: 2,
              maximumFractionDigits: 2,
            })}
          </strong>
        </div>

        <span class="line"></span>

        <div>
          <p>Total you'll repay over the term</p>
          <strong style="font-size: 20px;"
            >{currency}{result.total.toLocaleString(undefined, {
              minimumFractionDigits: 2,
              maximumFractionDigits: 2,
            })}
          </strong>
        </div>
      </div>
    </div>
  {/if}
</div>

<style>
  .result {
    max-width: 400px;
    border: 1px solid var(--slate-800);
    background-color: var(--slate-800);
    color: var(--white);
    height: stretch;
    border-radius: 0 16px 16px 64px;
    padding: 2em;

    @media (max-width: 768px) {
      box-sizing: border-box;
      max-width: none;
      border-radius: 0;
      padding-bottom: 2em;
    }

    p {
      font-size: 16px;
      padding-bottom: 8px;
      color: var(--slate-300);

      @media (max-width: 768px) {
        font-size: 14px;
      }
    }
  }

  .result-empty {
    padding: 6em 2em 0 2em;
  }

  .result-calculated {
    text-align: left;
  }

  .result-section {
    background-color: var(--slate-900);
    border-top: 2px solid var(--yellow);
    border-radius: 8px;
    padding: 1em;
    margin-top: 1em;

    div {
      padding: 0.25em;
    }
  }

  .line {
    height: 1px;
    background-color: var(--slate-300);
    opacity: 20%;
    margin: 0.5em 0;
    display: block;
  }
</style>
