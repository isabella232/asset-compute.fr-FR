---
title: Déploiement d’une application personnalisée  [!DNL Asset Compute Service]
description: Déployer une application personnalisée  [!DNL Asset Compute Service] .
exl-id: a68d4f59-8a8f-43b2-8bc6-19320ac8c9ef
source-git-commit: 129651ba432b75703bc27baa7081da60302f828d
workflow-type: ht
source-wordcount: '184'
ht-degree: 100%

---

# Déploiement d’une application personnalisée {#deploy-custom-application}

Pour déployer votre application, utilisez la commande [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy). Dans le terminal, la commande affiche une URL pour accéder à l’application personnalisée. L’URL est au format `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Pour obtenir la même URL sans redéployer l’application, utilisez la commande [`aio app get-url`](https://github.com/adobe/aio-cli#aio-app-get-url-action).

Utilisez l’URL dans un [profil de traitement d’ [!DNL Experience Manager]  as a  [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html?lang=fr) pour intégrer votre application avec [!DNL Experience Manager] as a [!DNL Cloud Service].

Assurez-vous que votre projet App Builder et votre espace de travail correspondent à l’environnement [!DNL Experience Manager] as a [!DNL Cloud Service] où vous voulez utiliser votre action. Il dispose de différents environnements de développement, d’évaluation et de production. Vous pouvez vérifier l’environnement en vérifiant les informations d’identification `AIO_runtime_*` définies dans votre fichier ENV dans la racine de votre application Firefly. Par exemple, pour effectuer un déploiement dans un espace de travail `Stage`, le format de l’`AIO_runtime_namespace` est `xxxxxx_xxxxxxxxx_stage`. Pour l’intégrer avec l’environnement de production [!DNL Experience Manager] as a [!DNL Cloud Service], utilisez les URL d’application de votre espace de travail `Production` Firefly.

>[!CAUTION]
>
>N’utilisez pas un espace de travail personnel dans les environnements [!DNL Experience Manager] critiques.

>[!MORELIKETHIS]
>
>* [Comprendre et gérer les environnements dans  [!DNL Experience Manager]  as a  [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html?lang=fr).

