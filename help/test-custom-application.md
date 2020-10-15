---
title: Testez et [!DNL Asset Compute Service] déboguez une application personnalisée.
description: Testez et [!DNL Asset Compute Service] déboguez une application personnalisée.
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '788'
ht-degree: 0%

---


# Test et débogage d’une application personnalisée {#test-debug-custom-worker}

## Exécuter des tests unitaires pour une application personnalisée {#test-custom-worker}

Installez [Docker Desktop](https://www.docker.com/get-started) sur votre ordinateur. Pour tester un programme de travail personnalisé, exécutez la commande suivante à la racine de l’application :

```bash
$ aio app test
```

<!-- TBD
To run tests for a custom application, run `adobe-asset-compute test-worker` command in the root of the custom application application application.

Document interactively running `adobe-asset-compute` commands `test-worker` and `run-worker`.
-->

Ceci exécute une structure de test unitaire personnalisée pour les actions de l’application Asset Compute dans le projet comme décrit ci-dessous. Il est connecté via une configuration dans le `package.json` fichier. Il est également possible d’effectuer des tests unitaires JavaScript tels que Jest. `aio app test` exécute les deux.

Le plug-in [aio-cli-plugin-asset-compute](https://github.com/adobe/aio-cli-plugin-asset-compute#install-as-local-devdependency) est incorporé en tant que dépendance au développement dans l’application d’application personnalisée afin qu’il ne soit pas nécessaire de l’installer sur les systèmes de génération/test.

### Structure de test d&#39;unité d&#39;application {#unit-test-framework}

La structure de test d&#39;unité d&#39;application Asset Compute permet de tester les applications sans écrire de code. Il repose sur le principe du fichier de rendu source pour les applications. Une certaine structure de fichiers et de dossiers doit être configurée pour définir des cas de test avec des fichiers source de test, des paramètres facultatifs, des rendus attendus et des scripts de validation personnalisés. Par défaut, les rendus sont comparés pour l’égalité des octets. En outre, les services HTTP externes peuvent être facilement moqués à l’aide de fichiers JSON simples.

### Tests d&#39;Ajoute {#add-tests}

Les tests sont attendus dans le `test` dossier au niveau racine du projet AIO. Les cas de test pour chaque application doivent se trouver dans le chemin d’accès `test/asset-compute/<worker-name>`, avec un dossier pour chaque cas de test :

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

Consultez des [exemples d’applications](https://github.com/adobe/asset-compute-example-workers/) personnalisées pour en savoir plus. Vous trouverez ci-dessous une référence détaillée.

### Test output {#test-output}

Les résultats de test détaillés, y compris les journaux de l’application personnalisée, sont disponibles dans le `build` dossier situé à la racine de l’application Firefly, comme le montre la `aio app test` sortie.

### Services externes fictifs {#mock-external-services}

Il est possible de simuler des appels de service externes dans vos actions en définissant `mock-<HOST_NAME>.json` des fichiers dans vos cas de test, où HOST_NAME est l&#39;hôte que vous souhaitez simuler. Un exemple d’utilisation est une application qui effectue un appel distinct à S3. La nouvelle structure de test ressemblerait à ceci :

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

Le fichier fictif est une réponse http au format JSON. For more information, see [this documentation](https://www.mock-server.com/mock_server/creating_expectations.html). S’il existe plusieurs noms d’hôte à simuler, définissez plusieurs `mock-<mocked-host>.json` fichiers. Ci-dessous, un exemple de fichier fictif du `google.com` nom `mock-google.com.json`:

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

L&#39;exemple `worker-animal-pictures` contient un fichier [](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/test/asset-compute/worker-animal-pictures/simple-test/mock-upload.wikimedia.org.json) fictif pour le service Wikimedia avec lequel il interagit.

#### Partage de fichiers dans les cas de test {#share-files-across-test-cases}

Il est recommandé d’utiliser des liens symboliques relatifs si vous partagez `file.*`des scripts `params.json` ou `validate` des scripts entre plusieurs tests. Ils sont pris en charge avec git. Veillez à attribuer un nom unique à vos fichiers partagés, car vous pouvez en avoir plusieurs. Dans l’exemple ci-dessous, les tests sont en train de mélanger et de faire correspondre quelques fichiers partagés, et les leurs :

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

### Tester les erreurs attendues {#test-unexpected-errors}

Les cas de tests d’erreur ne doivent pas contenir de `rendition.*` fichier attendu et doivent définir le fichier `errorReason` attendu dans le `params.json` fichier.

Structure de cas d&#39;erreur :

```json
<error_test_case>/
    file.jpg
    params.json
```

Fichier de paramètres avec raison d’erreur :

```javascript
{
    "errorReason": "SourceUnsupported",
    // ... other params
}
```

Voir la liste complète et la description des raisons [d’erreur de calcul des](https://github.com/adobe/asset-compute-commons#error-reasons)ressources.

## Débogage d’une application personnalisée {#debug-custom-worker}

Les étapes suivantes montrent comment déboguer votre application personnalisée à l’aide du code Visual Studio. Il permet d’afficher les journaux en direct, les points d’arrêt des accès et le code d’étape par étape ainsi que le rechargement en direct des modifications de code local à chaque activation.

La plupart de ces étapes sont généralement automatisées `aio` par défaut, voir la section Débogage de l’application dans la documentation [de](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md)Firefly. Pour l&#39;instant, les étapes ci-dessous incluent une solution.

1. Installez le dernier [fichier wskdebug](https://github.com/apache/openwhisk-wskdebug) de GitHub et le [répertoire](https://www.npmjs.com/package/ngrok)facultatif.

   ```shell
   npm install -g @openwhisk/wskdebug
   npm install -g ngrok --unsafe-perm=true
   ```

1. Ajoutez votre fichier JSON de paramètres utilisateur. Il continue à utiliser l&#39;ancien débogueur de code VS, le nouveau a [quelques problèmes](https://github.com/apache/openwhisk-wskdebug/issues/74) avec wskdebug : `"debug.javascript.usePreview": false`.
1. Fermez toutes les instances d’applications ouvertes via `aio app run`.
1. Déployez le code le plus récent à l’aide de `aio app deploy`.
1. Exécutez uniquement l’outil de développement de calcul Asset à l’aide de `npx adobe-asset-compute devtool`. Gardez-le ouvert.
1. Dans l&#39;éditeur de code VS, ajoutez la configuration de débogage suivante à votre `launch.json`:

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

   Récupérez le NOM DE L&#39;ACTION à partir de la sortie de `aio app deploy`. On dirait `Your deployed actions -> TypicalCoffeeCat-0.0.1/__secured_worker`.

1. Sélectionnez `wskdebug worker` dans la configuration d&#39;exécution/débogage et appuyez sur l&#39;icône de lecture. Patientez jusqu’à ce qu’il s’affiche en début jusqu’à ce qu’il affiche **[!UICONTROL Prêt pour les activations]** dans la fenêtre de la console **[!UICONTROL de]** débogage.

1. Cliquez sur **[!UICONTROL Exécuter]** dans l&#39;outil Devtool. Vous pouvez voir les actions s&#39;exécutant dans l&#39;éditeur de code VS et le début des journaux s&#39;afficher.

1. Définissez un point d’arrêt dans votre code, exécutez à nouveau et il doit être atteint.

Toutes les modifications de code sont chargées en temps réel et prennent effet dès que la prochaine activation se produit.

>[!NOTE]
>
>Deux activations sont présentes pour chaque requête dans les applications personnalisées. La première requête est une action Web qui s’appelle de manière asynchrone dans le code du SDK. La deuxième activation est celle qui atteint votre code.
