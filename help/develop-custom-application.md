---
title: Développer pour  [!DNL Asset Compute Service]
description: Créer des applications personnalisées à l’aide d’ [!DNL Asset Compute Service].
translation-type: tm+mt
source-git-commit: 95e384d2a298b3237d4f93673161272744e7f44a
workflow-type: tm+mt
source-wordcount: '1562'
ht-degree: 95%

---


# Développement d’une application personnalisée {#develop}

Avant de commencer à développer une application personnalisée :

* Assurez-vous que toutes les [conditions préalables](/help/understand-extensibility.md#prerequisites-and-provisioning) sont remplies.
* Installez les [outils logiciels requis](/help/setup-environment.md#create-dev-environment).
* Voir [Configuration de votre environnement](setup-environment.md) pour vous assurer que vous êtes prêt à créer une application personnalisée.

## Création d’une application personnalisée {#create-custom-application}

Assurez-vous que [[!DNL Adobe I/O] CLI](https://github.com/adobe/aio-cli) est installé localement.

1. Pour créer une application personnalisée, [créez une application Firefly](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#4-bootstrapping-new-app-using-the-cli). Pour ce faire, exécutez `aio app init <app-name>` sur votre terminal.

   Si vous n’êtes pas encore connecté, cette commande appelle un navigateur qui vous invite à vous connecter à [Adobe Developer Console](https://console.adobe.io/) avec votre Adobe ID. Voir [ici](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli) pour plus d’informations sur la connexion à partir de l’interface de ligne de commande.

   Adobe vous recommande de vous connecter. Si vous rencontrez des problèmes, appliquez les instructions [pour créer une application sans vous connecter](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#42-developer-is-not-logged-in-as-enterprise-organization-user).

1. Après vous être connecté, suivez les invites de l’interface de ligne de commande et sélectionnez les éléments `Organization`, `Project` et `Workspace` à utiliser pour l’application. Choisissez le projet et l’espace de travail que vous avez créés lors de la [configuration de votre environnement](setup-environment.md).

   ```sh
   $ aio app init <app-name>
   Retrieving information from [!DNL Adobe I/O] Console.
   ? Select Org My Adobe Org
   ? Select Project MyFireflyProject
   ? Select Workspace myworkspace
   create console.json
   ```

1. Lorsque l’invite `Which Adobe I/O App features do you want to enable for this project?` s’affiche, sélectionnez `Actions`. Veillez à désélectionner l’option `Web Assets` car les ressources Web utilisent différentes vérifications d’authentification et d’autorisation.

   ```bash
   ? Which Adobe I/O App features do you want to enable for this project?
   select components to include (Press <space> to select, <a> to toggle all, <i> to invert selection)
   ❯◉ Actions: Deploy Runtime actions
   ◯ Events: Publish to Adobe I/O Events
   ◯ Web Assets: Deploy hosted static assets
   ◯ CI/CD: Include GitHub Actions based workflows for Build, Test and Deploy
   ```

1. À l’invite `Which type of sample actions do you want to create?`, veillez à sélectionner `Adobe Asset Compute Worker` :

   ```bash
   ? Which type of sample actions do you want to create?
   Select type of actions to generate
   ❯◉ Adobe Asset Compute Worker
   ◯ Generic
   ```

1. Suivez les autres invites et ouvrez la nouvelle application dans Visual Studio Code (ou votre éditeur de code préféré). Il contient la structure et l’exemple de code pour une application personnalisée.

   Lisez ici les [principaux composants d’une application Firefly](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application).

   L’application de modèle utilise notre [SDK Asset Compute](https://github.com/adobe/asset-compute-sdk#asset-compute-sdk) pour le chargement, le téléchargement et l’orchestration des rendus d’application. Les développeurs n’ont donc qu’à implémenter la logique de l’application personnalisée. Dans le dossier `actions/<worker-name>`, le fichier `index.js` indique où ajouter le code d’application personnalisé.

Voir [Exemples d’applications personnalisées](#try-sample) pour consulter des exemples et des idées d’applications personnalisées.

### Ajout des informations d’identification {#add-credentials}

Lorsque vous vous connectez lors de la création de l’application, la plupart des informations d’identification de Firefly sont collectées dans votre fichier ENV. Toutefois, l’utilisation de l’outil de développement nécessite des informations d’identification supplémentaires.

<!-- TBD: Check if manual setup of credentials is required.
Manual set up of credentials is removed from troubleshooting and best practices page. Link was broken.
If you did not log in, refer to our troubleshooting guide to [set up credentials manually](troubleshooting.md).
-->

#### Informations d’identification pour le stockage de l’outil de développement {#developer-tool-credentials}

L’outil de développement utilisé pour tester les applications personnalisées avec [!DNL Asset Compute service] nécessite un conteneur de stockage dans le cloud pour l’hébergement des fichiers de test, ainsi que la réception et l’affichage des rendus générés par les applications.

>[!NOTE]
>
>Il est distinct de l’espace de stockage dans le cloud d’[!DNL Adobe Experience Manager] as a [!DNL Cloud Service]. Il s’applique uniquement au développement et au test avec l’outil de développement Asset Compute.

Assurez-vous d’avoir accès à un [conteneur de stockage dans le cloud pris en charge](https://github.com/adobe/asset-compute-devtool#prerequisites). Ce conteneur peut être partagé par plusieurs développeurs sur différents projets, selon les besoins.

#### Ajout des informations d’identification au fichier ENV {#add-credentials-env-file}

Ajoutez les informations d’identification suivantes pour l’outil de développement dans le fichier ENV dans la racine de votre projet Firefly :

1. Ajoutez le chemin d’accès absolu au fichier de clé privée créé lors de l’ajout de services à votre projet Firefly :

   ```conf
   ASSET_COMPUTE_PRIVATE_KEY_FILE_PATH=
   ```

1. Si le fichier `console.json` ne se trouve pas directement dans la racine de votre application Firefly, ajoutez le chemin d’accès absolu au fichier JSON d’intégration d’Adobe Developer Console. Il s’agit du même fichier [`console.json`](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#42-developer-is-not-logged-in-as-enterprise-organization-user) que celui téléchargé dans l’espace de travail du projet. À titre de variante, vous pouvez également utiliser la commande `aio app use <path_to_console_json>` au lieu d’ajouter le chemin d’accès à votre fichier ENV.

   ```conf
   ASSET_COMPUTE_INTEGRATION_FILE_PATH=
   ```

1. Ajoutez les informations d’identification S3 ou de la solution de stockage Azure. Vous n’avez besoin d’accéder qu’à une seule solution d’espace de stockage dans le cloud.

   ```conf
   # S3 credentials
   S3_BUCKET=
   AWS_ACCESS_KEY_ID=
   AWS_SECRET_ACCESS_KEY=
   AWS_REGION=
   
   # Azure Storage credentials
   AZURE_STORAGE_ACCOUNT=
   AZURE_STORAGE_KEY=
   AZURE_STORAGE_CONTAINER_NAME=
   ```

## Exécution de l’application {#run-custom-application}

Avant d’exécuter l’application avec l’outil de développement Asset Compute, configurez correctement les [informations d’identification](#developer-tool-credentials).

Pour exécuter l’application dans l’outil de développement, utilisez la commande `aio app run`. Il déploie l’action sur [!DNL Adobe I/O] Runtime et début l’outil de développement sur votre machine locale. Cet outil est utilisé pour tester les demandes des applications au cours du développement. Voici un exemple de demande de rendu :

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg"
    }
]
```

>[!NOTE]
>
>N’utilisez pas l’indicateur `--local` avec la commande `run`. Il ne fonctionne pas avec les applications personnalisées [!DNL Asset Compute] et l’outil de développement Asset Compute. Les applications personnalisées sont appelées par [!DNL Asset Compute Service] qui ne peut pas accéder aux actions exécutées sur les machines locales des développeurs.

Voir [ici](test-custom-application.md) comment tester et déboguer votre application. Lorsque vous avez terminé de développer votre application personnalisée, [déployez-la](deploy-custom-application.md).

## Essai de l’exemple d’application fourni par Adobe {#try-sample}

Voici des exemples d’applications personnalisées :

* [worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic)
* [worker-animal-pictures](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-animal-pictures)

### Modèle d’application personnalisée {#template-custom-application}

[worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic) est un modèle d’application. Il génère un rendu en copiant simplement le fichier source. Le contenu de cette application est le modèle reçu lors du choix d’`Adobe Asset Compute` lors de la création de l’application aio.

Le fichier de l’application [`worker-basic.js`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) utilise le [`asset-compute-sdk`](https://github.com/adobe/asset-compute-sdk#overview) pour télécharger le fichier source, orchestrer le traitement de chaque rendu et charger les rendus résultants dans l’espace de stockage dans le cloud.

Le [`renditionCallback`](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) défini dans le code de l’application, permet d’exécuter toute la logique de traitement de l’application. Le rappel de rendu de `worker-basic` copie simplement le contenu du fichier source dans le fichier de rendu.

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

exports.main = worker(async (source, rendition) => {
    // copy source to rendition to transfer 1:1
    await fs.copyFile(source.path, rendition.path);
});
```

## Appel d’une API externe {#call-external-api}

Dans le code de l’application, vous pouvez effectuer des appels d’API externe pour faciliter le traitement de l’application. Vous trouverez ci-dessous un exemple de fichier d’application appelant une API externe.

```javascript
exports.main = worker(async function (source, rendition) {

    const response = await fetch('https://adobe.com', {
        method: 'GET',
        Authorization: params.AUTH_KEY
    })
});
```

Par exemple, [`worker-animal-pictures`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L46) effectue une requête de récupération d’une URL statique à partir de Wikimedia à l’aide de la bibliothèque [`node-httptransfer`](https://github.com/adobe/node-httptransfer#node-httptransfer).

<!-- TBD: Revisit later to see if this note is required.
>[!NOTE]
>
>For extra authorization for these API calls, see [custom authorization checks](#custom-authorization-checks).
-->

### Transmission de paramètres personnalisés {#pass-custom-parameters}

Vous pouvez transmettre des paramètres personnalisés définis à l’aide des objets de rendu. Ils peuvent être référencés dans l’application au moyen d’[`rendition`instructions](https://github.com/adobe/asset-compute-sdk#rendition). Voici un exemple d’objet de rendu :

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg",
        "my-custom-parameter": "my-custom-parameter-value"
    }
]
```

Voici un exemple de fichier d’application accédant à un paramètre personnalisé :

```javascript
exports.main = worker(async function (source, rendition) {

    const customParam = rendition.instructions['my-custom-parameter'];
    console.log('Custom paramter:', customParam);
    // should print out `Custom parameter: "my-custom-parameter-value"`
});
```

`example-worker-animal-pictures` transmet un paramètre personnalisé [`animal`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L39) pour déterminer le fichier à récupérer auprès de Wikimedia.

## Prise en charge de l’authentification et de l’autorisation {#authentication-authorization-support}

Par défaut, les applications personnalisées Asset Compute sont fournies avec des contrôles d’autorisation et d’authentification pour les applications Firefly. Cette fonction est activée en définissant l’annotation `require-adobe-auth` sur `true` dans `manifest.yml`.

### Accès à d’autres API d’Adobe {#access-adobe-apis}

<!-- TBD: Revisit this section. Where do we document console workspace creation?
-->

Ajoutez les services d’API à l’espace de travail de la console [!DNL Asset Compute] créé lors de la configuration. Ces services font partie du jeton d’accès JWT généré par [!DNL Asset Compute Service]. Le jeton et les autres informations d’identification sont accessibles dans l’objet `params` d’action de l’application.

```javascript
const accessToken = params.auth.accessToken; // JWT token for Technical Account with entitlements from the console workspace to the API service
const clientId = params.auth.clientId; // Technical Account client Id
const orgId = params.auth.orgId; // Experience Cloud Organization
```

### Transmission des informations d’identification des systèmes tiers {#pass-credentials-for-tp}

Pour gérer les informations d’identification d’autres services externes, transmettez-les sous la forme de paramètres par défaut sur les actions. Ils sont automatiquement chiffrés en transit. Pour plus d’informations, voir [Création d’actions dans le guide du développeur Runtime](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/creating_actions.md). Définissez-les ensuite à l’aide de variables d’environnement lors du déploiement. Ces paramètres sont accessibles dans l’objet `params` à l’intérieur de l’action.

Définissez les paramètres par défaut dans `inputs` dans le fichier `manifest.yml` :

```yaml
packages:
  __APP_PACKAGE__:
    actions:
      worker:
        function: 'index.js'
        runtime: 'nodejs:10'
        web: true
        inputs:
           secretKey: $SECRET_KEY
        annotations:
          require-adobe-auth: true
```

L’expression `$VAR` lit la valeur d’une variable d’environnement nommée `VAR`.

Au cours du développement, la valeur peut être définie dans le fichier ENV local, car `aio` lit automatiquement les variables d’environnement des fichiers ENV en plus de celles définies dans le shell appelant. Dans cet exemple, le fichier ENV ressemble à ce qui suit :

```CONF
#...
SECRET_KEY=secret-value
```

Pour le déploiement de la production, il est possible de définir les variables d’environnement dans le système d’intégration continue (CI), par exemple en utilisant des secrets dans les actions GitHub. Enfin, accédez aux paramètres par défaut de l’application en tant que tels :

```javascript
const key = params.secretKey;
```

## Dimensionnement des applications {#sizing-workers}

Une application s&#39;exécute dans un conteneur de l&#39;exécution [!DNL Adobe I/O] avec [limites](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/system_settings.md) qui peut être configuré par l&#39;intermédiaire de `manifest.yml` :

```yaml
    actions:
      myworker:
        function: /actions/myworker/index.js
        limits:
          timeout: 300000
          memorySize: 512
          concurrency: 1
```

En raison du traitement plus étendu généralement effectué par les applications Asset Compute, il est plus probable qu’il y ait à ajuster ces limites pour optimiser les performances (suffisamment pour gérer les ressources binaires) et l’efficacité (sans gaspiller les ressources en raison de la mémoire de conteneur inutilisée).

Le délai d’expiration par défaut pour les actions du Runtime est d’une minute, mais il peut être augmenté en définissant la limite `timeout` (en millisecondes). Si vous prévoyez de traiter des fichiers plus volumineux, augmentez cette durée. Tenez compte du temps total nécessaire pour télécharger la source, traiter le fichier et charger le rendu. Si une action atteint le délai d’expiration, c’est-à-dire si elle ne renvoie pas l’activation avant la limite de délai spécifiée, le Runtime ignore le conteneur et ne le réutilise pas.

Les applications d&#39;Asset compute par nature tendent à être liées au réseau et au disque. Le fichier source doit d’abord être téléchargé, le traitement nécessite souvent beaucoup de ressources, puis les rendus résultants sont à nouveau téléchargés.

La mémoire disponible pour un conteneur d’action est spécifiée par le paramètre `memorySize` en Mo. Actuellement, il définit également le niveau d’accès du conteneur à l’unité centrale. Il s’agit surtout d’un élément clé du coût d’utilisation du Runtime (les conteneurs plus volumineux coûtent plus cher). Utilisez ici une valeur plus élevée lorsque votre traitement nécessite davantage de mémoire ou d’unité centrale. Veillez toutefois à ne pas gaspiller les ressources, car plus les conteneurs sont volumineux, plus le débit global est faible.

En outre, il est possible de contrôler la simultanéité des actions dans un conteneur à l’aide du paramètre `concurrency`. Il s’agit du nombre d’activations simultanées obtenues par un seul conteneur (de la même action). Dans ce modèle, le conteneur d’action est semblable à un serveur Node.js recevant plusieurs requêtes simultanées, jusqu’à cette limite. Si elle n’est pas définie, la valeur par défaut dans le Runtime est de 200, ce qui est idéal pour les actions Firefly plus modestes, mais généralement trop élevé pour les applications Asset Compute, compte tenu de l’intensité accrue des activités de disque et des traitements locaux. Selon leur mise en œuvre, il est possible que certaines applications ne fonctionnent pas correctement avec les activités simultanées. Le SDK Asset Compute garantit que les activations sont séparées en écrivant des fichiers dans différents dossiers uniques.

Testez les applications pour trouver les valeurs optimales de `concurrency` et `memorySize`. Des conteneurs plus volumineux reviennent à une limite de mémoire plus haute, ce qui permet une plus grande simultanéité, mais peut aussi entraîner du gaspillage si le trafic est plus faible.
