# Allow lowering of onboarding fees

- Author(s): <!-- your GitHub @username -->
- Start Date: <!-- fill me in with today's date, YYYY-MM-DD -->
- Category: <!-- economic, technical, meta -->
- Original HIP PR: <!-- leave this empty; maintainer will fill in ID of this pull request -->
- Tracking Issue: <!-- leave this empty; maintainer will create a discussion issue -->
- Vote Requirements: veHNT

## Summary

This HIP proposes to make the $A$ factor of the subDAO utility score more granular by using the individual onboarding fee an active device paid instead of relying on a homogeneous onboarding fee. This will allow subDAOs to change their onboarding fee without either negatively affecting their subDAO utility score or artificially inflate their subDAO utility score and be subject to slashing.

## Motivation

The current definition of the subDAO utility score as specified in HIP51 is shown below. The definition does not allow the changing of the onboarding fee without significantly affecting the $A$ part of the score. The community has expressed the preference to change the onboarding fee. However, lowering the onboarding fee will significantly lower the subDAO utility score and thus subDAOs are disincentivized to do so. At the same time, increasing the onboarding fee will artificially inflate the subDAO utility score, an offense punishable by slashing as written in HIP51. This HIP proposes to change the $A$ score to align with the original intention of HIP51 while still allowing a subDAO to change their onboarding fee via their internal governance.

$\text{DAO Utility Score} = V \times D \times A$

where

$V = \text{max}(1, veHNT_{DNP})$

$D = \text{max}(1, \sqrt{\text{DNP DCs burned in USD}})$

$A = \text{max}(1, \sqrt[4]{\text{DNP Active Device Count} \times \text{DNP Device Activation Fee}})$

## Stakeholders

This change will impact the entire ecosystem as it alters the interpretation of the HIP51 subDAO utility score that is directly responsible for the distribution of HNT between subDAOs.

## Detailed Explanation

This HIP proposes to change the $A$ factor to only count the onboarding fees an active device has actually paid. If a device has paid $\$40$ but the current onboarding fee is $\$10$ the device will still be counted as $\$40$. Conversely a device that has paid $\$10$ while the current onboarding fee is $\$40$ will still be counted as $\$10$. This change will allow subDAOs to change their onboarding factor without affecting the $A$ factor of subDAO utility score that it had been awarded for previous onboarding fees.

An active device is any rewardable entity that has been onboarded and has received rewards in the past 30 days. This definition of what an _active device_ entails allows any subDAO to use their own definition of _device_ without requiring Helium DAO oversight. The use of the actual onboarding fee that was paid for a device removes the ability to onboard devices for a low onboarding fee and later game the subDAO utility score by increasing the onboarding fee for new devices.

The exact and explicit specification of the proposal is shown below. It is important to note that the remaining factors of the subDAO utility score, namely $V$ and $D$ remain unchanged.

$\mathcal{H_s} : \text{the set of all hotspots } h \text{ onboarded to subDAO } s$

$O_s(h) : \text{the onboarding fee paid for hotspot } h \text{ towards subDAO }s$

$a_s(h) =
\begin{cases}
O_s(h) & \textbf{iff } h \text{ has received rewards in the last 30 days}^{1)} \\
0 & else
\end{cases}$

$A_s : \text{the subDAO } A \text{ score for subDAO } s$

$\ ^{1)} \textbf{ iff}: \text{if and only if}$

$A_s = max\left(1, \sqrt[4]{\sum_{h \in \mathcal{H_s}} a_s(h)}\right)$

### examples

Example 1: IOT subDAO has 450k active devices of which 448k devices have paid $40 and 2k devices have paid $10 onboarding fees.

$A_{\text{IOT}} = max\left(1, \sqrt[4]{\sum_{h \in \mathcal{H_\text{IOT}}}A_{\text{IOT}}(h)}\right) = max\left(1, \sqrt[4]{448 000 \cdot 40 + 2 000 \cdot 10}\right) = max\left(1, \sqrt[4]{17 940 000}\right) \approxeq 65.08$

Example 2: MOBILE subDAO has 4000 active devices of which 3800 have paid no onboarding fees and 200 have paid $10 onboarding fees.

$A_{\text{MOBILE}} = max\left(1, \sqrt[4]{\sum_{h \in \mathcal{H_\text{MOBILE}}}A_{\text{MOBILE}}(h)}\right) = max\left(1, \sqrt[4]{3 800 \cdot 0 + 200 \cdot 10}\right) = max\left(1, \sqrt[4]{2 000}\right) \approxeq 6.69$

Example 3: WIFI subDAO has opted not to pay any onboarding fees and have 100k active devices.

$A_{\text{WIFI}} = max\left(1, \sqrt[4]{\sum_{h \in \mathcal{H_\text{WIFI}}}A_{\text{WIFI}}(h)}\right) = max\left(1, \sqrt[4]{100 000 \cdot 0}\right) = max\left(1, \sqrt[4]{0}\right) = 1$

## Drawbacks

There are no clear drawbacks to this proposal. The proposal will reinterpret the subDAO utility score as set forth by HIP51 in a more granular way that will ultimately benefit every subDAO that wants to readjust their onboarding fee to better align with the current environment.

## Rationale and Alternatives

The change this HIP proposes can be best described as a bug-fix. There are two major alternatives to this HIP, the first is leaving the bug in place where it can be exploited by a rogue subDAO in the future. The second is a major revamp of the subDAO utility score. A major revamp of the subDAO utility score takes months of discussion and modeling whereas several actors within the ecosystem have voiced their wish to change the onboarding fee sooner rather than later. Without this change the changing of a subDAOs onboarding fee is either artificially inflating the subDAO utility score (punishable by slashing) or very disincentivized by losing part of the $A$ factor.

## Unresolved Questions

None.

## Deployment Impact

The implementation of the `active-devices` will have to be altered. The `active-devices` oracle uses a database in which it stores key metrics such as `lastReward` that it can use to correctly determine the number of active devices. The `distribution` oracle uses this information to distribute HNT to the treasuries of the different subDAOs. This proposal proposes to add paid onboarding fee to this database in order to provide the `distribution` oracle with the correct values to use for the $A$ factor of the subDAO utility score.

## Success Metrics

This proposal is a success when the `distribution` and `active-devices` oracles can correctly determine the $A$ factor of the subDAO utility score based on the amount of active devices and the corresponding onboarding fee that was paid. As a consequence, this will allow the the subDAOs to set the onboarding fee via their internal governance without requiring a veHNT vote.
