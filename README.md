![Repo-GitHub](https://img.shields.io/badge/dynamic/xml?color=blue&label=IPM%20version&version&prefix=v&query=%2F%2FVersion&url=https%3A%2F%2Fraw.githubusercontent.com%2Fbdeboe%2Fisc-iknow-utils%2Fmain%2Fmodule.xml)


# iKnow Utilities

This repository bundles a few reusable IRIS NLP utilities for common scenarios. You can easily install the bundle using [IPM](https://github.com/intersystems/ipm):

```ObjectScript
USER> zpm

zpm:USER> install iknow-utils
```

Alternatively, use `$SYSTEM.OBJ.LoadDir()` passing in the path where you downloaded this repository.

All classes are in a `bdb.iKnow` package to avoid conflicting with existing namespace contents and provided without any warranty. Feel free to report issues through the [GitHub repo](https://github.com/bdeboe/isc-iknow-utils/issues).

For a sample code bundle accessing the iKnow APIs directly from your Python code, please check this related [Github repo](https://github.com/bdeboe/isc-iknow-irispy)


## Converters

`bdb.iKnow.Converters.SemiStructured` is a simple `%iKnow.Source.Converter` implementation that tries to prevent common key-value style elements from messing up iKnow output. For example:

```text
MRN: 12345
HISTORY OF PRESENT ILLNESS: Patient reported wobbly toes in an earlier encounter, for which close
followup was recommended. Wobbling subsided after 3w of intense inactivity.
  
BP: 123/mmHg
LS: 456
KV: 78xyz
TREATMENT: Patient will continue to watch Netflix 14h pd.
```

iKnow expects natural language text and does not consider single line breaks to mean the end of a real sentence. Therefore, when passing the above text verbatim to the iKnow engine, you'll get those structured `BP: 123/mmHg`, `LS: 456`, etc elements concatenated to one another as if it were a sentence.

The `bdb.iKnow.Converters.SemiStructured` class can be used in a `<converter ... />` tag in your domain definition to sneak a double line break in right before such keys so they are not appended to the previous line. Note that the implementation is somewhat cautious in only adding a line break _before_ the keys and only getting triggered by all-uppercase keys to limit false positives that might mess up genuine use of colons in natural language sentences. This is a sample after all and easy to adapt to your needs.

As a bonus, this Converter can also pick up key values as metadata, by supplying a list of keys as the converter parameter, as shown in the example below:

```ObjectScript
USER> set converted = ##class(bdb.iKnow.Converters.SemiStructured).TestFull(str,$lb("MRN,KV,LS"),.meta)

USER> zwrite meta
meta("KV")="78xyz"
meta("LS")=456
meta("MRN")=12345
```
## DOCKER Support
### Prerequisites   
Make sure you have [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker desktop](https://www.docker.com/products/docker-desktop) installed.    
### Installation    
Clone/git pull the repo into any local directory
```
$ git clone https://github.com/bdeboe/isc-iknow-utils.git 
```
Open the terminal in this directory and run:
```
$ docker-compose up -d
```
Test from docker console
```
$ docker-compose exec iris iris session iris
USER>
```
or using **WebTerminal**
```
http://localhost:42773/terminal/
```
