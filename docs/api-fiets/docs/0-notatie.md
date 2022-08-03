## Notatie

Datatypes zijn de JSON-datatypes ([[ECMA-404]]) en ook de JSON-notatiewijze wordt gebruikt.

Typenotaties van [TypeScript] worden op plekken gebruikt. Daarbij geldt:

- waar `[]` achter een type staat, wordt een lijst van dat type objecten bedoeld.
- waar `<T>` staat, wordt bedoeld dat |T| een generieke typevariabele is.

  Bijvoorbeeld bij `ResultWrapper<T>` en diens eigenschap `result: T[]`.
  Met `ResultWrapper<Survey>` wordt dan aangegeven dat het type van de eigenschap `result` in dat geval `Survey[]` is.

- waar `Partial<T>` staat, wordt bedoeld dat alle eigenschappen van |T| optioneel zijn.

Waar de waarde een `string` is, uit een beperkte enumeratielijst, worden meerdere waardes aangegeven door ze met spaties te scheiden.

Waar in een URL een `{var}` voorkomt, staat dat deel voor een variabele die ingevuld moet worden.

<figure>
<figcaption>Gebruikte namespaces in dit document</figcaption>

| Prefix    | Namespace                                           |
| --------- | --------------------------------------------------- |
| `schema:` | `http://schema.org/`                                |
| `mv:`     | `http://schema.mobivoc.org/#`                       |
| `vp:`     | `https://data.velopark.be/openvelopark/vocabulary#` |
| `vpt:`    | `https://data.velopark.be/openvelopark/terms#`      |
| `time:`   | `http://www.w3.org/2006/time#`                      |
| `fp:`     | `https://data.crow.nl/dsfp/def#`                    |

</figure>

[typescript]: https://www.typescriptlang.org/
