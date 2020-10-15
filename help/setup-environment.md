---
title: Définissez l’environnement de développement requis pour [!DNL Asset Compute Service].
description: Configuration de l’environnement de développement [!DNL Asset Compute Service] pour la création et le test de débuts personnalisés.
translation-type: tm+mt
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: tm+mt
source-wordcount: '376'
ht-degree: 1%

---


# Configuration d’un environnement développeur {#create-dev-environment}

Pour créer une configuration qui vous permet de développer pour [!DNL Asset Compute Service], suivez ces instructions et exigences.

1. [Obtenez l’accès et les informations d’identification](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials) pour Project Firefly.

1. [Configurez l’environnement](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#local-environment-set-up) local et les outils nécessaires.

1. Voici d&#39;autres outils qui vous aideront à vous développer en douceur :

   * [Git](https://git-scm.com/).
   * [Docker Desktop](https://www.docker.com/get-started).
   * [NodeJS](https://nodejs.org) (LTS v10 à v12, les versions impaires ne sont pas recommandées) et [NPM](https://www.npmjs.com). L&#39;utilisateur d&#39;OSX HomeBrew peut effectuer `brew install node` les deux opérations. Sinon, téléchargez-le à partir de la page [de téléchargement de](https://nodejs.org/en/)NodeJS.
   * Un IDE qui convient à NodeJS, nous recommandons [Visual Studio Code (VS Code)](https://code.visualstudio.com) car il s&#39;agit de l&#39;IDE pris en charge pour le débogueur. Vous pouvez utiliser n&#39;importe quel autre IDE en tant qu&#39;éditeur de code, mais une utilisation avancée (débogueur, par exemple) n&#39;est pas encore prise en charge.
   * [AIO CLI](https://github.com/adobe/aio-cli) (`aio`) - installation à l’aide `npm install -g @adobe/aio-cli`.

1. Veillez à respecter les [conditions préalables](/help/understand-extensibility.md#prerequisites-and-provisioning).

## Configuration d’un projet Firefly {#create-firefly-project}

1. Accédez au rôle Administrateur système ou Développeur dans l’organisation de l’expérience. Ceci peut être défini par un administrateur système dans le [Admin Console](https://adminconsole.adobe.com/overview).

1. Connectez-vous à la Console [développeur](https://console.adobe.io/)Adobe. Assurez-vous que vous faites partie de la même organisation Adobe Experience Cloud que l’AEM en tant qu’intégration Cloud Service. Pour plus d&#39;informations sur Adobe Developer Console, consultez la documentation [de](https://www.adobe.io/apis/experienceplatform/console/docs.html)la Console.

1. [Créez un projet](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md)Firefly. Cliquez sur **[!UICONTROL Créer un projet]** > **[!UICONTROL Projet à partir d’un modèle]**. Sélectionnez luciole. Il crée un projet Firefly avec deux espaces de travail : `Production` et `Stage`. Ajoutez des espaces de travail supplémentaires, par exemple `Development`, selon les besoins.

1. Dans le projet Firefly, sélectionnez un espace de travail et abonnez-vous aux services requis pour Asset Compute. Cliquez sur **Ajouter au projet** > **API** et ajoutez `Asset Compute`, `IO Events`et `IO Events Management` des services. Lors de l’ajout de la première API, elle invite à créer une clé privée. Enregistrez ces informations sur votre ordinateur lorsque vous en aurez besoin pour tester votre application personnalisée à l’aide de l’outil de développement.

## Etape suivante {#next-step}

Maintenant que votre environnement est configuré, vous êtes prêt à [créer une application](develop-custom-application.md)personnalisée.

<!-- TBD items for later:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)
-->
