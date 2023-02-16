# MozPromise

## Definition

https://searchfox.org/mozilla-central/source/xpcom/threads/MozPromise.h

## Explain for `Then()`

When calling MozPromise's [Then()](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#1031-1049), it will return a [ThenCommand](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#955-1028) that is used to determine different future usages, either doing `Then()` again or doing `Track()` to terminate chaining.

`ThenCommand` holds strong references to `ThenValue` and the targeted `Receiver` (MozPromise). It's just a scoped helper class, and it will add `ThenValue` into `Receiver`'s `mThevValues` which is an array to store all related `Then()` called on the targeted promise.

There are different types of [ThenValue](https://searchfox.org/mozilla-central/search?q=symbol:T_mozilla%3A%3AMozPromise%3A%3AThenValue&redirect=false) but they all inherit from [ThenValueBase](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#464-622), which only holds a strong reference to the targeted thread on which the resolve/reject method should run on.

`ThenValueBase` is inherited from [MozPromise::Request](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#445-455) which provides an interface to stop the resolve/reject method from running after the caller triggers `Disconnect()`.

## Resolve or Reject a Promise

There are several ways to resolve/reject a promise.

- Use `MozPromiseHolder`'s [methods](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#1363-1423)
- Use `CreateAndXXX` [methods](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#258-288)
- Use `MozPromise::Private`'s [methods](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#1212-1263)

The first two methods are helper methods and they will eventually trigger the third ones. After changing the private promise's [value](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#1225,1243,1261) that represents the result of the promise, [MozPromise::DispatchAll()](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#1138-1149) will be called in order to notify the result to all other `Then()` and chained promises waiting for its result.

Notifying `Then()` is done by [ThenValueBase::Dispatch](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#530-580) where a [runnable](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#469-500) will be created which holds astrong references to `ThenValueBase` and a `MozPromise` that the result belongs to.

When `Then()` receives the result, it performs [DoResolveOrReject()](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#598-611) to mark itself as completed and would execute the resolve/reject method if it's not disconnected yet. The methods will be run in [DoResolveOrRejectInternal()](https://searchfox.org/mozilla-central/search?q=DoResolveOrRejectInternal&path=) depends on the type of `ThenValueBase`.

The strong reference to `MozPromise` and `ThenValueBase` will be [released by the runnable after running resolve/reject method](https://searchfox.org/mozilla-central/rev/66aa740e65a343659a7446b890145781f1b6a344/xpcom/threads/MozPromise.h#489-491) to ensure that they will be alive during running those methods.
