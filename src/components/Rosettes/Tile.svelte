<script>
  import { tweened } from 'svelte/motion';
  import { cubicOut } from 'svelte/easing';
  import { interpolate as flubberInterpolate } from 'flubber';
  import { interpolateHcl, scaleLinear } from 'd3';

  export let d = {};
  export let startX = 0;
  export let startY = 0;
  export let fillBackgroundColor = '#FFFFFF';
  export let startOpacity = 0.9;
  export let strokeColor = '#FFFFFF'
  export let selectColor = '#000000';
  export let hovered = false;
  export let labelHovered = false;
  export let anyHovered = false;

  const flyDuration = 400 * Math.random() + 800;
  const opacityFactor = Math.min(1.0, Math.max(Math.random(), 0.5));

  const opacityScale = scaleLinear()
    .domain([1, 0])
    .range([0.3, 0.9]);
  
  let x = tweened(startX, {
    duration: flyDuration,
    easing: cubicOut
  });

  let y = tweened(startY, {
    duration: flyDuration,
    easing: cubicOut
  });

  let path = tweened(null, {
    duration: flyDuration,
    easing: cubicOut,
    interpolate: flubberInterpolate
  });

  let fillColor = tweened(null, {
    duration: flyDuration,
    easing: cubicOut,
    interpolate: interpolateHcl
  });

  let opacity = tweened(startOpacity, {
    duration: flyDuration,
    easing: cubicOut
  });

  $: path.set(d.path)
  $: x.set(d.x);
  $: y.set(d.y);

  $: fillColor.set(hovered ? selectColor : d.color);

  $: {
      let o = opacityScale(opacityFactor * d.withinClusterIndex / d.cluster.length);
      if (labelHovered) {
        o = 1.0;
      }
      if (anyHovered && !labelHovered) {
        o = 0.2;
      }
      opacity.set(o);
    }
</script>

<g
  class="tile"
  transform="translate({$x} {$y})"
>
  <path
    class="tile-path-background"
    d={$path}
    fill={fillBackgroundColor}
    fill-opacity="1.0"
  />
  <path
    class="tile-path"
    d={$path}
    fill={$fillColor}
    fill-opacity={$opacity}
    stroke={strokeColor}
    stroke-width="0.5"
    stroke-opacity="0.9"
  />
</g>
