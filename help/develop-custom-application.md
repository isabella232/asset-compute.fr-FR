---
title: Développer pour [!DNL Asset Compute Service].
description: Créez des applications personnalisées à l’aide [!DNL Asset Compute Service]de.
translation-type: tm+mt
source-git-commit: 127895cf1bab59546f9ba0be2b3b7a935627effb
workflow-type: tm+mt
source-wordcount: '1496'
ht-degree: 0%

---


# Développement d’une application personnalisée {#develop}

Avant de commencer à développer une application personnalisée :

* Assurez-vous que toutes les [conditions préalables](/help/understand-extensibility.md#prerequisites-and-provisioning) sont remplies.
* Installez les outils [logiciels](/help/setup-environment.md#create-dev-environment)requis.
* Voir [configurer votre environnement](setup-environment.md) pour vous assurer que vous êtes prêt à créer une application personnalisée.

## Création d’une application personnalisée {#create-custom-application}

Assurez-vous que l&#39;interface de ligne de commande [E/S](https://github.com/adobe/aio-cli) Adobe est installée localement.

1. Pour créer une application personnalisée, [créez une application](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#4-bootstrapping-new-app-using-the-cli)Firefly. Pour ce faire, exécutez `aio app init <app-name>` dans votre terminal.

   Si vous ne vous êtes pas encore connecté, cette commande invite un navigateur à vous connecter à la console [de développement des](https://console.adobe.io/) Adobes avec votre Adobe ID. Voir [ici](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli) pour plus d’informations sur la connexion à partir du cli.

   Adobe vous recommande de vous connecter. Si vous rencontrez des problèmes, suivez les instructions [pour créer une application sans vous connecter](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#42-developer-is-not-logged-in-as-enterprise-organization-user).

1. Après vous être connecté, suivez les invites de l’interface de ligne de commande et sélectionnez le `Organization`, `Project`et `Workspace` à utiliser pour l’application. Choisissez le projet et l’espace de travail que vous avez créés lors de la [configuration de votre environnement](setup-environment.md).

   ```sh
   $ aio app init <app-name>
   Retrieving information from Adobe I/O Console..
   ? Select Org My Adobe Org
   ? Select Project MyFireflyProject
   ? Select Workspace myworkspace
   create console.json
   ```

1. Lorsque vous y êtes invité `Which Adobe I/O App features do you want to enable for this project?`, sélectionnez au moins `Actions`:

   ```bash
   ? Which Adobe I/O App features do you want to enable for this project?
   select components to include (Press <space> to select, <a> to toggle all, <i> to invert selection)
   ❯◉ Actions: Deploy Runtime actions
   ◯ Events: Publish to Adobe I/O Events
   ◯ Web Assets: Deploy hosted static assets
   ◯ CI/CD: Include GitHub Actions based workflows for Build, Test and Deploy
   ```

1. Lorsque vous y êtes invité `Which type of sample actions do you want to create?`, veillez à sélectionner `Adobe Asset Compute Worker`:

   ```bash
   ? Which type of sample actions do you want to create?
   Select type of actions to generate
   ❯◉ Adobe Asset Compute Worker
   ◯ Generic
   ```

1. Suivez les autres invites et ouvrez la nouvelle application dans Visual Studio Code (ou votre éditeur de code favori). Il contient l’échafaudage et l’exemple de code pour une application personnalisée.

   Lisez ici les [principaux composants d&#39;une application](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application)Firefly.

   L’application de modèle utilise notre SDK [](https://github.com/adobe/asset-compute-sdk#asset-compute-sdk) Asset Compute pour le transfert, le téléchargement et l’orchestration des rendus d’application. Les développeurs n’ont donc qu’à implémenter la logique d’application personnalisée. Dans le `actions/<worker-name>` dossier, le `index.js` fichier indique où ajouter le code d’application personnalisé.

Voir [des exemples d’applications](#try-sample) personnalisées pour consulter des exemples et des idées d’applications personnalisées.

### Ajouter les informations d’identification {#add-credentials}

Lorsque vous vous connectez lors de la création de l’application, la plupart des informations d’identification de la luciole sont collectées dans votre fichier ENV. Toutefois, l’utilisation de l’outil de développement nécessite des informations d’identification supplémentaires.

<!-- TBD: Check if manual setup of credentials is required.
Manual set up of credentials is removed from troubleshooting and best practices page. Link was broken.
If you did not log in, refer to our troubleshooting guide to [set up credentials manually](troubleshooting.md).
-->

#### Informations d’identification de l’enregistrement de l’outil de développement {#developer-tool-credentials}

L’outil de développement utilisé pour tester les applications personnalisées avec le véritable conteneur [!DNL Asset Compute service] nécessite un enregistrement cloud pour l’hébergement des fichiers de test et pour la réception et l’affichage des rendus générés par les applications.

>[!NOTE]
>
>Il est distinct de l’enregistrement de cloud de [!DNL Adobe Experience Manager] en tant que Cloud Service. Il s’applique uniquement au développement et au test avec l’outil de développement Asset Compute.

Assurez-vous d’avoir accès à un conteneur [d’enregistrement de cloud](https://github.com/adobe/asset-compute-devtool#prerequisites)pris en charge. Ce conteneur peut être partagé par plusieurs développeurs sur différents projets, selon les besoins.

#### Ajouter les informations d’identification au fichier ENV {#add-credentials-env-file}

Ajoutez les informations d’identification suivantes pour l’outil de développement dans le fichier ENV à la racine de votre projet Firefly :

1. Ajoutez le chemin d&#39;accès absolu au fichier de clé privée créé lors de l&#39;ajout de services à votre projet Firefly :

   ```conf
   ASSET_COMPUTE_PRIVATE_KEY_FILE_PATH=
   ```

1. Ajoutez les informations d&#39;identification S3 ou Azure Enregistrement. Vous n’avez besoin que d’accéder à une seule solution d’enregistrement de cloud.

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

## Exécuter l’application {#run-custom-application}

Avant d’exécuter l’application avec l’outil Asset Compute Developer Tool, configurez correctement les [informations d’identification](#developer-tool-credentials).

Pour exécuter l’application dans l’outil de développement, utilisez `aio app run` la commande. Il déploie l’action sur Adobe I/O Runtime et début l’outil de développement sur votre machine locale. Cet outil est utilisé pour tester les demandes d’application pendant le développement. Voici un exemple de demande de rendu :

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
>N&#39;utilisez pas l&#39; `--local` indicateur avec la `run` commande. Il ne fonctionne pas avec les applications [!DNL Asset Compute] personnalisées et l’outil Asset Compute Developer. Les applications personnalisées sont appelées par le [!DNL Asset Compute Service] qui ne peut pas accéder aux actions exécutées sur les machines locales des développeurs.

Voir [ici](test-custom-application.md) comment tester et déboguer votre application. Lorsque vous avez terminé de développer votre application personnalisée, [déployez votre application](deploy-custom-application.md)personnalisée.

## Essayer l&#39;exemple d&#39;application fourni par l&#39;Adobe {#try-sample}

Voici des exemples d’applications personnalisées :

* [travailleur-base](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic)
* [travailleur-animal-images](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-animal-pictures)

### Modèle d’application personnalisée {#template-custom-application}

Le [programme de travail de base](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic) est une application de modèle. Il génère un rendu en copiant simplement le fichier source. Le contenu de cette application est le modèle reçu lors du choix `Adobe Asset Compute` de la création de l’application aio.

Le fichier de l’application [`worker-basic.js`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) utilise le [`asset-compute-sdk`](https://github.com/adobe/asset-compute-sdk#overview) pour télécharger le fichier source, orchestrer chaque traitement de rendu et télécharger les rendus résultants dans l’enregistrement cloud.

Le code [`renditionCallback`](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) défini dans l’application permet d’exécuter toute la logique de traitement de l’application. Le rappel de rendu dans `worker-basic` copie simplement le contenu du fichier source dans le fichier de rendu.

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

exports.main = worker(async (source, rendition) => {
    // copy source to rendition to transfer 1:1
    await fs.copyFile(source.path, rendition.path);
});
```

## Appel d’une API externe {#call-external-api}

Dans le code de l’application, vous pouvez effectuer des appels d’API externes pour faciliter le traitement de l’application. Vous trouverez ci-dessous un exemple de fichier d’application appelant une API externe.

```javascript
exports.main = worker(async function (source, rendition) {

    const response = await fetch('https://adobe.com', {
        method: 'GET',
        Authorization: params.AUTH_KEY
    })
});
```

Par exemple, le [`worker-animal-pictures`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L46) fait une requête de récupération d’une URL statique à partir de Wikimedia à l’aide de la [`node-httptransfer`](https://github.com/adobe/node-httptransfer#node-httptransfer) bibliothèque.

<!-- TBD: Revisit later to see if this note is required.
>[!NOTE]
>
>For extra authorization for these API calls, see [custom authorization checks](#custom-authorization-checks).
-->

### Transmettre des paramètres personnalisés {#pass-custom-parameters}

Vous pouvez transmettre des paramètres personnalisés définis par le biais des objets de rendu. Ils peuvent être référencés dans l’application dans des instructions [`rendition`](https://github.com/adobe/asset-compute-sdk#rendition). Voici un exemple d’objet de rendu :

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg",
        "my-custom-parameter": "my-custom-parameter-value"
    }
]
```

Voici un exemple de fichier d’application accédant à un paramètre personnalisé :

```javascript
exports.main = worker(async function (source, rendition) {

    const customParam = rendition.instructions['my-custom-parameter'];
    console.log('Custom paramter:', customParam);
    // should print out `Custom parameter: "my-custom-parameter-value"`
});
```

Le `example-worker-animal-pictures` transmet un paramètre personnalisé [`animal`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L39) pour déterminer le fichier à récupérer de Wikimedia.

## Prise en charge de l’authentification et de l’autorisation {#authentication-authorization-support}

Par défaut, les applications personnalisées Asset Compute sont fournies avec des contrôles d’autorisation et d’authentification pour les applications de luciole. Cette fonction est activée en définissant l&#39; `require-adobe-auth` annotation sur `true` dans le `manifest.yml`.

### Accès à d&#39;autres API d&#39;Adobe {#access-adobe-apis}

<!-- TBD: Revisit this section. Where do we document console workspace creation?
-->

Ajoutez les services d&#39;API à l&#39;espace de travail [!DNL Asset Compute] Console créé lors de la configuration. Ces services font partie du jeton d&#39;accès JWT généré par [!DNL Asset Compute Service]. Le jeton et les autres informations d’identification sont accessibles dans l’objet d’action de l’application `params` .

```javascript
const accessToken = params.auth.accessToken; // JWT token for Technical Account with entitlements from the console workspace to the API service
const clientId = params.auth.clientId; // Technical Account client Id
const orgId = params.auth.orgId; // Experience Cloud Organization
```

### Transmettre les informations d’identification des systèmes tiers {#pass-credentials-for-tp}

Pour gérer les informations d’identification d’autres services externes, transmettez-les en tant que paramètres par défaut sur les actions. Ils sont automatiquement chiffrés en transit. Pour plus d’informations, voir [Création d’actions dans le guide](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/creating_actions.md)du développeur d’exécution. Définissez-les ensuite à l’aide de variables d’environnement lors du déploiement. Ces paramètres sont accessibles dans l&#39; `params` objet à l&#39;intérieur de l&#39;action.

Définissez les paramètres par défaut à l’intérieur du `inputs` dans le `manifest.yml`:

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

L’ `$VAR` expression lit la valeur d’une variable d’environnement nommée `VAR`.

Au cours du développement, la valeur peut être définie dans le fichier ENV local car elle lit `aio` automatiquement les variables d&#39;environnement des fichiers ENV en plus des variables définies dans le shell appelant. Dans cet exemple, le fichier ENV ressemble à ce qui suit :

```CONF
#...
SECRET_KEY=secret-value
```

Pour le déploiement de la production, il est possible de définir les variables d&#39;environnement dans le système d&#39;identification des clients, par exemple en utilisant les secrets dans les actions GitHub. Enfin, accédez aux paramètres par défaut de l’application en tant que tels :

```javascript
const key = params.secretKey;
```

## Dimensionnement des applications {#sizing-workers}

Une application s’exécute dans un conteneur de Adobe I/O Runtime avec [des limites](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/system_settings.md) qui peuvent être configurées via le `manifest.yml`:

```yaml
    actions:
      myworker:
        function: /actions/myworker/index.js
        limits:
          timeout: 300000
          memorySize: 512
          concurrency: 1
```

En raison du traitement plus étendu généralement effectué par les applications Asset Compute, il est plus probable que l’on doive ajuster ces limites pour obtenir des performances optimales (suffisamment grandes pour gérer les ressources binaires) et une efficacité (sans gaspiller les ressources en raison de la mémoire de conteneur inutilisée).

Le délai d’expiration par défaut pour les actions dans l’exécution est d’une minute, mais il peut être augmenté en définissant la `timeout` limite (en millisecondes). Si vous prévoyez de traiter des fichiers plus volumineux, augmentez cette durée. Tenez compte du temps total nécessaire pour télécharger la source, traiter le fichier et télécharger le rendu. Si une action expire, c’est-à-dire qu’elle ne renvoie pas l’activation avant la limite de délai spécifiée, l’exécution ignore le conteneur et ne le réutilise pas.

Les applications de calcul des ressources par nature tendent à être liées aux E/S réseau et disque. Le fichier source doit d’abord être téléchargé, le traitement est souvent lourd en E/S et les rendus résultants sont ensuite téléchargés à nouveau.

La mémoire disponible pour un conteneur d&#39;action est spécifiée par `memorySize` en Mo. Actuellement, cela définit également le niveau d&#39;accès de l&#39;UC au conteneur et, plus important encore, c&#39;est un élément clé du coût d&#39;utilisation de l&#39;exécution (les conteneurs plus volumineux coûtent plus cher). Utilisez ici une valeur plus élevée lorsque votre traitement nécessite davantage de mémoire ou d&#39;unité centrale, mais veillez à ne pas gaspiller de ressources car plus les conteneurs sont gros, plus le débit global est faible.

En outre, il est possible de contrôler la concurrence d&#39;action dans un conteneur à l&#39;aide du `concurrency` paramètre. Il s’agit du nombre d’activations simultanées qu’un seul conteneur (de la même action) obtient. Dans ce modèle, le conteneur d’action est semblable à un serveur Node.js recevant plusieurs requêtes simultanées, jusqu’à cette limite. Si elle n’est pas définie, la valeur par défaut dans l’environnement d’exécution est 200, ce qui est idéal pour les actions de luciole plus petites, mais généralement trop pour les applications Asset Compute, compte tenu de leur traitement local et de leur activité sur disque plus intensifs. Certaines applications, en fonction de leur mise en oeuvre, peuvent également ne pas fonctionner correctement avec les activités simultanées. Le SDK Asset Compute garantit que les activations sont séparées en écrivant des fichiers dans différents dossiers uniques.

Testez les applications pour trouver les nombres optimaux pour `concurrency` et `memorySize`. Conteneurs plus grands = limite de mémoire plus élevée peut permettre une plus grande simultanéité mais peut également être un gaspillage pour un trafic plus faible.
