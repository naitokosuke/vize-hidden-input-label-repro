# vize-hidden-input-label-repro

Minimal reproduction for a false positive in [vize](https://github.com/ubugeeei-prod/vize)'s `a11y/form-control-has-label`.

A form control with the [`hidden` attribute](https://html.spec.whatwg.org/multipage/interaction.html#the-hidden-attribute) is not rendered and not in the accessibility tree. A common case is a file input opened from a labelled button. The rule still asks it for a label.

## Steps

```sh
pnpm install
pnpm lint
```

[`src/HiddenInput.vue`](./src/HiddenInput.vue):

```vue
<template>
  <input hidden type="file" />
</template>
```

## Expected

No warning.

## Actual

```
Patina lint report: 1 warning in 1 file

src/HiddenInput.vue
  src/HiddenInput.vue:2:3 warning a11y/form-control-has-label <input> elements must have an associated label
    Reference: docs/content/rules/accessibility.md
```

## Notes

[`is_exempt_input_type`](https://github.com/ubugeeei-prod/vize/blob/v0.476.0/crates/vize_patina/src/rules/a11y/form_control_has_label.rs#L40-L53) exempts `type="hidden" | "submit" | "reset" | "button" | "image"`. The rule doesn't look at the `hidden` attribute anywhere, so `<select hidden>` and `<textarea hidden>` are flagged too.

## Environment

vize 0.476.0, Node v26.10.0, pnpm 12.9.0, macOS (arm64)
