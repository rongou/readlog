# Lance: Efficient Random Access in Columnar Storage through Adaptive Structural EncodingsGR00T N1

* Authors: LanceDB
* Link: https://arxiv.org/abs/2504.15247
* Date: 2025-04-21

## Notes

* Sequential and random access, NVMe caching
* Current columnar formats bad for search-oriented workflows: full-text search, semantic search, RAG
* S3 vs NVMe: optimized for sequential vs random reads
* Random access requires 1 IOPS per column, amplified by compression and nested structure encodings
* Structural encoding (shredding): converts a potentially nested array into one or more leaf nodes, which each contain
  one or more primitive arrays and/or buffers.
* Compressive encoding: takes each of these leaf arrays or buffers and compresses them into the smallest possible
  representation
* Lance
    * full zip encoding for large data types: the repetition levels, definition levels, and all buffers that make up the
      leaf column are zipped into a single buffer of values
    * mini-block encoding for small data types: divide an array into a series of small chunks, then compressed opaquely
      and chunked
* Lance allows for a smaller search cache, performs better than Parquet for both random access and full scans

## Ideas

* Store world-model training data in Lance format on S3, use NVMe caching for random access.
