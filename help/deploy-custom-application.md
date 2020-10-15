---
title: Application [!DNL Asset Compute Service] Deploycustom.
description: Application [!DNL Asset Compute Service] Deploycustom.
translation-type: tm+mt
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: tm+mt
source-wordcount: '201'
ht-degree: 8%

---


# Déploiement d’une application personnalisée {#deploy-custom-application}

Pour déployer votre application, utilisez la commande [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy) . Dans le terminal, la commande affiche une URL permettant d’accéder à l’application personnalisée. L’URL est au format `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Pour obtenir la même URL sans redéployer l’application, utilisez [`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action) la commande.

Utilisez l’URL dans un Profil de [traitement du Experience Manager en tant que Cloud Service](https://docs.adobe.com/content/help/fr-FR/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html) pour intégrer votre application [!DNL Experience Manager] en tant que Cloud Service.

Assurez-vous que votre projet et votre espace de travail Firefly correspondent à l’environnement [!DNL Experience Manager] en tant qu’Cloud Service dans lequel vous souhaitez utiliser votre action. Il a différents environnements de développement, de mise en scène et de production. Vous pouvez vérifier l’environnement en vérifiant `AIO_runtime_*` les informations d’identification définies dans votre fichier ENV à la racine de votre application Firefly. Par exemple, pour effectuer un déploiement sur un `Stage` espace de travail, le `AIO_runtime_namespace` format est `xxxxxx_xxxxxxxxx_stage`. Pour l’intégrer [!DNL Experience Manager] en tant qu’environnement de production Cloud Service, utilisez les URL d’application de votre `Production` espace de travail Firefly.

>[!CAUTION]
>
>N’utilisez pas d’espace de travail personnel sur les [!DNL Experience Manager] environnements critiques.

>[!MORELIKETHIS]
>
>* [Comprendre et gérer les environnements en Experience Manager en tant que Cloud Service](https://docs.adobe.com/content/help/fr-FR/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html).

