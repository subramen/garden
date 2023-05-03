---
title: "Why do people contribute to OSS for free?"
date: 2023-03-10T11:52:25-04:00
draft: true
summary: 
tags: ['opensource']
---

... is what I asked myself when I joined the Meta Open Source team. In the 2000s, this novel engineering paradigm confounded traditional economists, and they set about studying it. As I didn't have first-hand experience maintaining a community project, I leveraged research by information technologists, sociologists and economists to make sense of this dynamic. Sharing my takeaways here.

{{< figure src="../../images/xkcd-oss-maintainer.jpg" align="center" caption="Obligatory XKCD" >}}

## OSS Work is Tough
- The quality bar for submissions is much higher than a regular corporate job
- Lot of legwork to understand the project and people working on it
- Communication can be fairly irregular at times, making it harder to meaningfully contribute

But people still want to dedicate their time and effort for free. Why?

## Motivations
Motivations to contribute overlap on a spectrum from intrinsic (personally rewarding) to extrinsic (earn a reward / avoid punishment). Intrinsic motivators are generally considered to be stronger predictors of long-term effort because of how it is inherently fulfilling.

{{< figure src="../../images/oss-motivators.jpeg" align="center" caption="Motivators range from intrinsic to extrinsic" >}}

When designing incentives to encourage participation, project owners can choose from 
- extrinsic incentives like paying them, or broadcasting positive aspects of their participation (like expertise, reliability, community spirit), or 
- aid their intrinsic desire to contribute by not only reducing the friction of participation but also nurturing an inherent sense of belonging and purpose.

Motivating contributors intrinsically is of course harder, but in this note we'll see why it's worth the work, and some first principles that can guide strategy.

## Why Intrinsic Motivation?
Intrinsic motivation is associated with exploring and learning. Because these activities are linked to dopamine systems, positive experiences here get reinforced into habits and personal identities [[0](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5364176/)]. 

Extrinsic incentives are still useful in certain contexts and can serve as a complementary tool for enhancing intrinsic motivation. The effectiveness of extrinsic incentives can depend on the type of activity and how they are framed and delivered. 

In open source specifically, two motivators appear to explain long-term consistent participation: i) being a member of a community of practice, and ii) having the means to construct identities in the community [[1](https://dl.acm.org/doi/abs/10.1287/mnsc.1060.0553)].


## Participating in a community of practice
A [community of practice](https://en.wikipedia.org/wiki/Community_of_practice) provides a platform to learn and share skills. Participants improve their skills and knowledge by participating and learning from each other. As they participate, they gain opportunities to socialize aspects they want to be known for (reliability, expertise, team-player etc.), thereby constructing their identity in the community. 

When their constructed identities are acknowledged and the value of their participation cherished, contributors are naturally motivated. This leads to a reinforcing loop, and they will continue to participate and strengthen their constructed identities. This in turn fosters a sense of belonging and purpose.

Healthy participation is key to growing healthy communities. The rate of participation is found to be a strong predictor for contributor productivity. A study ( analyzing 133 core developers from 40 OSS projects found that participants ramped up faster when they socialized more with the community [[2](https://journals.sagepub.com/doi/10.1177/1094428110375002)] [pdf](https://www.statmodel.com/download/2011-ORM-14-1.pdf)):

{{< figure src="../../images/oss-socialization-rates.jpg" align="center" caption="Socialization rates are closely correlated with onboarding time" >}}


## Aiding identity construction
By simply participating and communicating, members are sharing what they want to be known for. Perhaps they are experts in an area and have insightful commentary on relevant discussions, or they enjoy sharing what they know, or they might be quiet but consistent and reliable in their work. 

Projects can provide tools to encourage and ease the process of identity construction. One familiar example is the Contributing and Code of Conduct pages. These documents signal the identity types that are valued by the community. New participants use these as guardrails when forming their identities in the community.

Another ubiquitous example is that of the discussion forum. Every project has a place the community can congregate and communicate with, where participants can cultivate their personas. Often projects will have a community manager whose role is to facilitate communication and ease the process of identity formation.

A more proactive approach "nudges" community members (perhaps extrinsically) towards behaviors/identities that bring value to the product. For example, [huggingface](https://huggingface.co/spaces) gamifies contributions to their ML Hub via leaderboards and social media, Quora has a [range of badges](https://www.quora.com/What-types-of-badges-can-you-get-on-Quora) that acknowledge and highlight valuable activities.

Not all strategies are appropriate for every project, and the adopted strategy should be a reflection of the project's own identity. A serious, geeky project runs the risk of alienating the existing community by adopting gamification or a radically different tone/voice to highlight valuable members in the community.


## In closing
Inculcating an inherent sense of enjoyment is the holy grail for teachers, managers, leaders and open source project owners. Because the community is a mosaic - contributors aren't fungible - it makes sense to approach this from first principles. Once project owners identify their own vision of the community, they can institute processes to build the desired community of practice and provide tools to help participants form their distinct personas that help the project grow.

----

## References
- [The Emerging Neuroscience of Intrinsic Motivation: A New Frontier in Self-Determination Research](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5364176/)

- [Motivation, Governance, and the Viability of Hybrid Forms in Open Source Software Development](https://dl.acm.org/doi/abs/10.1287/mnsc.1060.0553)

- [Socialization in Open Source Software Projects: A Growth Mixture Modeling Approach](https://www.statmodel.com/download/2011-ORM-14-1.pdf)
