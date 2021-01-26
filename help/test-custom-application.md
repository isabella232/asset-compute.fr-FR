---
title: Tester et déboguer [!DNL Asset Compute Service] l’application personnalisée
description: Test et débogage d’une application personnalisée  [!DNL Asset Compute Service] .
translation-type: tm+mt
source-git-commit: 95e384d2a298b3237d4f93673161272744e7f44a
workflow-type: tm+mt
source-wordcount: '787'
ht-degree: 99%

---


# Test et débogage d’une application personnalisée {#test-debug-custom-worker}

## Exécution de tests unitaires pour une application personnalisée {#test-custom-worker}

Installez [Docker Desktop](https://www.docker.com/get-started) sur votre ordinateur. Pour tester un programme de travail personnalisé, exécutez la commande suivante à la racine de l’application :

```bash
$ aio app test
```

<!-- TBD
To run tests for a custom application, run `adobe-asset-compute test-worker` command in the root of the custom application application application.

Document interactively running `adobe-asset-compute` commands `test-worker` and `run-worker`.
-->

elle exécute un framework de test unitaire personnalisé pour les actions de l’application Asset Compute dans le projet comme décrit ci-dessous. Ce framework est connecté via une configuration dans le fichier `package.json`. Il est également possible d’effectuer des tests unitaires JavaScript tels que Jest. `aio app test` exécute les deux.

Le plug-in [aio-cli-plugin-asset-compute](https://github.com/adobe/aio-cli-plugin-asset-compute#install-as-local-devdependency) est incorporé en tant que dépendance de développement dans l’application personnalisée pour qu’il ne soit pas nécessaire de l’installer sur les systèmes de création/test.

### Framework de test unitaire d’application {#unit-test-framework}

Le framework de test unitaire d’application Asset Compute permet de tester les applications sans écrire de code. Il repose sur le principe d’un fichier entre la source et le rendu pour les applications. Il est nécessaire de configurer une certaine structure de fichiers et de dossiers pour définir des cas de test avec des fichiers source de test, des paramètres facultatifs, des rendus attendus et des scripts de validation personnalisés. Par défaut, les rendus sont comparés pour l’égalité des octets. En outre, il est facile de simuler les services HTTP externes à l’aide de fichiers JSON simples.

### Ajout de tests {#add-tests}

Les tests doivent être placés dans le dossier `test` au niveau racine du projet [!DNL Adobe I/O]. Les cas de test pour chaque application doivent se trouver dans le chemin d’accès `test/asset-compute/<worker-name>`, avec un dossier pour chaque cas :

```yaml
action/
manifest.yml
package.json
...
test/
  asset-compute/
    <worker-name>/
        <testcase1>/
            file.jpg
            params.json
            rendition.png
        <testcase2>/
            file.jpg
            params.json
            rendition.gif
            validate
        <testcase3>/
            file.jpg
            params.json
            rendition.png
            mock-adobe.com.json
            mock-console.adobe.io.json
```

Consultez les [exemples d’applications personnalisées](https://github.com/adobe/asset-compute-example-workers/) pour en savoir plus. Vous trouverez ci-dessous une référence détaillée.

### Test de la sortie {#test-output}

Les résultats de test détaillés, y compris les journaux de l’application personnalisée, sont disponibles dans le dossier `build` situé à la racine de l’application Firefly, comme le montre la sortie `aio app test`.

### Simulation des services externes {#mock-external-services}

Il est possible de simuler des appels de service externes dans vos actions en définissant des fichiers `mock-<HOST_NAME>.json` dans vos cas de test, HOST_NAME étant l’hôte que vous souhaitez simuler. Un exemple d’utilisation est une application qui effectue un appel distinct à S3. La nouvelle structure de test pourrait ressembler à ceci :

```json
test/
  asset-compute/
    <worker-name>/
      <testcase3>/
        file.jpg
        params.json
        rendition.png
        mock-<HOST_NAME1>.json
        mock-<HOST_NAME2>.json
```

Le fichier de simulation est une réponse http au format JSON. Pour plus d’informations, consultez [cette documentation](https://www.mock-server.com/mock_server/creating_expectations.html). S’il existe plusieurs noms d’hôte à simuler, définissez plusieurs fichiers `mock-<mocked-host>.json`. Un exemple de fichier de simulation de `google.com` nommé `mock-google.com.json` est proposé ci-dessous :

```json
[{
    "httpRequest": {
        "path": "/images/hello.txt"
        "method": "GET"
    },
    "httpResponse": {
        "statusCode": 200,
        "body": {
          "message": "hello world!"
        }
    }
}]
```

L’exemple `worker-animal-pictures` contient un [fichier de simulation](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/test/asset-compute/worker-animal-pictures/simple-test/mock-upload.wikimedia.org.json) pour le service Wikimedia avec lequel il interagit.

#### Partage de fichiers pour les cas de test {#share-files-across-test-cases}

Il est recommandé d’utiliser des liens symboliques relatifs si vous partagez des scripts `file.*`, `params.json` ou `validate` pour différents tests. Ils sont pris en charge avec git. Veillez à attribuer un nom unique à vos fichiers partagés, car vous pouvez en avoir plusieurs. Dans l’exemple ci-dessous, les tests combinent quelques fichiers partagés, et les leurs :

```json
tests/
    file-one.jpg
    params-resize.json
    params-crop.json
    validate-image-compare

    jpg-png-resize/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-resize.json
        rendition.png
        validate    - symlink: ../validate-image-compare

    jpg2-png-crop/
        file.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate    - symlink: ../validate-image-compare

    jpg-gif-crop/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate
```

### Test des erreurs attendues {#test-unexpected-errors}

Les cas de tests d’erreur ne doivent pas contenir de fichier `rendition.*` attendu et ont à définir la valeur `errorReason` attendue dans le fichier `params.json`.

Structure des cas de tests d’erreur :

```json
<error_test_case>/
    file.jpg
    params.json
```

Fichier de paramètres avec la raison de l’erreur :

```javascript
{
    "errorReason": "SourceUnsupported",
    // ... other params
}
```

Voir la liste complète et la description des [raisons des erreurs d’Asset Compute](https://github.com/adobe/asset-compute-commons#error-reasons).

## Débogage d’une application personnalisée {#debug-custom-worker}

Les étapes ci-dessous montrent comment déboguer votre application personnalisée à l’aide de Visual Studio Code. Il permet d’afficher les journaux en direct, d’atteindre des points d’arrêt, de parcourir le code, mais aussi de charger à nouveau en direct des modifications du code local à chaque activation.

La plupart de ces étapes sont généralement automatisées d’usine par `aio` ; voir la section Débogage de l’application dans la [documentation de Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md). Pour le moment, les étapes ci-dessous comportent une solution de contournement.

1. Installez la dernière version de [wskdebug](https://github.com/apache/openwhisk-wskdebug) depuis GitHub et, facultativement, [ngrok](https://www.npmjs.com/package/ngrok).

   ```shell
   npm install -g @openwhisk/wskdebug
   npm install -g ngrok --unsafe-perm=true
   ```

1. Ajoutez à votre fichier JSON de paramètres utilisateur. Il continue à utiliser l’ancien débogueur VS Code, le nouveau a [quelques problèmes](https://github.com/apache/openwhisk-wskdebug/issues/74) avec wskdebug : `"debug.javascript.usePreview": false`.
1. Fermez toutes les instances d’applications ouvertes via `aio app run`.
1. Déployez le code le plus récent à l’aide de `aio app deploy`.
1. Exécutez uniquement l’outil Asset Compute Devtool avec `npx adobe-asset-compute devtool`. Gardez-le ouvert.
1. Dans l’éditeur VS Code, ajoutez la configuration de débogage suivante à votre `launch.json` :

   ```json
   {
     "type": "node",
     "request": "launch",
     "name": "wskdebug worker",
     "runtimeExecutable": "wskdebug",
     "args": [
       "PROJECT-0.0.1/__secured_worker",           // Replace this with your ACTION NAME
       "${workspaceFolder}/actions/worker/index.js",
       "-l",
       "--ngrok"
     ],
     "localRoot": "${workspaceFolder}",
     "remoteRoot": "/code",
     "outputCapture": "std",
     "timeout": 30000
   }
   ```

   Récupérez le NOM DE L’ACTION à partir de la sortie de `aio app deploy`. Il apparaît sous la forme `Your deployed actions -> TypicalCoffeeCat-0.0.1/__secured_worker`.

1. Sélectionnez `wskdebug worker` dans la configuration d’exécution/de débogage et appuyez sur l’icône de lecture. Patientez jusqu’au démarrage et à l’affichage de la mention **[!UICONTROL Prêt pour les activations]** dans la fenêtre **[!UICONTROL Console de débogage]**.

1. Cliquez sur **[!UICONTROL Exécuter]** dans l’outil Devtool. Vous pouvez voir les actions s’exécuter dans l’éditeur VS Code et les journaux commencer à s’afficher.

1. Définissez un point d’arrêt dans votre code et lancez à nouveau l’exécution. Le point d’arrêt devrait ensuite être atteint.

Toutes les modifications de code sont chargées en temps réel et prennent effet dès que l’activation suivante se produit.

>[!NOTE]
>
>Deux activations sont présentes pour chaque requête dans les applications personnalisées. La première requête est une action web qui s’appelle elle-même de manière asynchrone dans le code du SDK. La seconde activation est celle qui atteint votre code.
