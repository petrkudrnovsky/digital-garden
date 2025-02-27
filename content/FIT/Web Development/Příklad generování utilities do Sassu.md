- zdroj: Dominik Dvořák, Externity

```scss
$base-indent: 0.8rem; // schované v base.scss
$utility-classes-iterations: 5;

$utility-classes-maps: (
  mt: margin-top, // mapuje se jako $clsName, $properties
  mr: margin-right,
  mb: margin-bottom,
  ml: margin-left,
  pt: padding-top,
  pr: padding-right,
  pb: padding-bottom,
  pl: padding-left,
);

@mixin indent($properties, $multiplier: 1, $size: $base-indent, $important: '') {
  @each $property in $properties {
    #{$property}: $size * $multiplier #{$important};
  }
}

@mixin properties($properties, $value, $multiplier: 1, $important: '') {
  @if $multiplier != 1 {
    $value: $value * $multiplier;
  }

  @each $property in $properties {
    #{$property}: #{$value} #{$important};
  }
}


@each $clsName, $properties in $utility-classes-maps {
    .#{$clsName}-auto {
      @include properties($properties, 'auto', 1, !important);
    }

    @for $i from 0 through $utility-classes-iterations {
      .#{$clsName}-#{$i} {
        @include indent($properties, $i, $base-indent, !important);
      }
    }
}
```