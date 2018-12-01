---
layout: post
title: Get N Items From Multiple Arrays With Condition
date: 2011-07-18 15:25
comments: true
categories: [conditional array merge, multiple arrays, PHP, php, Programming]
---
The title may be a bit confusing, but recent I have come to need a function that retrieves the latest items from my database object arrays in one of my PHP project. I have three object arrays obtained using the `mysql_fetch_object` function, and I needed to get the latest items based on the timestamp they were posted. Before you tell me I should have used better SQL queries, I should mention that the MySQL tables have different data structures, but with the same column names.

I googled around hoping that there would be some simple solution to my needs – a function that will retrieve a preset number of items based on a condition from an array and output them as a new array. But the search was taking too long and Google was showing me too many irrelevant results. This prompted me to write my own solution, as usual.

<!--more-->

```php
function getItemsFromArray($n, $inputArray, $outputArray = null) {
    if (!function_exists("cmp")) {
        function cmp($a, $b) { // function to sort by posted date, you can change this to your needs
            if ($a -> $posted ==  $b -> $posted) return 0; // if equals do nothing
            return ($a -> $posted > $b -> $posted) ? -1 : 1; // latest comes first
        }
    }
    // make sure the input array is not empty
    if (!empty($inputArray)) {
        $tempArray = $inputArray;
        if (!$outputArray) $outputArray = array();
        // sort items before processing to make sure the latest is at the start
        usort($tempArray, 'cmp');
        for ($i = 0; $i < sizeof($inputArray) && $ $i < $n; $i++) {
            // push into output array
            array_push($outputArray, $inputArray[$i]);
            // sort the output array to keep the latest items at the start of the array
            usort($outputArray, 'cmp');
            // only keep first n items, discard the rest
            array_splice($outputArray, $n);
        }
    }
    return $outputArray; // return result array
}
```

This script will output an array containing the latest items sorted by posted timestamp. If an array is provided in the $outputArray field, the script will push the items from the `$inputArray` into that array, and sort the array on each loop to make sure the output array only contains the latest items. It’s worth to note though, the compare function I wrote for sorting the arrays using the usort function was written for my usage. If you want to use this function, you’d probably want to edit that part. See below for typical usage.

```php
/* $nObj, $tObj, $aObj are object arrays */
$news = array();
$news = getItemsFromArray(5, $nObj, $news);
$news = getItemsFromArray(5, $tObj, $news);
$news = getItemsFromArray(5, $aObj, $news);
```

I hope this helps someone else facing a similar situation. I’m sure there are numerous other ways to go about this, and I’d like to hear from you guys.

*Wassalam*
