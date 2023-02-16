## Then

When calling MozPromise's [Then()](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#1031-1049), it will return a [ThenCommand](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#955-1028), which can help to determine different future usage, either do `Then()` again or do `Track()` to terminate chaining.

[ThenCommand] holds strong references to `ThenValue` and the targeted `Receiver` (MozPromise). It's just a scope helper method, and it will add `ThenValue` into `Receiver`'s `mThevValues` which is an array to store all related `Then()` called on the targeted promise.

There are different types of [ThenValue](https://searchfox.org/mozilla-central/search?q=symbol:T_mozilla%3A%3AMozPromise%3A%3AThenValue&redirect=false) but they all inherit from [ThenValueBase](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#464-622), which only holds a strong reference to the targeted thread on which the resolve/reject method should run on.

`ThenValueBase` is inherited from [MozPromise::Request](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#445-455) which provides an interface to stop the resolve/reject method from running after the caller triggers `Disconnect()`.
