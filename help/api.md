---
title: '[!DNL Asset Compute Service] API HTTP.'
description: '[!DNL Asset Compute Service] API HTTP pour créer des applications personnalisées.'
translation-type: tm+mt
source-git-commit: 18e97e544014933e9910a12bc40246daa445bf4f
workflow-type: tm+mt
source-wordcount: '2931'
ht-degree: 3%

---


# [!DNL Asset Compute Service] API HTTP {#asset-compute-http-api}

L’utilisation de l’API est limitée à des fins de développement. L’API est fournie comme contexte lors du développement d’applications personnalisées. [!DNL Adobe Experience Manager] en tant que Cloud Service utilise l’API pour transmettre les informations de traitement à une application personnalisée. Pour plus d’informations, voir [Utilisation des microservices de ressources et Profils](https://docs.adobe.com/content/help/fr-FR/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)de traitement.

>[!NOTE]
>
>[!DNL Asset Compute Service] est disponible uniquement pour une utilisation avec [!DNL Experience Manager] comme Cloud Service.

Tout client de l’API [!DNL Asset Compute Service] HTTP doit suivre ce flux de haut niveau :

1. Un client est mis en service en tant que [!DNL Adobe Developer Console] projet dans une organisation IMS. Chaque client (système ou environnement) distinct a besoin de son propre projet distinct pour séparer le flux de données du événement.

1. Un client génère un jeton d&#39;accès pour le compte technique à l’aide de l’authentification [](https://www.adobe.io/authentication/auth-methods.html)JWT (Service Account).

1. Un client n’appelle [`/register`](#register) qu’une seule fois pour récupérer l’URL du journal.

1. Un client appelle [`/process`](#process-request) pour chaque ressource pour laquelle il souhaite générer des rendus. L&#39;appel est asynchrone.

1. Un client effectue régulièrement des sondages sur le journal de [réception des événements](#asynchronous-events). Elle reçoit des événements pour chaque rendu demandé lorsque le rendu est traité (`rendition_created` type d&#39;événement) ou en cas d’erreur (`rendition_failed` type d&#39;événement).

Le module [@adobe/asset-compute-client](https://github.com/adobe/asset-compute-client) facilite l’utilisation de l’API dans le code Node.js.

## Authentification et autorisation {#authentication-and-authorization}

Toutes les API requièrent une authentification par jeton d&#39;accès. Les requêtes doivent définir les en-têtes suivants :

1. `Authorization` en-tête avec jeton porteur, qui est le jeton de compte technique, reçu via un échange [](https://www.adobe.io/authentication/auth-methods.html) JWT depuis le projet Adobe Developer Console. Les [étendues](#scopes) sont décrites ci-dessous.

<!-- TBD: Change the existing URL to a new path when a new path for docs is available. The current path contains master word that is not an inclusive term. Logged ticket in AIO's GitHub repo to get a new URL.
-->

1. `x-gw-ims-org-id` avec l’ID d’organisation IMS.

1. `x-api-key` avec l’ID client du [!DNL Adobe Developers Console] projet.

### Portées {#scopes}

Vérifiez les étendues suivantes pour le jeton d&#39;accès :

* `openid`
* `AdobeID`
* `asset_compute`
* `read_organizations`
* `event_receiver`
* `event_receiver_api`
* `adobeio_api`
* `additional_info.roles`
* `additional_info.projectedProductContext`

Pour cela, le [!DNL Adobe Developer Console] projet doit être abonné à `Asset Compute`des services `I/O Events``I/O Management API` et à des services. La ventilation des étendues individuelles est la suivante :

* De base
   * scopes: `openid,AdobeID`

* Calcul des ressources
   * metascope: `asset_compute_meta`
   * scopes: `asset_compute,read_organizations`

* Événements d&#39;E/S Adobe
   * metascope: `event_receiver_api`
   * scopes: `event_receiver,event_receiver_api`

* API de gestion des E/S Adobe
   * metascope: `ent_adobeio_sdk`
   * scopes: `adobeio_api,additional_info.roles,additional_info.projectedProductContext`

## Enregistrement {#register}

Chaque client du [!DNL Asset Compute service] projet - un projet unique [!DNL Adobe Developer Console] abonné au service - doit s&#39; [enregistrer](#register-request) avant de traiter les demandes. L’étape d’enregistrement renvoie le journal de événement unique requis pour récupérer les événements asynchrones du traitement du rendu.

À la fin de son cycle de vie, un client peut [annuler son enregistrement](#unregister-request).

### Enregistrer la demande {#register-request}

Cet appel d’API configure un [!DNL Asset Compute] client et fournit l’URL du journal de événement. Il s’agit d’une opération puissante qui ne doit être appelée qu’une seule fois pour chaque client. Il peut être de nouveau appelé pour récupérer l’URL du journal.

| Paramètre | Valeur |
|--------------------------|------------------------------------------------------|
| Méthode | `POST` |
| Chemin    | `/register` |
| Header `Authorization` | Tous les en-têtes [liés à](#authentication-and-authorization)l’autorisation. |
| Header `x-request-id` | Facultatif, il est possible de définir par les clients un identifiant unique de bout en bout des demandes de traitement sur l’ensemble des systèmes. |
| Corps de requête | Doit être vide. |

### Enregistrer la réponse {#register-response}

| Paramètre | Valeur |
|-----------------------|------------------------------------------------------|
| Type MIME | `application/json` |
| Header `X-Request-Id` | Identique à l’en-tête de `X-Request-Id` requête ou à celui généré de manière unique. Servez-vous pour identifier les demandes entre les systèmes et/ou les demandes d&#39;assistance. |
| Organe de réponse | Objet JSON contenant `journal`, `ok` et/ou `requestId` des champs. |

Les codes d’état HTTP sont les suivants :

* **200 Réussite**: Lorsque la demande est acceptée. Il contient l’ `journal` URL qui est averti de tout résultat du traitement asynchrone déclenché par `/process` (comme type de événement `rendition_created` en cas de réussite ou `rendition_failed` en cas d’échec).

   ```json
   {
       "ok": true,
       "journal": "https://api.adobe.io/events/organizations/xxxxx/integrations/xxxx/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
       "requestId": "1234567890"
   }
   ```

* **401 Non autorisé**: survient lorsque la requête ne dispose pas d’une [authentification](#authentication-and-authorization)valide. Un exemple peut être un jeton d&#39;accès non valide ou une clé d&#39;API non valide.

* **403 Interdit**: survient lorsque la demande ne dispose pas d’une [autorisation](#authentication-and-authorization)valide. Un exemple peut être un jeton d&#39;accès valide, mais le projet Adobe Developer Console (compte technique) n&#39;est pas abonné à tous les services requis.

* **429 Trop de requêtes**: survient lorsque le système est surchargé par ce client ou autre. Les clients doivent réessayer avec un retour sur investissement [](https://en.wikipedia.org/wiki/Exponential_backoff)exponentiel. Le corps est vide.
* **Erreur** 4xx : En cas d’erreur du client et d’échec de l’enregistrement. Généralement, une réponse JSON telle que celle-ci est renvoyée, même si elle n’est pas garantie pour toutes les erreurs :

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **Erreur** 5xx : survient lorsqu’une autre erreur côté serveur s’est produite et que l’enregistrement a échoué. Généralement, une réponse JSON telle que celle-ci est renvoyée, même si elle n’est pas garantie pour toutes les erreurs :

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

### Annuler l&#39;enregistrement de la demande {#unregister-request}

Cet appel d&#39;API annule l&#39;enregistrement d&#39;un [!DNL Asset Compute] client. Après cela, il n&#39;est plus possible d&#39;appeler `/process`. L’utilisation de l’appel d’API pour un client non enregistré ou un client à enregistrer renvoie une `404` erreur.

| Paramètre | Valeur |
|--------------------------|------------------------------------------------------|
| Méthode | `POST` |
| Chemin    | `/unregister` |
| Header `Authorization` | Tous les en-têtes [liés à](#authentication-and-authorization)l’autorisation. |
| Header `x-request-id` | Facultatif, il est possible de définir par les clients un identifiant unique de bout en bout des demandes de traitement sur l’ensemble des systèmes. |
| Corps de requête | Vide. |

### Annuler l’enregistrement de la réponse {#unregister-response}

| Paramètre | Valeur |
|-----------------------|------------------------------------------------------|
| Type MIME | `application/json` |
| Header `X-Request-Id` | Identique à l’en-tête de `X-Request-Id` requête ou à celui généré de manière unique. Servez-vous pour identifier les demandes entre les systèmes et/ou les demandes d&#39;assistance. |
| Organe de réponse | Objet JSON contenant `ok` et `requestId` des champs. |

Les codes d’état sont les suivants :

* **200 Réussite**: survient lorsque l’enregistrement et le journal sont trouvés et supprimés.

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **401 Non autorisé**: survient lorsque la requête ne dispose pas d’une [authentification](#authentication-and-authorization)valide. Un exemple peut être un jeton d&#39;accès non valide ou une clé d&#39;API non valide.

* **403 Interdit**: survient lorsque la demande ne dispose pas d’une [autorisation](#authentication-and-authorization)valide. Un exemple peut être un jeton d&#39;accès valide, mais le projet Adobe Developer Console (compte technique) n&#39;est pas abonné à tous les services requis.

* **404 introuvable**: survient lorsqu’il n’existe aucune inscription en cours pour les informations d’identification données.

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **429 Trop de requêtes**: survient lorsque le système est surchargé. Les clients doivent réessayer avec un retour sur investissement [](https://en.wikipedia.org/wiki/Exponential_backoff)exponentiel. Le corps est vide.

* **Erreur** 4xx : survient lorsqu’une autre erreur client s’est produite et que l’annulation de l’enregistrement a échoué. Généralement, une réponse JSON telle que celle-ci est renvoyée, même si elle n’est pas garantie pour toutes les erreurs :

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **Erreur** 5xx : survient lorsqu’une autre erreur côté serveur s’est produite et que l’enregistrement a échoué. Généralement, une réponse JSON telle que celle-ci est renvoyée, même si elle n’est pas garantie pour toutes les erreurs :

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

## Demande de traitement {#process-request}

L’ `process` opération envoie une tâche qui transforme une ressource source en plusieurs rendus, en fonction des instructions de la requête. Des notifications concernant la réussite de l&#39;exécution (type d&#39;événement `rendition_created`) ou toute erreur (type d&#39;événement `rendition_failed`) sont envoyées à un journal de Événement qui doit être récupéré à l&#39;aide [ou de l&#39;enregistrement](#register) une seule fois avant d&#39;effectuer un nombre quelconque de `/process` demandes. Les requêtes incorrectement formées échouent immédiatement avec un code d’erreur 400.

Les binaires sont référencés à l’aide d’URL, telles que les URL pré-signées Amazon AWS S3 ou les URL SAS d’Enregistrement Azure Blob, pour la lecture du `source` fichier (`GET` URL) et l’écriture des rendus (`PUT` URL). Le client est responsable de la génération de ces URL pré-signées.

| Paramètre | Valeur |
|--------------------------|------------------------------------------------------|
| Méthode | `POST` |
| Chemin    | `/process` |
| Type MIME | `application/json` |
| Header `Authorization` | Tous les en-têtes [liés à](#authentication-and-authorization)l’autorisation. |
| Header `x-request-id` | Facultatif, il est possible de définir par les clients un identifiant unique de bout en bout des demandes de traitement sur l’ensemble des systèmes. |
| Corps de requête | Doit être au format JSON de demande de processus, comme décrit ci-dessous. Il fournit des instructions sur la ressource à traiter et les rendus à générer. |

### JSON de demande de traitement {#process-request-json}

Le corps de requête de `/process` est un objet JSON avec ce schéma de haut niveau :

```json
{
    "source": "",
    "renditions" : []
}
```

Les champs disponibles sont les suivants :

| Nom | Type | Description | Exemple |
|--------------|----------|-------------|---------|
| `source` | `string` | URL de la ressource source à traiter. Facultatif en fonction du format de rendu demandé (ex. `fmt=zip`). | `"http://example.com/image.jpg"` |
| `source` | `object` | Description de la ressource source à traiter. Voir la description des champs [d&#39;objet](#source-object-fields) Source ci-dessous. Facultatif en fonction du format de rendu demandé (ex. `fmt=zip`). | `{"url": "http://example.com/image.jpg", "mimeType": "image/jpeg" }` |
| `renditions` | `array` | Rendus à générer à partir du fichier source. Chaque objet de rendu prend en charge les instructions [de](#rendition-instructions)rendu. Requis. | `[{ "target": "https://....", "fmt": "png" }]` |

Il `source` peut s’agir d’un `<string>` nom qui est considéré comme une URL ou `<object>` d’un champ supplémentaire. Les variantes suivantes sont similaires :

```json
"source": "http://example.com/image.jpg"
```

```json
"source": {
    "url": "http://example.com/image.jpg"
}
```

### Champs d’objet source {#source-object-fields}

| Nom | Type | Description | Exemple |
|-----------|----------|-------------|---------|
| `url` | `string` | URL de la ressource source à traiter. Requis. | `"http://example.com/image.jpg"` |
| `name` | `string` | Nom du fichier source. L&#39;extension de fichier dans le nom peut être utilisée si aucun type MIME ne peut être détecté. prévaut sur le nom de fichier dans le chemin d’URL ou le nom de fichier dans `content-disposition` l’en-tête de la ressource binaire. La valeur par défaut est &quot;file&quot;. | `"image.jpg"` |
| `size` | `number` | Taille du fichier source en octets. Prend la priorité sur `content-length` l’en-tête de la ressource binaire. | `10234` |
| `mimetype` | `string` | Type MIME du fichier de ressource source. prévaut sur l’en-tête de la ressource binaire. `content-type` | `"image/jpeg"` |

### Exemple de `process` demande complète {#complete-process-request-example}

```json
{
    "source": "https://www.adobe.com/content/dam/acom/en/lobby/lobby-bg-bts2017-logged-out-1440x860.jpg",
    "renditions" : [{
            "name": "image.48x48.png",
            "target": "https://some-presigned-put-url-for-image.48x48.png",
            "fmt": "png",
            "width": 48,
            "height": 48
        },{
            "name": "image.200x200.jpg",
            "target": "https://some-presigned-put-url-for-image.200x200.jpg",
            "fmt": "jpg",
            "width": 200,
            "height": 200
        },{
            "name": "cqdam.xmp.xml",
            "target": "https://some-presigned-put-url-for-cqdam.xmp.xml",
            "fmt": "xmp"
        },{
            "name": "cqdam.text.txt",
            "target": "https://some-presigned-put-url-for-cqdam.text.txt",
            "fmt": "text"
    }]
}
```

## Réponse du processus {#process-response}

La `/process` requête est immédiatement renvoyée avec succès ou échec en fonction de la validation de la requête de base. Le traitement réel des ressources se produit de manière asynchrone.

| Paramètre | Valeur |
|-----------------------|------------------------------------------------------|
| Type MIME | `application/json` |
| Header `X-Request-Id` | Identique à l’en-tête de `X-Request-Id` requête ou à celui généré de manière unique. Servez-vous pour identifier les demandes entre les systèmes et/ou les demandes d&#39;assistance. |
| Organe de réponse | Objet JSON contenant `ok` et `requestId` des champs. |

Codes d&#39;état :

* **200 Réussite**: Si la demande a été envoyée avec succès. La réponse JSON comprend `"ok": true`:

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **400 Requête** non valide : Si la requête n’est pas correctement formée, par exemple les champs obligatoires manquants dans le fichier JSON de la requête. La réponse JSON comprend `"ok": false`:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **401 Non autorisé**: Lorsque la demande ne dispose pas d’une [authentification](#authentication-and-authorization)valide. Un exemple peut être un jeton d&#39;accès non valide ou une clé d&#39;API non valide.
* **403 Interdit**: Lorsque la demande n&#39;a pas d&#39; [autorisation](#authentication-and-authorization)valide. Un exemple peut être un jeton d&#39;accès valide, mais le projet Adobe Developer Console (compte technique) n&#39;est pas abonné à tous les services requis.
* **429 Trop de requêtes**: Lorsque le système est surchargé par ce client ou en général. Les clients peuvent réessayer avec un retour sur investissement [](https://en.wikipedia.org/wiki/Exponential_backoff)exponentiel. Le corps est vide.
* **Erreur** 4xx : En cas d’erreur du client. Généralement, une réponse JSON telle que celle-ci est renvoyée, même si elle n’est pas garantie pour toutes les erreurs :

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **Erreur** 5xx : En cas d&#39;erreur côté serveur. Généralement, une réponse JSON telle que celle-ci est renvoyée, même si elle n’est pas garantie pour toutes les erreurs :

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

La plupart des clients sont susceptibles de réessayer la même requête avec un renvoi [](https://en.wikipedia.org/wiki/Exponential_backoff) exponentiel sur toute erreur, à l’ *exception* des problèmes de configuration tels que 401 ou 403, ou des requêtes non valides telles que 400. Outre la limitation régulière du taux par le biais de 429 réponses, une interruption ou une limitation de service temporaire peut entraîner des erreurs 5xx. Il serait alors conseillé de réessayer après un certain temps.

Toutes les réponses JSON (le cas échéant) incluent la valeur `requestId` qui est identique à celle de l’ `X-Request-Id` en-tête. Il est recommandé de lire à partir de l’en-tête, puisqu’il est toujours présent. La valeur `requestId` est également renvoyée dans tous les événements liés au traitement des demandes en tant que `requestId`valeur. Les clients ne doivent pas présumer du format de cette chaîne, il s&#39;agit d&#39;un identifiant de chaîne opaque.

## Inscription au post-traitement {#opt-in-to-post-processing}

Le SDK [](https://github.com/adobe/asset-compute-sdk) Asset Compute prend en charge un ensemble d’options de post-traitement des images de base. Les travailleurs personnalisés peuvent explicitement opt-in au post-traitement en définissant le champ `postProcess` de l’objet de rendu sur `true`.

Les cas d’utilisation pris en charge sont les suivants :

* Recadrez un rendu à un rectangle dont les limites sont définies par recadrage.w, recadrage.h, recadrage.x et recadrage.y. Elle est définie par `instructions.crop` dans l’objet de rendu.
* Redimensionnez les images en utilisant la largeur, la hauteur ou les deux. Il est défini par `instructions.width` et `instructions.height` dans l’objet de rendu. Pour redimensionner en utilisant uniquement la largeur ou la hauteur, définissez une seule valeur. Compute Service conserve les proportions.
* Définissez la qualité d’une image JPEG. Elle est définie par `instructions.quality` dans l’objet de rendu. La meilleure qualité est indiquée par `100` des valeurs plus petites indiquant une qualité réduite.
* Créez des images entrelacées. Elle est définie par `instructions.interlace` dans l’objet de rendu.
* Définissez les ppp pour ajuster la taille de rendu à des fins de publication sur ordinateur en ajustant l’échelle appliquée aux pixels. Il est défini par `instructions.dpi` dans l’objet de rendu pour modifier la résolution en dpi. Cependant, pour redimensionner l’image de sorte qu’elle ait la même taille à une résolution différente, suivez les `convertToDpi` instructions.
* Redimensionnez l’image de sorte que sa largeur ou sa hauteur de rendu reste identique à celle de l’image d’origine à la résolution de cible (ppp) spécifiée. Elle est définie par `instructions.convertToDpi` dans l’objet de rendu.

## Fichiers filigrane {#add-watermark}

Le SDK [](https://github.com/adobe/asset-compute-sdk) Asset Compute prend en charge l’ajout d’un filigrane aux fichiers d’image PNG, JPEG, TIFF et GIF. Le filigrane est ajouté à la suite des instructions de rendu dans l’ `watermark` objet du rendu.

Le filigrane est effectué pendant le post-traitement du rendu. Pour les fichiers de filigrane, le programme de travail personnalisé [opte pour le post-traitement](#opt-in-to-post-processing) en définissant le champ `postProcess` de l’objet de rendu sur `true`. Si le programme de travail n’accepte pas, le filigrane n’est pas appliqué, même si l’objet de filigrane est défini sur l’objet de rendu dans la requête.

## Instructions de rendu {#rendition-instructions}

Il s&#39;agit des options disponibles pour la `renditions` baie dans [/processus](#process-request).

### Champs communs {#common-fields}

| Nom | Type | Description | Exemple |
|-------------------|----------|-------------|---------|
| `fmt` | `string` | Le format de cible des rendus peut également être utilisé `text` pour l’extraction de texte et `xmp` pour l’extraction de métadonnées XMP au format xml. Voir Formats [pris en charge](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html) | `png` |
| `worker` | `string` | URL d’une application [](develop-custom-application.md)personnalisée. Doit être une `https://` URL. Si ce champ est présent, le rendu est créé par une application personnalisée. Tout autre champ de rendu défini est ensuite utilisé dans l’application personnalisée. | `"https://1234.adobeioruntime.net`<br>`/api/v1/web`<br>`/example-custom-worker-master/worker"` |
| `target` | `string` | URL vers laquelle le rendu généré doit être téléchargé à l’aide du PUT HTTP. | `http://w.com/img.jpg` |
| `target` | `object` | Informations de transfert d’URL présignées en plusieurs parties pour le rendu généré. Il s’agit du chargement [](https://jackrabbit.apache.org/oak/docs/features/direct-binary-access.html) AEM/Oak Direct Binary avec ce comportement [de transfert en](http://jackrabbit.apache.org/oak/docs/apidocs/org/apache/jackrabbit/api/binary/BinaryUpload.html)plusieurs parties.<br>Champs:<ul><li>`urls`: tableau de chaînes, une pour chaque URL de partie présignée</li><li>`minPartSize`: la taille minimale à utiliser pour une partie = url</li><li>`maxPartSize`: la taille maximale à utiliser pour une partie = url</li></ul> | `{ "urls": [ "https://part1...", "https://part2..." ], "minPartSize": 10000, "maxPartSize": 100000 }` |
| `userData` | `object` | Espace réservé facultatif contrôlé par le client et transmis tel quel aux événements de rendu. Permet aux clients d’ajouter des informations personnalisées pour identifier les événements de rendu. Ne doit pas être modifié ou utilisé dans les applications personnalisées, car les clients sont libres de modifier ce paramètre à tout moment. | `{ ... }` |

### Champs spécifiques au rendu {#rendition-specific-fields}

Pour obtenir une liste des formats de fichier actuellement pris en charge, voir Formats [de fichier](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html)pris en charge.

| Nom | Type | Description | Exemple |
|-------------------|----------|-------------|---------|
| `*` | `*` | Des champs personnalisés avancés peuvent être ajoutés qu’une application [](develop-custom-application.md) personnalisée comprend. |  |
| `embedBinaryLimit` | `number` en octets | Si cette valeur est définie et que la taille de fichier du rendu est inférieure à cette valeur, le rendu est incorporé dans le événement envoyé une fois la génération du rendu terminée. La taille maximale autorisée pour l’incorporation est de 32 Ko (32 x 1 024 octets). Si un rendu a une taille supérieure à la `embedBinaryLimit` limite, il est placé à un emplacement dans l’enregistrement cloud et n’est pas incorporé dans le événement. | `3276` |
| `width` | `number` | Largeur en pixels. uniquement pour les rendus d’image. | `200` |
| `height` | `number` | Hauteur en pixels. uniquement pour les rendus d’image. | `200` |
|  |  | Les proportions sont toujours conservées si : <ul> <li> Les deux `width` et `height` sont spécifiés, puis l’image correspond à la taille tout en conservant les proportions </li><li> Seule `width` ou seule `height` est spécifiée, l’image obtenue utilise la dimension correspondante tout en conservant les proportions</li><li> Si aucune `width` `height` valeur n’est spécifiée, la taille d’image d’origine en pixels est utilisée. Cela dépend du type de source. Pour certains formats, tels que les fichiers PDF, une taille par défaut est utilisée. Il peut y avoir une limite de taille maximale.</li></ul> |  |
| `quality` | `number` | Spécifiez la qualité jpeg dans la plage de `1` à `100`. Applicable uniquement aux rendus d’image. | `90` |
| `xmp` | `string` | Utilisé uniquement par l’écriture différée des métadonnées XMP, il est codé en base 64 XMP pour écrire dans le rendu spécifié. |  |
| `interlace` | `bool` | Créez un fichier PNG entrelacé ou GIF ou JPEG progressif en le définissant sur `true`. Il n&#39;a aucun effet sur les autres formats de fichier. |  |
| `jpegSize` | `number` | Taille approximative du fichier JPEG en octets. Il remplace tout `quality` paramètre. N’a aucun effet sur les autres formats. |  |
| `dpi` | `number` ou `object` | Définissez les ppp x et y. Pour plus de simplicité, il peut également être défini sur un nombre unique utilisé pour x et y. Elle n&#39;a aucun effet sur l&#39;image elle-même. | `96` ou `{ xdpi: 96, ydpi: 96 }` |
| `convertToDpi` | `number` ou `object` | x et y ppp rééchantillonnent les valeurs tout en conservant la taille physique. Pour plus de simplicité, il peut également être défini sur un nombre unique utilisé pour x et y. | `96` ou `{ xdpi: 96, ydpi: 96 }` |
| `files` | `array` | Liste des fichiers à inclure dans l’archive ZIP (`fmt=zip`). Chaque entrée peut être soit une chaîne URL, soit un objet avec les champs :<ul><li>`url`: URL de téléchargement du fichier</li><li>`path`: Stocker le fichier sous ce chemin dans le fichier ZIP</li></ul> | `[{ "url": "https://host/asset.jpg", "path": "folder/location/asset.jpg" }]` |
| `duplicate` | `string` | Gestion des duplicata pour les archives ZIP (`fmt=zip`). Par défaut, plusieurs fichiers stockés sous le même chemin d’accès dans le fichier ZIP génèrent une erreur. Si vous définissez cette valeur `duplicate` sur `ignore` , seul le premier fichier à stocker et le reste à ignorer sont enregistrés. | `ignore` |
| `watermark` | `object` | Contient des instructions sur le [filigrane](#watermark-specific-fields). |  |

### Champs spécifiques aux filigranes {#watermark-specific-fields}

Le format PNG est utilisé comme filigrane.

| Nom | Type | Description | Exemple |
|-------------------|----------|-------------|---------|
| `scale` | `number` | Echelle du filigrane, entre `0.0` et `1.0`. `1.0` signifie que le filigrane a son échelle d’origine (1:1) et que les valeurs inférieures réduisent la taille du filigrane. | Une valeur de `0.5` signifie la moitié de la taille d’origine. |
| `image` | `url` | URL du fichier PNG à utiliser en filigrane. |  |

## Événements asynchrones {#asynchronous-events}

Une fois le traitement d&#39;un rendu terminé ou lorsqu&#39;une erreur se produit, un événement est envoyé à un Journal [d&#39;E/S](https://www.adobe.io/apis/experienceplatform/events/documentation.html#!adobedocs/adobeio-events/master/intro/journaling_api.md)Adobe. Les clients doivent écouter l&#39;URL du journal fournie par le biais de [l&#39;inscription](#register). La réponse du journal comprend un `event` tableau constitué d&#39;un objet pour chaque événement, dont le `event` champ inclut la charge utile réelle du événement.

Le Type d&#39;événement d&#39;E/S d&#39;Adobe pour tous les événements du [!DNL Asset Compute Service] est `asset_compute`. Le journal est automatiquement abonné à ce type d&#39;événement uniquement et il n&#39;est pas nécessaire de le filtrer en fonction du Type d&#39;événement d&#39;E/S de l&#39;Adobe. Les types d&#39;événement spécifiques au service sont disponibles dans la `type` propriété du événement.

### Event types {#event-types}

| Événement | Description |
|---------------------|-------------|
| `rendition_created` | Envoyé pour chaque rendu traité et téléchargé avec succès. |
| `rendition_failed` | Envoyé pour chaque rendu dont le traitement ou le téléchargement a échoué. |

### Attributs de événement {#event-attributes}

| Attribut | Type | Événement | Description |
|-------------|----------|---------------|-------------|
| `date` | `string` | `*` | Horodatage lorsque le événement a été envoyé au format étendu simplifié [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) , tel que défini par JavaScript [Date.toISOString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString). |
| `requestId` | `string` | `*` | ID de la requête d’origine à `/process`, identique à `X-Request-Id` l’en-tête. |
| `source` | `object` | `*` | Le `source` de la `/process` demande. |
| `userData` | `object` | `*` | Le rendu `userData` de la `/process` demande si défini. |
| `rendition` | `object` | `rendition_*` | Objet de rendu correspondant transmis dans `/process`. |
| `metadata` | `object` | `rendition_created` | Propriétés [de métadonnées](#metadata) du rendu. |
| `errorReason` | `string` | `rendition_failed` | Le [motif](#error-reasons) d’échec du rendu est le cas échéant. |
| `errorMessage` | `string` | `rendition_failed` | Texte donnant plus de détails sur l’échec du rendu, le cas échéant. |

### Métadonnées {#metadata}

| Propriété | Description |
|--------|-------------|
| `repo:size` | Taille du rendu en octets. |
| `repo:sha1` | Résumé sha1 du rendu. |
| `dc:format` | Type MIME du rendu. |
| `repo:encoding` | Codage du jeu de caractères du rendu au cas où il s’agirait d’un format texte. |
| `tiff:ImageWidth` | Largeur du rendu en pixels. Uniquement disponible pour les rendus d’image. |
| `tiff:ImageLength` | Longueur du rendu en pixels. Uniquement disponible pour les rendus d’image. |

### Raisons de l’erreur {#error-reasons}

| Reason | Description |
|---------|-------------|
| `RenditionFormatUnsupported` | Le format de rendu demandé n&#39;est pas pris en charge pour la source donnée. |
| `SourceUnsupported` | La source spécifique n&#39;est pas prise en charge même si le type est pris en charge. |
| `SourceCorrupt` | Les données source sont endommagées. Inclut des fichiers vides. |
| `RenditionTooLarge` | Impossible de télécharger le rendu à l&#39;aide des URL pré-signées fournies dans `target`. La taille réelle du rendu est disponible sous forme de métadonnées dans `repo:size` et peut être utilisée par le client pour traiter à nouveau ce rendu avec le nombre approprié d’URL pré-signées. |
| `GenericError` | Toute autre erreur inattendue. |
