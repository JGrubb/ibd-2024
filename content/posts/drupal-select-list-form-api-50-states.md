---
title: Drupal select list for the Form API - 50 states
date: 2011-06-06
tags: 
  - drupal
alias: 219-drupal-select-list-form-api-50-states
---

This is the list I've spent a half hour hacking around in TextMate trying to figure out how to not manually build. Couldn't quite do it, so here it is, that you may find it and use it when building a Drupal form that has a select list of all 50 states and the District of Columbia.

~~~php
'#options' => array(
 '' => t('Please Select'),
 'AL' => t('Alabama'),
 'AK' => t('Alaska'),
 'AZ' => t('Arizona'),
 'AR' => t('Arkansas'),
 'CA' => t('California'),
 'CO' => t('Colorado'),
 'CT' => t('Connecticut'),
 'DE' => t('Delaware'),
 'DC' => t('District of Columbia'),
 'FL' => t('Florida'),
 'GA' => t('Georgia'),
 'HI' => t('Hawaii'),
 'ID' => t('Idaho'),
 'IL' => t('Illinois'),
 'IN' => t('Indiana'),
 'IA' => t('Iowa'),
 'KS' => t('Kansas'),
 'KY' => t('Kentucky'),
 'LA' => t('Louisiana'),
 'ME' => t('Maine'),
 'MD' => t('Maryland'),
 'MA' => t('Massachusetts'),
 'MI' => t('Michigan'),
 'MN' => t('Minnesota'),
 'MS' => t('Mississippi'),
 'MO' => t('Missouri'),
 'MY' => t('Montana'),
 'NE' => t('Nebraska'),
 'NV' => t('Nevada'),
 'NH' => t('New Hampshire'),
 'NJ' => t('New Jersey'),
 'NM' => t('New Mexico'),
 'NY' => t('New York'),
 'NC' => t('North Carolina'),
 'ND' => t('North Dakota'),
 'OH' => t('Ohio'),
 'OK' => t('Oklahoma'),
 'OR' => t('Oregon'),
 'PA' => t('Pennsylvania'),
 'RI' => t('Rhode Island'),
 'SC' => t('South Carolina'),
 'SD' => t('South Dakota'),
 'TN' => t('Tennessee'),
 'TX' => t('Texas'),
 'UT' => t('Utah'),
 'VT' => t('Vermont'),
 'VA' => t('Virginia'),
 'WA' => t('Washington'),
 'WV' => t('West Virginia'),
 'WI' => t('Wisconsin'),
 'WY' => t('Wyoming'),
),
~~~