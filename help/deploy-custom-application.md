---
title: Déploiement d’une application personnalisée  [!DNL Asset Compute Service] .
description: Déploiement d’une application personnalisée  [!DNL Asset Compute Service] .
translation-type: ht
source-git-commit: 79630efa8cee2c8919d11e9bb3c14ee4ef54d0f3
workflow-type: ht
source-wordcount: '197'
ht-degree: 100%

---


# Déploiement d’une application personnalisée {#deploy-custom-application}

Pour déployer votre application, utilisez la commande [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy). Dans le terminal, la commande affiche une URL pour accéder à l’application personnalisée. L’URL est au format `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Pour obtenir la même URL sans redéployer l’application, utilisez la commande [`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action).

Utilisez l’URL dans un [Profil de traitement dans Experience Manager as a Cloud Service](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html) pour intégrer votre application avec [!DNL Experience Manager] as a Cloud Service.

Veillez à ce que votre projet et votre espace de travail Firefly correspondent à l’environnement [!DNL Experience Manager] as a Cloud Service dans lequel vous souhaitez utiliser votre action. Il dispose de différents environnements de développement, d’évaluation et de production. Vous pouvez vérifier l’environnement en vérifiant les informations d’identification `AIO_runtime_*` définies dans votre fichier ENV dans la racine de votre application Firefly. Par exemple, pour effectuer un déploiement dans un espace de travail `Stage`, le format de l’`AIO_runtime_namespace` est `xxxxxx_xxxxxxxxx_stage`. Pour l’intégrer avec l’environnement de production [!DNL Experience Manager] as a Cloud Service, utilisez les URL d’application de votre espace de travail `Production` Firefly.

>[!CAUTION]
>
>N’utilisez pas un espace de travail personnel dans les environnements [!DNL Experience Manager] critiques.

>[!MORELIKETHIS]
>
>* [Comprendre et gérer les environnements dans Experience Manager as a Cloud Service](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html).

