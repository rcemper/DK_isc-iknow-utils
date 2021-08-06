# iKnow Utilities

This repository bundles a few reusable IRIS NLP utilities for common scenarios.

## Converters

`iKnow.Converters.SemiStructured` is a simple `%iKnow.Source.Converter` implementation that tries to prevent common key-value style elements from messing up iKnow output. For example:

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

The `iKnow.Converters.SemiStructured` class can be used in a `<converter >` tag in your domain definition to sneak a double line break in right before such keys so they are not appended to the previous line. Note that the implementation is somewhat cautious in only adding a line break _before_ the keys and only getting triggered by all-uppercase keys to limit false positives that might mess up genuine use of colons in natural language sentences. This is a sample after all and easy to adapt to your needs.

As a bonus, this Converter can also pick up key values as metadata, by supplying a list of keys as the converter parameter, as shown in the example below:

```ObjectScript
USER> set converted = ##class(iKnow.Converters.SemiStructured).TestFull(str,$lb("MRN","KV","LS"),.meta)

USER> zwrite meta
meta("KV")="78xyz"
meta("LS")=456
meta("MRN")=12345
```