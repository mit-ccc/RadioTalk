# RadioTalk
This repository contains supplementary information for the paper ["RadioTalk: a large-scale corpus of talk radio transcripts"](https://arxiv.org/abs/1907.07073), forthcoming at Interspeech 2019.

# Data location and access
The corpus as documented in the paper is available in the Amazon AWS S3 bucket `radio-talk` at `s3://radio-talk/v1.0/`  (Browse on [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets/radio-talk?region=us-east-1&tab=objects))

The entire corpus is available as one file of about 9.3 GB at `s3://radio-talk/v1.0/radiotalk.json.gz`, and there's also a version with one file per month under `s3://radio-talk/v1.0/monthly/`. [Pre-trained word embeddings](#pre-trained-word-embeddings) are also available. Any future versions will be released under other `vX.Y` prefixes for suitable values of `X` and `Y`.

# Data description
The RadioTalk corpus is in JSONL format, with one json document per line. Each line represents one "snippet" of audio, may contain multiple sentences, and is represented as a dictionary object with the following keys:
* `content`: The transcribed speech from the snippet.
* `callsign`: The call letters of the station the snippet aired on.
* `city`: The city the station is based in, as in FCCC filings.
* `state`: The state the station is based in, as in FCCC filings.
* `show_name`: The name of the show containing this snippet.
* `signature`: The initial 8 bytes of an MD5 hash of the `content` field, after lowercasing and removing English stopwords (specifically the NLTK stopword list), intended to help with deduplication.
* `studio_or_telephone`: A flag for whether the underlying audio came from a telephone or studio audio equipment. (The most useful feature in distinguishing these is the [narrow frequency range](https://en.wikipedia.org/wiki/Plain_old_telephone_service#Characteristics) of telephone audio.)
* `guessed_gender`: The imputed speaker gender.
* `segment_start_time`: The Unix timestamp of the beginning of the underlying audio.
* `segment_end_time`: The Unix timestamp of the end of the underlying audio.
* `speaker_id`: A diarization ID for the person speaking in the audio snippet.
* `audio_chunk_id`: An ID for the audio chunk this snippet came from (each chunk may be split into multiple snippets).

An example snippet from the corpus (originally on one line but pretty-printed here for readability):
```
{
    "content": "This would be used for housing programs and you talked a little bit about how the attorney",
    "callsign": "KABC",
    "city": "Los Angeles",
    "state": "CA",
    "show_name": "The Drive Home With Jillian Barberie & John Phillips",
    "signature": "afd7d2ee",
    "studio_or_telephone": "T",
    "guessed_gender": "F",
    "segment_start_time": 1540945402.6,
    "segment_end_time": 1540945408.6,
    "speaker_id": "S0",
    "audio_chunk_id": "2018-10-31/KABC/00_20_28/16"
}
```

# Pre-trained word embeddings
A word embedding model trained on the RadioTalk data, in the format produced by [gensim](https://radimrehurek.com/gensim/models/word2vec.html), is also available in the bucket, at `s3://radio-talk/v1.0/word2vec/`. The embeddings are 300-dimensional and were trained with the skip-gram with negative sampling variant of Word2Vec (see [Mikolov et al 2013](https://arxiv.org/abs/1301.3781)). See also our [evaluation](word2vec/word2vec-eval.ipynb) of these embeddings on some standard analogy and similarity tasks.

## Word embedding details
Besides doing the usual preprocessing -- conversion to lowercase, removing punctuation, etc -- we also concatenated common phrases into single tokens with words separated by underscores  before training the embeddings. (Specifically, the list of phrases to combined included the titles of English Wikipedia articles, a list of phrases [detected](https://radimrehurek.com/gensim/models/phrases.html) from the corpus, and the names of certain political figures.) Counting these combined collocations as single terms, the model vocabulary contains 53,968 terms.

For reproducibility, the gensim model object was initialized with the following non-default parameters:
* size = 300
* sg = 1
* hs = 0
* negative = 10
* window = 8
* min\_count = 25
* workers = 4

# Kaldi model
As discussed in the paper, to transcribe radio speech we started with the [JHU ASpIRE speech-to-text model](https://kaldi-asr.org/models/m1) and
replaced its language model with one trained on the transcripts of various radio programs.  Our final Kaldi model files
(which can be used as drop-in replacements for the outputs of the recipe linked above) can be downloaded from
`s3://radio-talk/v1.0/models/radiotalk_kaldi_model_20191106.tgz`  (Note:  1.9 GB compressed, 4.8 GB uncompressed)

# Initial station sample
The initial set of 50 radio stations for ingestion was chosen from the universe of all 1,912 talk radio stations as follows. First, we excluded certain stations from consideration:
* stations without an online stream of their broadcasts,
* stations in Alaska or Hawaii, and
* the recently licensed category of "low-power FM stations".

Next, we took a random sample of 50 stations from the remaining 1,842, stratifying by four variables:
* Radio band (AM or FM),
* Four-way Census region (Midwest, Northeast, South, West) based on the containing state,
* Whether there were at least 10 stations listed in that station's city, as a proxy for population density, and
* Whether the station was in a battleground state for the 2016 presidential election. Battleground states for our purposes were NV, AZ, CO, IA, WI, MI, OH, PA, VA, NC, FL, NH.

This sample was intended to be nationally representative and to permit weighting summary estimates back to the population of radio stations along these four variables. Note that some (8 as of June 2019) of the selected stations have either ceased airing a talk format or no longer offer an online stream of their broadcasts, and are thus not included in the later parts of the corpus.

The list of these initial stations is included in the file `talk_radio_sample.csv`.

# Demo
This interface lets you listen to a sample of radio clips restricted to the topic and U.S. state of your choosing:   https://radio.cortico.ai/

