# sos-dataexchanger (stage: planning)

Exchanging data between languages is a common problem with multi-language data analysis. [Script of Scripts (SoS)](https://vatlab.github.io/sos-docs/) tries to address this problem by providing a [Polyglot Notebook](https://github.com/vatlab/sos-notebook) in which **variables can be exchanged among live kernels with the assistance of the SoS kernel**.

SoS also provides a [SoS Workflow Engine](https://github.com/vatlab/SoS) that essentially executes scripts in different languages with a DAG. However, like all workflow systems, **data exchange among steps can only be done through files**.

## Need of a common data exchange mechanism

## Rough design of a common data exchange mechanism

1. A data store is defined to either
  * Store data in different formats locally
  * Interface existing data through predefined API
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

 
