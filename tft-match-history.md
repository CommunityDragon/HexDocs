---
title: "Match History"
authors: "@SKarajic"
tags: ""
markers: "tft-marker"
---
# Match History
## Best Practices
To best utilize TFT match history there are two things to keep in mind; match history is still evolving and fresh data is cheaper.

### Response Body Structure
A basic version of TFT match history was released in the client in patch 9.19\. If you look up matches played during this patch they'll return `data_version: "1"` which is used through patch 9.20\. In patch 9.19, the only fields for a participant that are guranteed to be accurate are `last_round`, `level`, `placement`, and `puuid`. In patch 9.20, items were added to the response body but were not guranteed to be accurate. As of patch 9.21 all the fields in the response should be accurate. As of patch 9.22, a new field called `character_id` was added to the UnitDto and the data version was bumped to `data_version: "2"`.

### Fresh Data
The match history service itself is optimized for fresh data. The newer the matches, the cheaper the cost to look them up. With Set 2 launching on November 4th, 2019 and the data inconsistencies prior to patch 9.20, we’re discouraging developers from backfilling data for Set 1.
