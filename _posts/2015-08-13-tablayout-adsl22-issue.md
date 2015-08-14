---
layout: post
title: TabLayout ADSLv22.2.1 issue
author: Oyewale Oyediran
feature-image: event-driven-architecture.png
tags:
- android
- adsl
- issue
- appcompat
- dev
---

I have been battling with some rougue piece of code which I just discovered to be a known bug in the Android Design Support Library v22.2.1.
The TabLayout in a Fragment seems to loose its Tabs when you add/replace the fragment, and shows up when only when the fragment is recreated.

![ADSL Issue](/images/adsl-issue.png)

It is a known [issue], and a permanent workaround is expected in a future release of the library. So Chris Baines recommends a workaround, by replacing your standard setupWithViewPager method,

```java
@Override
public void onViewCreated(View view, Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);
    tabLayout.setupWithViewPager(viewPager);
}
```

with,

```java
@Override
public void onViewCreated(View view, Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);   

    ....  
    
    if (ViewCompat.isLaidOut(tabLayout)) {
        tabLayout.setupWithViewPager(viewPager);
    } else {
        tabLayout.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {

            @Override
            public void onLayoutChange(View v, int left, int top, int right, 
              int bottom, int oldLeft, int oldTop, int oldRight, int oldBottom) {
                tabLayout.setupWithViewPager(viewPager);

                tabLayout.removeOnLayoutChangeListener(this);
            }
        });
    }
}
```

I hope this saves you some debugging time.


[issue]:https://code.google.com/p/android/issues/detail?id=180462