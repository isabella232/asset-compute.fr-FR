---
title: Notes de mise à jour d’ [!DNL Asset Compute Service].
description: Nouvelles fonctionnalités, améliorations et problèmes connus dans  [!DNL Asset Compute Service].
translation-type: tm+mt
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: tm+mt
source-wordcount: '193'
ht-degree: 100%

---


# Notes de mise à jour d’[!DNL Asset Compute Service] {#release-notes}

La dernière version d’[!DNL Asset Compute Service] est sortie le 30 juillet 2020.

<!--

To test your custom applications with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). Currently, Adobe supports Azure Blob Storage and AWS S3.

>[!NOTE]
>
>Cloud storage access is only required for using the developer tool. You can still create, test and deploy custom applications with out using the developer tool.
-->

## Nouveautés {#what-is-new}

Voici la première version d’[!DNL Asset Compute Service]. Il s’agit d’un service évolutif et extensible d’[!DNL Adobe Experience Cloud] destiné à traiter des ressources numériques. Il permet d’obtenir différents rendus à partir des images, des vidéos, des documents et d’autres formats de fichiers, notamment sous forme de miniatures, de texte extrait, de métadonnées et d’archives.

Actuellement, [!DNL Asset Compute Service] ne peut être utilisé que par [!DNL Experience Manager] as a Cloud Service.

## Limites et problèmes connus {#known-limitations}

Pour tester votre application personnalisée à l’aide de l’[outil de développement](https://github.com/adobe/asset-compute-devtool), vous devez accéder à un [conteneur de stockage dans le cloud](https://github.com/adobe/asset-compute-devtool#prerequisites).

* L’accès à l’espace de stockage dans le cloud (différent de celui du blob store d’[!DNL Experience Manager]) n’est nécessaire que pour l’outil de développement. Vous pouvez tout de même créer, tester et déployer des applications personnalisées sans l’outil de développement.
* Il peut s’agir d’un conteneur partagé utilisé par plusieurs développeurs dans différents projets.

## Contribution {#contribute-open-source}

L’extensibilité d’[!DNL Asset Compute Service] est développée dans le cadre d’un modèle de développement ouvert sur [github.com/adobe](https://github.com/adobe) qui accueille les contributions des développeurs d’extensions. Tous les composants pertinents pour le développement, la création, le test et le déploiement d’applications personnalisées sont open source. Voir [Comment et où contribuer à Compute Service](contribute-to-compute-service.md).

<!-- **TBD:**
* Are we versioning the releases?
* Is there any compatibility information to be added? With Project Firefly versions, or AEMaaCS releases, or other offerings/integrations such as InDesign Server?
-->
