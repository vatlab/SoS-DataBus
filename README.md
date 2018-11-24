# sos-dataexchanger (stage: planning)

Exchanging data between languages is a common problem with multi-language data analysis. [Script of Scripts (SoS)](https://vatlab.github.io/sos-docs/) tries to address this problem by providing a [Polyglot Notebook](https://github.com/vatlab/sos-notebook) in which **variables can be exchanged among live kernels with the assistance of the SoS kernel**. At the user level, all it takes it to execute a `%get var --from kernel_B` magic from `kernel_A`.

SoS also provides a [SoS Workflow Engine](https://github.com/vatlab/SoS) that essentially executes scripts in different languages with a DAG. However, like all workflow systems, **data exchange among steps can only be done through files**.

## Need of a common data exchange mechanism

Data exchange through files are convenient but can have the following problems:

1. File contents are untyped and it is up to the consumer of the files to decide how to interpret them.
2. Data exchange through files can be inefficient due to file system delays, especially when there are a large amount of small files.
3. Sharing of files can be problematic if multiple processes need to access the same files.
4. Persistence of files is a two-blade sword because temporary files, if not properly managed, can lead to all sorts of problems.

It would be useful to devise a mechanism to exchange typed information between scripts written in different languges. This is
a huge topic that has led to the development of numerous projects with different emphases.

## Rough design of a common data exchange mechanism

The overall design looks like a [`redis` server](https://redis.io/) with supports for more data types. Basically,

1. A data store is defined to either
  * Store data in different formats locally, or
  * Interface existing data through predefined API, or another datastore
2. A protocol is defined to save and request meta data, complete data, and/or pieces of data through network (zmq)
3. Each language defines its own library to talk to the data store.

In practice, users would

1. Start a data store daemon, which could be done automatically by SoS, or manually outside of SoS.
2. During interactive data analysis or batch data processing (workflow), save and load the data to the data store using a uniform manner, for example,
```
(Python)
import datastore as ds
import pandas as pd

ds = ds.connect(...)
dat = pd.DataFrame(...)
ds.push('dat', dat)
```

and

```
(R)
library(datastore)
dat = ds_pull('dat')
```

More details will follow as they mature.
