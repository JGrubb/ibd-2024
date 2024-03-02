---
title: Programmatically creating image styles in Drupal
date: 2016-06-07
tags: 
  - drupal
alias: 285-programmatically-creating-image-styles-drupal
---

Because sometimes you need to roll out an image style across 26 websites, and dammit you just don't feel like dealing with Features.

~~~php
php

/**
 \* Adds mobile_content_image style
 \*
 \* @param $sandbox
 \* @return bool
 \*/

function hook_update_N(&$sandbox) {
  $style = image_style_load('mobile_content_image');
  if (!$style) {
    $style = image_style_save([
      'name' = 'mobile_content_image',
      'label' => 'Mobile Content Image (500 x 250)'
    ]);
    $effect = [
      'name' => 'image_scale_and_crop',
      'data' => [
        'width' => '500',
        'height' => '250'
      ],
      'isid' => $style['isid'] // presumably returned by the call above?
    ];
    image_effect_save($effect);
  }
  return TRUE;
}
~~~