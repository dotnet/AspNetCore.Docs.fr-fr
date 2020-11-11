* `-F|--no-formatting`

  Indicateur dont la présence provoque la suppression de la mise en forme de la réponse HTTP.

* `-h|--header`

  Définit un en-tête de requête HTTP. Les deux formats de fichier suivants sont pris en charge :

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Spécifie un fichier dans lequel le corps de la réponse HTTP doit être écrit. Par exemple, `--response:body "C:\response.json"`. Le fichier est créé s’il n’existe pas.

* `--response:headers`

  Spécifie un fichier dans lequel les en-têtes de la réponse HTTP doivent être écrits. Par exemple, `--response:headers "C:\response.txt"`. Le fichier est créé s’il n’existe pas.

* `-s|--streaming`

  Indicateur dont la présence provoque l’activation du streaming de la réponse HTTP.
