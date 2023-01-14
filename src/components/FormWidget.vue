<template>
  <FormGroup v-if="info" :id="id" :label-type="data.noLabel ? LabelType.None : info.label" v-bind="mappedProps">
    <component :is="info.class" :data="data" :current-id="id" />
  </FormGroup>
</template>

<script setup lang="ts">
import FormGroup from "@/components/FormGroup.vue";
import { has, pick } from "lodash";
import { LabelType, WidgetData } from "@/common/types";
import WidgetDropdown from "@/components/WidgetDropdown.vue";
import WidgetHeading from "@/components/WidgetHeading.vue";
import WidgetInput from "@/components/WidgetInput.vue";
import WidgetLabel from "@/components/WidgetLabel.vue";
import WidgetMultiCheckbox from "@/components/WidgetMultiCheckbox.vue";
import WidgetPicture from "@/components/WidgetPicture.vue";
import WidgetPositions from "@/components/WidgetPositions.vue";
import WidgetRadio from "@/components/WidgetRadio.vue";
import WidgetSpacer from "@/components/WidgetSpacer.vue";
import WidgetSpinbox from "@/components/WidgetSpinbox.vue";
import WidgetStopwatch from "@/components/WidgetStopwatch.vue";
import WidgetTextarea from "@/components/WidgetTextarea.vue";

const props = defineProps<{
  id: string,
  data: WidgetData
}>();

const widgetName = $computed(() => `<${props.data.name ?? "Unnamed widget"}>`);
const widgetType = $computed(() => `"${props.data.type ?? "[None]"}"`);

// Table containing metadata for each widget type
const info = {
  dropdown:      { class: WidgetDropdown,      label: LabelType.LabelTag,  required: ["name", "options"] }, //dropdown selection
  heading:       { class: WidgetHeading,       label: LabelType.None,      required: ["name"] },            //text heading
  label:         { class: WidgetLabel,         label: LabelType.None,      required: ["name"] },            //text label (same as heading?)
  text:          { class: WidgetInput,         label: LabelType.LabelTag,  required: ["name"] },            //text box
  number:        { class: WidgetInput,         label: LabelType.LabelTag,  required: ["name"] },            //standard web number entry
  checkbox:      { class: WidgetInput,         label: LabelType.LabelTag,  required: ["name"] },            //singular checkbox
  multicheckbox: { class: WidgetMultiCheckbox, label: LabelType.PlainText, required: ["name", "options"] }, //list of checkboxes
  picture:       { class: WidgetPicture,       label: LabelType.None,      required: ["file"] },            //inline picture
  positions:     { class: WidgetPositions,     label: LabelType.PlainText, required: ["name", "file"] },    //position map
  radio:         { class: WidgetRadio,         label: LabelType.PlainText, required: ["name", "options"] }, //radio button list
  spacer:        { class: WidgetSpacer,        label: LabelType.None,      required: [] },                  //empty spacer (how to increase size?)
  spinbox:       { class: WidgetSpinbox,       label: LabelType.LabelTag,  required: ["name"] },            //fancy number entry (use this)
  stopwatch:     { class: WidgetStopwatch,     label: LabelType.PlainText, required: ["name"] },            //stopwatch metric
  textarea:      { class: WidgetTextarea,      label: LabelType.LabelTag,  required: ["name"] }             //large text box (comments)
}[props.data.type];

if (info === undefined)
  throw new Error(`Unknown type ${widgetType} in widget ${widgetName}`);

for (const i of info.required)
  if (!has(props.data, i))
    throw new Error(`Required attribute "${i}" was not specified in widget ${widgetName} (type ${widgetType})`);

// Props to pass from the widget data to the sub-components
const mappedProps = pick(props.data, ["name", "align", "row", "col", "rowspan", "colspan", "labelColspan"]);
</script>
