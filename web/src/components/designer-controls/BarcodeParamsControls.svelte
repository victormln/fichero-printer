<script lang="ts">
  import { Barcode } from "$/fabric-object/barcode";
  import { tr } from "$/utils/i18n";
  import MdIcon from "$/components/basic/MdIcon.svelte";

  interface Props {
    selectedBarcode: Barcode;
    editRevision: number;
    valueUpdated: () => void;
  }

  let { selectedBarcode, editRevision, valueUpdated }: Props = $props();

  let randomLength = $state(parseInt(localStorage.getItem("barcode_random_length") || "6", 10));
  let randomType = $state<"alnum" | "num">((localStorage.getItem("barcode_random_type") || "num") as "alnum" | "num");

  $effect(() => {
    localStorage.setItem("barcode_random_length", randomLength.toString());
    localStorage.setItem("barcode_random_type", randomType);
  });

  function generateRandomCode() {
    const charsAlnum = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    const charsNum = "0123456789";
    const chars = randomType === "num" ? charsNum : charsAlnum;
    let result = "";
    for (let i = 0; i < randomLength; i++) {
      result += chars.charAt(Math.floor(Math.random() * chars.length));
    }
    selectedBarcode?.set("text", result);
    if (selectedBarcode?.canvas) {
      selectedBarcode.canvas.centerObject(selectedBarcode);
    }
    valueUpdated();
  }
</script>

<input type="hidden" value={editRevision}>

<div class="input-group input-group-sm flex-nowrap">
  <span class="input-group-text" title={$tr("params.barcode.encoding")}><MdIcon icon="code" /></span>
  <select
    class="form-select"
    value={selectedBarcode.encoding}
    onchange={(e) => {
      selectedBarcode?.set("encoding", e.currentTarget.value ?? "EAN13");
      valueUpdated();
    }}>
    <option value="EAN13">EAN13</option>
    <option value="CODE128B">Code128 B</option>
  </select>
</div>

<div class="input-group input-group-sm flex-nowrap">
  <span class="input-group-text" title={$tr("params.barcode.scale")}>
    <MdIcon icon="settings_ethernet" />
  </span>
  <input
    class="barcode-width form-control"
    type="number"
    min="0.1"
    step="0.1"
    value={selectedBarcode.scaleFactor}
    oninput={(e) => {
      selectedBarcode?.set("scaleFactor", e.currentTarget.valueAsNumber ?? 1);
      valueUpdated();
    }} />
</div>

<button
  class="btn btn-sm {selectedBarcode.printText ? 'btn-secondary' : ''}"
  title={$tr("params.barcode.enable_caption")}
  onclick={() => {
    selectedBarcode?.set("printText", !selectedBarcode.printText);
    valueUpdated();
  }}>
  123
</button>

<div class="input-group input-group-sm flex-nowrap">
  <span class="input-group-text" title={$tr("params.barcode.font_size")}>
    <MdIcon icon="format_size" />
  </span>
  <input
    class="barcode-width form-control"
    type="number"
    min="1"
    value={selectedBarcode.fontSize}
    oninput={(e) => {
      selectedBarcode?.set("fontSize", e.currentTarget.valueAsNumber ?? 12);
      valueUpdated();
    }} />
</div>

{#if selectedBarcode.encoding === "EAN13"}
  <div class="input-group input-group-sm flex-nowrap">
    <span class="input-group-text" title={$tr("params.barcode.content")}><MdIcon icon="view_week" /></span>
    <input
      class="barcode-content form-control"
      maxlength="12"
      value={editRevision !== -1 ? selectedBarcode.text : ""}
      oninput={(e) => {
        selectedBarcode?.set("text", e.currentTarget.value);
        valueUpdated();
      }} />
  </div>
{:else}
  <textarea
    class="barcode-content form-control"
    value={editRevision !== -1 ? selectedBarcode.text : ""}
    oninput={(e) => {
      selectedBarcode?.set("text", e.currentTarget.value);
      valueUpdated();
    }}></textarea>
  {#if selectedBarcode.encoding === "CODE128B"}
    <div class="mt-2 p-2 border rounded">
      <div class="d-flex align-items-center mb-2 gap-2">
        <MdIcon icon="casino" /> <strong>{$tr("params.barcode.random_generator")}</strong>
      </div>
      
      <div class="d-flex gx-2 align-items-center gap-2 mb-2">
        <label class="form-label mb-0 col-auto">{$tr("params.barcode.random_length")}:</label>
        <input 
          type="number" 
          class="form-control form-control-sm" 
          style="width: 70px;"
          min="1" 
          max="100" 
          bind:value={randomLength} />
      </div>
      
      <div class="d-flex gx-2 align-items-center gap-2 mb-2">
        <label class="form-label mb-0 col-auto">{$tr("params.barcode.random_type")}:</label>
        <select class="form-select form-select-sm" bind:value={randomType}>
          <option value="num">{$tr("params.barcode.random_type.num")}</option>
          <option value="alnum">{$tr("params.barcode.random_type.alnum")}</option>
        </select>
      </div>
      
      <button 
        class="btn btn-sm btn-outline-secondary w-100 mt-1 d-flex justify-content-center align-items-center gap-1" 
        onclick={generateRandomCode}>
        {$tr("params.barcode.generate_random")}
      </button>
    </div>
  {/if}
{/if}

<style>
  .input-group {
    width: fit-content;
  }

  textarea.barcode-content {
    height: 100px;
  }

  input.barcode-width {
    max-width: 64px;
  }
</style>
