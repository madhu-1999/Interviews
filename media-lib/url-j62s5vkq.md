---
mx-uid: j62s5vkqls35nlmcun5l2mnt
title: The Parquet Format and Performance Optimization Opportunities Boudewijn Braams (Databricks)
tags:
  - "#de"
video: https://www.youtube.com/watch?v=1j8SdS7s_NY
description: |-
  The Parquet format is one of the most widely used columnar storage formats in the Spark ecosystem. Given that I/O is expensive and that the storage layer is the entry point for any query execution, understanding the intricacies of your storage format is important for optimizing your workloads. As an introduction, we will provide context around the format, covering the basics of structured data formats and the underlying physical data storage model alternatives (row-wise, columnar and hybrid). Given this context, we will dive deeper into specifics of the Parquet format: representation on disk, physical data organization (row-groups, column-chunks and pages) and encoding schemes. Now equipped with sufficient background knowledge, we will discuss several performance optimization opportunities with respect to the format: dictionary encoding, page compression, predicate pushdown (min/max skipping), dictionary filtering and partitioning schemes. We will learn how to combat the evil that is 'many small files', and will discuss the open-source Delta Lake format in relation to this and Parquet in general. This talk serves both as an approachable refresher on columnar storage as well as a guide on how to leverage the Parquet format for speeding up analytical workloads in Spark using tangible tips and tricks.

  About: Databricks provides a unified data analytics platform, powered by Apache Spark™, that accelerates innovation by unifying data science, engineering and business.
  Read more here: https://databricks.com/product/unified-data-analytics-platform

  Connect with us:
  Website: https://databricks.com
  Facebook: https://www.facebook.com/databricksinc
  Twitter: https://twitter.com/databricks
  LinkedIn: https://www.linkedin.com/company/databricks
  Instagram: https://www.instagram.com/databricksinc/ 

  Get insights on how to launch a successful lakehouse architecture in Rise of the Data Lakehouse by Bill Inmon, the father of the data warehouse. Download the ebook: https://dbricks.co/3IMxugQ
duration: 2446
creator: Databricks
creator_url: https://www.youtube.com/@Databricks
published_at: 2019-10-21
view_count: 186010
like_count: 3285
cover: "[[mx-cover-youtube_1j8SdS7s_NY.jpg]]"
aspect_ratio: 16 / 9
---
