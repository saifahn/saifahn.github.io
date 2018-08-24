---
layout: post
title:  "Accidentally passing arrays by reference in React"
date:   2018-08-24 21:00 +0100
author: "Sean"
categories: 
---

The last couple of posts have been simply documenting my process step by step. This is hardly interesting, both for people to read and for myself to write. I'm going to refocus what the blog will contain.

I'm going to write about the issues and problems that I come across in my projects and specific areas that I've researched to try and solve them. I may include a bit of background with each post, but I think having a succinct and to-the-point message for each post will give the blog a clear direction and actually make it useful (hopefully).

This time, I'm talking about one of my Magic: the Gathering projects. The project gathers a set of cards and displays them conditionally based on different properties and sorting toggles. Below is the main filter and sort function:

```javascript
filterCards = () => {
    const { cards } = this.state;
    const { mana, sortBy } = this.state.filters;
    let cardsToShow = cards;
    if (mana) {
      cardsToShow = cardsToShow.filter(card => (
        this.filterCardByMana(card, mana)
      ));
    }
    if (sortBy.indexOf('cmc') !== -1) {
      cardsToShow = this.sortByCMC(cardsToShow);
    }
    if (sortBy.indexOf('colour') !== -1) {
      cardsToShow = this.sortByColour(cardsToShow);
    }
    this.setState({ cardsToShow });
  }
```

For some reason, `this.state.cards` was being changed, even though none of the functions actually called `this.setState()` on it. After pondering and scouring the internet for answers, I discovered the problem.

I used `let cardsToShow = cards` with the intention to set a variable as a copy of the cards from `state` before filtering and sorting it. Filtering didn't cause a problem as it the filter function returned a new array. However,  the sorting functions `sortByCMC` and `sortByColour` used the method `Array.prototype.sort`, which altered the array in place. The reference of `this.state.cards` was passed all the way to the function, and the array on the component state was changed.

Using `let cardsToShow = cards.slice()` gets a copy of the array as a value rather than reference. This gives me the desired behaviour and lets all my tests pass. Success!

I think the takeaways from this are:

1. `Array.prototype.sort`, e.g. `cards.sort()` sorts an array in place and doesn't create a new one. This means your original data is changed, which you need to be aware of.
2. In some situations in Javascript, arrays are passed by reference.

Good things to know, and hopefully I won't get stuck on a problem about passing values for so long.