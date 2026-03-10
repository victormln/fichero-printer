<script lang="ts">
  import BrowserWarning from "$/components/basic/BrowserWarning.svelte";
  import LabelDesigner from "$/components/LabelDesigner.svelte";
  import PrinterConnector from "$/components/PrinterConnector.svelte";
  import { locale, locales, tr } from "$/utils/i18n";
  import DebugStuff from "$/components/DebugStuff.svelte";
  import MdIcon from "$/components/basic/MdIcon.svelte";
  import LabelPropsEditor from "$/components/designer-controls/LabelPropsEditor.svelte";
  import CsvControl from "$/components/designer-controls/CsvControl.svelte";
  import SavedLabelsMenu from "$/components/designer-controls/SavedLabelsMenu.svelte";
  import { DEFAULT_LABEL_PROPS } from "$/defaults";
  import type { LabelProps } from "$/types";
  import { CustomCanvas } from "$/fabric-object/custom_canvas";

  // eslint-disable-next-line no-undef
  const appCommit = __APP_COMMIT__;
  // eslint-disable-next-line no-undef
  const buildDate = __BUILD_DATE__;

  let debugStuffShow = $state<boolean>(false);
  let designer = $state<any>();
  let labelProps = $state<LabelProps>(DEFAULT_LABEL_PROPS);
  let csvEnabled = $state<boolean>(false);
  let fabricCanvas = $state<CustomCanvas | undefined>();
</script>

<div class="container my-2">
  <div class="row align-items-center mb-3">
    <div class="col">
      <h1 class="title">
        <img src="{import.meta.env.BASE_URL}logo.png" alt="Fichero" class="logo" />
      </h1>
    </div>
    <div class="col-md-6 d-flex gap-2 align-items-center justify-content-end">
      {#if designer}
        <div class="d-flex gap-1">
          <LabelPropsEditor {labelProps} onChange={designer.onUpdateLabelProps} />
          <CsvControl bind:enabled={csvEnabled} onPlaceholderPicked={designer.onCsvPlaceholderPicked} />
          <SavedLabelsMenu
            canvas={fabricCanvas!}
            onRequestLabelTemplate={designer.exportCurrentLabel}
            onLoadRequested={designer.onLoadRequested}
            {csvEnabled} />
        </div>
      {/if}
      <PrinterConnector />
    </div>
  </div>
  <div class="row">
    <div class="col">
      <BrowserWarning />
    </div>
  </div>

  <div class="row">
    <div class="col">
      <LabelDesigner bind:this={designer} bind:labelProps bind:csvEnabled bind:fabricCanvas />
    </div>
  </div>
</div>

<div class="footer text-end text-secondary p-3">
  <div>
    <select class="form-select form-select-sm text-secondary d-inline-block w-auto" bind:value={$locale}>
      {#each Object.entries(locales) as [key, name] (key)}
        <option value={key}>{name}</option>
      {/each}
    </select>
  </div>
  <div>
    {#if appCommit}
      <a class="text-secondary" href="https://github.com/mohamedha/fichero-printer/commit/{appCommit}">
        {appCommit.slice(0, 6)}
      </a>
    {/if}
    {$tr("main.built")}
    {buildDate}
  </div>
  <div>
    <a class="text-secondary" href="https://github.com/mohamedha/fichero-printer">{$tr("main.code")}</a>
    <button class="text-secondary btn btn-link p-0" onclick={() => debugStuffShow = true}>
      <MdIcon icon="bug_report" />
    </button>
  </div>
</div>

{#if debugStuffShow}
  <DebugStuff bind:show={debugStuffShow} />
{/if}

<style>
  .logo {
    height: 1.4em;
    vertical-align: middle;
    margin-right: 0.2em;
    border-radius: 4px;
  }

  .footer {
    position: absolute;
    bottom: 0;
    right: 0;
    z-index: -1;
  }

  @media only screen and (max-device-width: 540px) {
    .footer {
      position: relative !important;
      z-index: 0 !important;
    }
  }
</style>
