---
layout: post
title: "Getting Storybook to work with Vuetify 2.0"
description: "A description of the issues I ran into with Storybook after upgrading to Vuetify 2.0, and how I fixed it."
comments: true
tags:
  - vuetify
  - vuetifyjs
  - vue
  - vuejs
  - js
  - storybook
  - storybookjs
---

This is just a short post regarding how I got Storybook to play nicely with [Vuetify][1] after upgrading to Vuetify 2.0.

As most of you already know, Vuetify 2.0 recently came out. That's awesome, but it also came with a few breaking changes, the primary of which is their move towards making Vuetify a Vue plugin, which had to be [bootstrapped to the primary Vue instance][0].

This is fine and all, except it broke my [Storybook][2] build.

![Sad day]({{ "/assets/img/2019/storybook-vuetify-broken.png" | absolute_url }}){: .center-image }

<!--more-->

As I've mentioned, Vuetify now has to be bootstrapped to the Vue app you're using it on, which means you will need to use the `addDecorator` helper method to achieve this.

```js
import { storiesOf } from '@storybook/vue';
import vuetify from '@/plugins/vuetify';
import Header from '@/components/Header.vue';

storiesOf('Header', module)
  .addDecorator(() => ({
    vuetify, // Important!
    template: '<v-app><v-content><story/></v-content></v-app>'
  }))
  .add('default', () => ({
    components: {
      Header
    },
    template: '<header/>'
  }));
```

And that's it! My Storybook build is back up and running, and everybody's happy again.

I hope this helps anyone else who ran into this issue.

[0]: https://vuetifyjs.com/en/getting-started/quick-start#bootstrapping-the-vuetify-object
[1]: https://vuetifyjs.com/
[2]: https://storybook.js.org/