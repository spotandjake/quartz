---
publish: "true"
---

Api to check domains for trustworthiness.
```
/get/trust
Request
{
  "site": "domain",
}
Response
{
  "trustState": 'Trustful' | 'Untrustful' | 'Unrated',
  "trustScore": "5", // if unrated this is null?, we store it as an integral percentage??? or point idk yet
  "reasons": {} // probably an object of reasining
}
```
And then basically when a user fetches a website you kick off a db check if it exists you return it (and maybe re-index it if the information is old), and then you return the information, if it doesn't exist you kick off an index (if it doesn't resolve to a trustworthy state) in some time (500ms) lets say then we respond withnothing and let the index finish to add it to the db.

On downtime (i.e we are not serving any requests or anything) you lazily scan the db and kick off requests for outdated sites.

Trustworthiness could probably be implemented a few ways (looking at googles pagerank would probs be a good start), and then combinig that with a set index maybe? (thought the set index should be a stop gap until enough sites are indexed), (Might also make sense to develop an index for publishers and stuff like that and relate them bidirectionally.