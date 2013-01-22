# Pilot

A mixin library for Sass providing values in px and rem.

Note: There is no error output. I assume you have an advanced understanding of Sass and CSS as well.

## Constants

	$base-font-size-px: 16 !default;
	$base-spacing-px: 24 !default;
	$base-font-size: $base-font-size-px / 10;
	$base-spacing: $base-spacing-px / 10;

Only edit the first two if you need to and do that in your custom stylesheet. The Pilot file remains untouched.

## Functions

### stripZero

	@function stripZero($value) {
		@if $value == "0px" or $value == "0rem" {
			@return 0;
		}
		@else {
			@return $value;
		}
	}

This function is used to strip away the unit if the value is zero.

## Mixins

### font-size

	@mixin font-size($size: $base-font-size-px, $height: 1) {
		font-size: stripZero($size + px);
		font-size: stripZero($size / 10 + rem);
		line-height: round($base-spacing * $height / $size * 10 * 100) / 100;
	}

Pass your desired <code>font-size</code> and a multiple of the <code>$base-spacing-px</code> and the <code>line-height</code> will be calculated accordingly.

Example:

	h1 {
		@include font-size(28, 2);
	}

Compiles to:

	h1 {
		font-size: 28px;
		font-size: 2.8rem;
		line-height: 1.71;
	}

### shorthand

	@mixin shorthand($property, $value1, $value2: false, $value3: false, $value4: false) {
		@if $value4 and ($value1 != 0 or $value2 != 0 or $value3 != 0 or $value4 != 0) {
			#{$property}: stripZero($value1 + px) stripZero($value2 + px) stripZero($value3 + px) stripZero($value4 + px);
			#{$property}: stripZero($value1 / 10 + rem) stripZero($value2 / 10 + rem) stripZero($value3 / 10 + rem) stripZero($value4 / 10 + rem);
		}
		@else if $value3 and ($value1 != 0 or $value2 != 0 or $value3 != 0) {
			#{$property}: stripZero($value1 + px) stripZero($value2 + px) stripZero($value3 + px);
			#{$property}: stripZero($value1 / 10 + rem) stripZero($value2 / 10 + rem) stripZero($value3 / 10 + rem);
		}
		@else if $value2 and ($value1 != 0 or $value2 != 0) {
			#{$property}: stripZero($value1 + px) stripZero($value2 + px);
			#{$property}: stripZero($value1 / 10 + rem) stripZero($value2 / 10 + rem);
		}
		@else if $value1 and $value1 != 0 {
			#{$property}: stripZero($value1 + px);
			#{$property}: stripZero($value1 / 10 + rem);
		}
	}

This mixin accepts a property and up to four values. Use this for the shorthand declarations for <code>margin</code>, <code>padding</code>, or <code>border-radius</code>.

Example:

	.box {
		@include shorthand(padding, 10, 20);
	}

Comiles to:

	.box {
		padding: 10px 20px;
		padding: 1rem 2rem;
	}

### leading

	@mixin leading($value: $base-spacing-px, $property: margin, $side: top) {
		#{$property}-#{$side}: stripZero($value + px);
		#{$property}-#{$side}: stripZero($value / 10 + rem);
	}

By default this mixin adds leading <code>margin</code> with the value of <code>$base-spacing-px</code>. You can modify the value to be <code>padding</code> and the side to right, bottom, or left.

Example:

	li + li {
		@include leading(20, margin, left);
	}

Compiles to:

	li + li {
		margin-left: 20px;
		margin-left: 2rem;
	}

### border

	@mixin border($value: 1, $style: solid, $side: false) {
		@if not $side {
			border: {
				width: stripZero($value + px);
				width: stripZero($value / 10 + rem);
				style: $style;
			}
		}
		@else {
			border-#{$side}: {
				width: stripZero($value + px);
				width: stripZero($value / 10 + rem);
				style: $style;
			}
		}
	}

The default output would be a 1px border to all sides. You can change that by simply passing a side to the mixin.

Example:

	.alert {
		@include border(3, solid, top);
	}

Compiles to:

	.alert {
		border-top-width: 3px;
		border-top-width: 0.3rem;
		border-top-style: solid;
	}

### box-shadow

	@mixin box-shadow($h-off: 2, $v-off: 2, $blur: 4, $spread: 0, $color: rgba(0,0,0,0.5), $inset: false) {
		@if $inset {
			box-shadow: stripZero($h-off + px) stripZero($v-off + px) stripZero($blur + px) stripZero($spread + px) $color inset;
			box-shadow: stripZero($h-off / 10 + rem) stripZero($v-off / 10 + rem) stripZero($blur / 10 + rem) stripZero($spread / 10 + rem) $color inset;
		}
		@else {
			box-shadow: stripZero($h-off + px) stripZero($v-off + px) stripZero($blur + px) stripZero($spread + px) $color;
			box-shadow: stripZero($h-off / 10 + rem) stripZero($v-off / 10 + rem) stripZero($blur / 10 + rem) stripZero($spread / 10 + rem) $color;
		}
	}

This mixin sets your desired <code>box-shadow</code>. By default the output would be a <code>box-shadow</code> with 2px offset, and a 4px blur of 50% black.

Example:

	.box {
		@include box-shadow(3, 6, 10, 0, rgba(0,0,0,0.3) inset);
	}

Compiles to:

	.box {
		box-shadow: 3px 6px 10px 0 rgba(0, 0, 0, 0.3) inset;
		box-shadow: 0.3rem 0.6rem 1rem 0 rgba(0, 0, 0, 0.3) inset;
	}

### position

	@mixin position($type, $coords) {
		$top: nth($coords, 1);
		$right: nth($coords, 2);
		$bottom: nth($coords, 3);
		$left: nth($coords, 4);

		position: $type;
		@if $top {
			@if $top == auto {
				top: $top;
			}
			@else {
				top: stripZero($top + px);
				top: stripZero($top / 10 + rem);
			}
		}
		@if $right {
			@if $right == auto {
				right: $right;
			}
			@else {
				right: stripZero($right + px);
				right: stripZero($right / 10 + rem);
			}
		}
		@if $bottom {
			@if $bottom == auto {
				bottom: $bottom;
			}
			@else {
				bottom: stripZero($bottom + px);
				bottom: stripZero($bottom / 10 + rem);
			}
		}
		@if $left {
			@if $left == auto {
				left: $left;
			}
			@else {
				left: stripZero($left + px);
				left: stripZero($left / 10 + rem);
			}
		}
	}

Passing a property and exactly four coordinates positions an element the way you want it. In most cases you only need two coordinates. Give the other coords a value of <code>false</code> to hide them from the stylesheet.

Example:

	.logo {
		@include position(absolute, 10, 20, false, false);
	}

Compiles to:

	.logo {
		position: absolute;
		top: 10px;
		top: 1rem;
		right: 20px;
		right: 2rem;
	}