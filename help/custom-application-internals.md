---
title: Comprendre le fonctionnement d’une application personnalisée.
description: Fonctionnement interne [!DNL Asset Compute Service] d’une application personnalisée pour vous aider à comprendre son fonctionnement.
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '774'
ht-degree: 0%

---


# Internes d’une application personnalisée {#how-custom-application-works}

Utilisez l’illustration suivante pour comprendre le processus de bout en bout lorsqu’un fichier numérique est traité à l’aide d’une application personnalisée par un client.

![Processus des applications personnalisées](assets/customworker.png)

*Figure : Procédure de traitement d’un actif à l’aide de [!DNL Asset Compute Service].*

## Enregistrement {#registration}

Le client doit appeler [`/register`](api.md#register) une fois avant la première demande pour [`/process`](api.md#process-request) configurer et récupérer l&#39;URL de journal pour la réception de Événements d&#39;E/S d&#39;Adobe pour le calcul des ressources d&#39;Adobe.

```sh
curl -X POST \
  https://asset-compute.adobe.io/register \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY"
```

La bibliothèque [`@adobe/asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) JavaScript peut être utilisée dans les applications NodeJS pour gérer toutes les étapes nécessaires, de l’enregistrement au traitement en passant par la gestion asynchrone des événements. Pour plus d’informations sur les en-têtes requis, voir [Authentification et autorisation](api.md).

## Traitement en cours {#processing}

Le client envoie une demande de [traitement](api.md#process-request) .

```sh
curl -X POST \
  https://asset-compute.adobe.io/process \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY" \
  -d "<RENDITION_JSON>
```

Le client est chargé de mettre correctement en forme les rendus avec des URL pré-signées. La bibliothèque [`@adobe/node-cloud-blobstore-wrapper`](https://github.com/adobe/node-cloud-blobstore-wrapper#presigned-urls) JavaScript peut être utilisée dans les applications NodeJS pour pré-signer les URL. Actuellement, la bibliothèque ne prend en charge que l&#39;Enregistrement Azure Blob et les Conteneurs AWS S3.

La demande de traitement renvoie une requête `requestId` qui peut être utilisée pour les Événements d&#39;E/S d&#39;Adobe d&#39;interrogation.

Vous trouverez ci-dessous un exemple de demande de traitement d’application personnalisée.

```json
{
    "source": "https://www.adobe.com/some-source-file.jpg",
    "renditions" : [
        {
            "worker": "https://my-project-namespace.adobeioruntime.net/api/v1/web/my-namespace-version/my-worker",
            "name": "rendition1.jpg",
            "target": "https://some-presigned-put-url-for-rendition1.jpg",
        }
    ],
    "userData": {
        "my-asset-id": "1234567890"
    }
}
```

L’ [!DNL Asset Compute Service] utilisateur envoie les demandes de rendu d’application personnalisée à l’application personnalisée. Il utilise un POST HTTP pour l’URL de l’application fournie, qui est l’URL d’action Web sécurisée du projet Firefly. Toutes les requêtes utilisent le protocole HTTPS pour optimiser la sécurité des données.

Le SDK [](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) Asset Compute utilisé par une application personnalisée traite la demande de POST HTTP. Il gère également le téléchargement de la source, le transfert de rendus, l&#39;envoi de événements d&#39;E/S et la gestion des erreurs.

<!-- TBD: Add the application diagram. -->

### Application code {#application-code}

Le code personnalisé doit uniquement fournir un rappel qui prend le fichier source disponible localement (`source.path`). Il `rendition.path` s’agit de l’emplacement où placer le résultat final d’une demande de traitement de ressources. L’application personnalisée utilise le rappel pour transformer les fichiers source disponibles localement en fichier de rendu en utilisant le nom transmis dans (`rendition.path`). Une application personnalisée doit écrire pour `rendition.path` créer un rendu :

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

// worker() is the entry point in the SDK "framework".
// The asynchronous function defined is the rendition callback.
exports.main = worker(async (source, rendition) => {

    // Tip: custom worker parameters are available in rendition.instructions.
    console.log(rendition.instructions.name); // should print out `rendition.jpg`.

    // Simplest example: copy the source file to the rendition file destination so as to transfer the asset as is without processing.
    await fs.copyFile(source.path, rendition.path);
});
```

### Téléchargement de fichiers source {#download-source}

Une application personnalisée traite uniquement des fichiers locaux. Le téléchargement du fichier source est géré par le SDK [](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk)Asset Compute.

### Création de rendu {#rendition-creation}

Le SDK appelle une fonction [de rappel de](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) rendu asynchrone pour chaque rendu.

La fonction de rappel a accès aux objets [source](https://github.com/adobe/asset-compute-sdk#source) et de [rendu](https://github.com/adobe/asset-compute-sdk#rendition) . Le fichier `source.path` existe déjà et est le chemin d&#39;accès à la copie locale du fichier source. Il `rendition.path` s’agit du chemin d’accès où le rendu traité doit être stocké. A moins que l&#39;indicateur [](https://github.com/adobe/asset-compute-sdk#worker-options-optional) disableSourceDownload ne soit défini, l&#39;application doit utiliser exactement le `rendition.path`. Sinon, le SDK ne peut pas localiser ni identifier le fichier de rendu et échoue.

La simplification excessive de l&#39;exemple est faite pour illustrer et se concentrer sur l&#39;anatomie d&#39;une application personnalisée. L’application copie simplement le fichier source vers la destination du rendu.

Pour plus d’informations sur les paramètres de rappel de rendu, voir API [SDK](https://github.com/adobe/asset-compute-sdk#api-details)Asset Compute.

### Téléchargement de rendus {#upload-rendition}

Une fois chaque rendu créé et stocké dans un fichier avec le chemin d’accès fourni par `rendition.path`, le SDK [](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) Asset Compute télécharge chaque rendu vers un enregistrement cloud (AWS ou Azure). Une application personnalisée obtient plusieurs rendus en même temps si, et uniquement si, la requête entrante comporte plusieurs rendus pointant vers la même URL d’application. Le téléchargement vers l’enregistrement cloud est effectué après chaque rendu et avant l’exécution du rappel pour le rendu suivant.

Le comportement `batchWorker()` est différent, car il traite en fait tous les rendus et seulement après avoir tous été traités les télécharge.

## Événements d&#39;E/S Adobe {#aio-events}

Le SDK envoie des Événements d’E/S d’Adobe pour chaque rendu. Ces événements sont de type `rendition_created` ou `rendition_failed` selon le résultat. Pour plus d’informations sur les événements, voir événements [asynchrones](api.md#asynchronous-events) Asset Compute.

## Recevoir des Événements d&#39;E/S Adobe {#receive-aio-events}

Le client sonde le Journal [des Événements](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#/Journaling) d&#39;E/SAdobe en fonction de sa logique de consommation. L’URL de journal initial est celle fournie dans la réponse de l’ `/register` API. Les événements peuvent être identifiés à l’aide du `requestId` qui est présent dans les événements et qui est le même que celui renvoyé dans `/process`. Chaque rendu comporte un événement distinct qui est envoyé dès que le rendu a été téléchargé (ou a échoué). Une fois qu’il reçoit un événement correspondant, le client peut afficher ou gérer les rendus résultants.

La bibliothèque JavaScript [`asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) facilite l’interrogation du journal en utilisant la `waitActivation()` méthode pour obtenir tous les événements.

```javascript
const events = await assetCompute.waitActivation(requestId);
await Promise.all(events.map(event => {
    if (event.type === "rendition_created") {
        // get rendition from cloud storage location
    }
    else if (event.type === "rendition_failed") {
        // failed to process
    }
    else {
        // other event types
        // (could be added in the future)
    }
}));
```

Pour plus d&#39;informations sur la façon d&#39;obtenir des événements de journal, consultez l&#39;API [des Événements d&#39;E/S](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#!adobedocs/adobeio-events/master/events-api-reference.yaml)d&#39;Adobe.

<!-- TBD:
* Illustration of the controls/data flow.
* Basic overview, in text and not code, of how an application works.
-->
