---
title: Résolution des problèmes [!DNL Asset Compute Service].
description: Dépannez et déboguez les applications personnalisées à l'aide [!DNL Asset Compute Service]de.
translation-type: tm+mt
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: tm+mt
source-wordcount: '306'
ht-degree: 1%

---


# Résolution des problèmes {#troubleshoot}

Voici quelques conseils de dépannage génériques qui peuvent vous aider à résoudre les problèmes liés à Asset Compute Service :

* Assurez-vous que l’application JavaScript ne se bloque pas au démarrage. Ces blocages sont généralement liés à une bibliothèque manquante ou à une dépendance.
* Assurez-vous que toutes les dépendances à installer sont référencées dans le `package.json` fichier de l’application.
* Assurez-vous que les erreurs qui peuvent provenir du nettoyage en cas d’échec ne génèrent pas leurs propres erreurs qui masquent le problème d’origine.

* Lors du démarrage de l’outil de développement pour la première fois avec une nouvelle [!DNL Asset Compute Service] intégration, il se peut que la première demande de traitement échoue car le Journal de Événements de calcul des ressources n’est pas entièrement configuré. Patientez un certain temps avant que le journal ne soit configuré avant d’envoyer une autre demande.
* Si vous rencontrez des erreurs lors de l’envoi de calculs de ressources `/register` ou de `/process` demandes, assurez-vous que toutes les API nécessaires sont ajoutées au projet et à l’espace de travail d’E/S de l’Adobe, c’est-à-dire au calcul des ressources, aux Événements d’E/S, à la gestion des Événements d’E/S et à l’exécution.

## Connexion aux problèmes via l&#39;interface de ligne de commande des E/S des Adobes {#login-via-aio-cli}

Si vous rencontrez des problèmes lors de la connexion à l’interface de ligne de commande [!DNL Adobe Developer Console] des E/S par [](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli)le biais de l’Adobe, ajoutez manuellement les informations d’identification requises pour le développement, le test et le déploiement de votre application personnalisée :

1. Accédez à votre projet et à votre espace de travail Firefly sur la console [de développement des](https://console.adobe.io/) Adobes et appuyez sur **[!UICONTROL Télécharger]** dans le coin supérieur droit. Ouvrez ce fichier et enregistrez ce fichier JSON à un emplacement sécurisé sur votre ordinateur.

1. Accédez au fichier ENV de votre application de luciole.

1. Ajoutez les informations d’identification Adobe I/O Runtime. Récupérez les informations d’identification Adobe I/O Runtime à partir du fichier JSON téléchargé. The credentials are under `project.workspace.services.runtime`. Ajoutez les informations d&#39;identification d&#39;exécution d&#39;E/S dans les `AIO_runtime_XXX` variables :

   ```json
   AIO_runtime_auth=
   AIO_runtime_namespace=
   ```

1. Ajoutez le chemin d’accès absolu au fichier JSON téléchargé à l’étape 1 :

   ```json
       ASSET_COMPUTE_INTEGRATION_FILE_PATH=
   ```

1. Configurez le reste des informations d’identification [](develop-custom-application.md) requises pour l’outil de développement.

<!-- TBD for later:
Add any best practices for developers in this section:
* Any items to take care of when creating projects.
* Any naming conventions, reserved keywords, etc.?
* Any terms that can become a source of confusion later based on our OOTB naming.

* If required, add limitations for custom applications and spin those off as best practices.
* Do NOT borrow any content from https://git.corp.adobe.com/nui/nui/blob/master/doc/worker_api.md. It is outdated and irrelevant for 3rd party custom applications.
-->
