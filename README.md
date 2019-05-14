<p align="center">
  <img src="https://imgur.com/P156wvW.png" width="400">
</p>

<p align="center">
  A VueJS Circular Slider for Laravel
</p>

# How to use

```bash
<ring-slider
  :completed-steps="8"
  :total-steps="10"
  :direction="true"
  link="{{ route('dashboard') }}">
  <template v-slot:title>{{ __('###') }}</template>
  <template v-slot:sub-title>{{ __('###') }}</template>
  <template v-slot:btn-text>{{ __('###') }}</template>
</<ring-slider>
```