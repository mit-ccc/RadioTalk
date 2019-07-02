# RadioTalk
The RadioTalk dataset of talk radio transcripts

This repository contains supplementary information for the paper "RadioTalk: a large-scale corpus of talk radio transcripts," forthcoming at Interspeech 2019.

# Data location and access
The corpus as documented in the paper is available in the S3 bucket `radio-talk` at `s3://radio-talk/v1/`. The entire corpus is available as one file of about 9.3 GB at `s3://radio-talk/v1/radiotalk.json.gz`, and there's also a version with one file per month under `s3://radio-talk/v1/monthly/`. Any future versions will be released under other `vX` prefixes for suitable values of `X`.

# Initial station sample
The initial set of 50 radio stations for ingestion was chosen from the universe of all 1,912 talk radio stations as follows. First, we excluded certain stations from consideration:
    * stations without an online stream of their broadcasts,
    * stations in Alaska or Hawaii, and
    * the recently licensed category of "low-power FM stations".

Next, we took a random sample of 50 stations from the remaining 1,842, stratifying by four variables:
    * Radio band (AM or FM),
    * Four-way Census region (Midwest, Northeast, South, West),
    * Whether there were at least 10 stations listed in that station's city, as a proxy for population density, and
    * Whether the station was in a battleground state for the 2016 presidential election. Battleground states for our purposes were NV, AZ, CO, IA, WI, MI, OH, PA, VA, NC, FL, NH.

This sample was intended to be nationally representative and to permit weighting summary estimates back to the population of radio stations along these four variables. Note that some (8 as of June 2019) of the selected stations have either ceased airing a talk format or no longer offer an online stream of their broadcasts, and are thus not included in the later parts of the corpus.

The list of these initial stations is included in the file `talk_radio_sample.csv`.

