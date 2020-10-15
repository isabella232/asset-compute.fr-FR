---
title: Notes de mise à jour de [!DNL Asset Compute Service].
description: Nouvelles fonctionnalités, améliorations et problèmes connus dans [!DNL Asset Compute Service].
translation-type: tm+mt
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: tm+mt
source-wordcount: '193'
ht-degree: 2%

---


# Notes de mise à jour de [!DNL Asset Compute Service] {#release-notes}

La dernière version de [!DNL Asset Compute Service] est sortie le 30 juillet 2020.

<!--

To test your custom applications with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). Currently, Adobe supports Azure Blob Storage and AWS S3.

>[!NOTE]
>
>Cloud storage access is only required for using the developer tool. You can still create, test and deploy custom applications with out using the developer tool.
-->

## What is new {#what-is-new}

Il s&#39;agit de la première version de [!DNL Asset Compute Service]. Il s’agit d’un service évolutif et extensible de [!DNL Adobe Experience Cloud] traitement des ressources numériques. Il peut transformer des formats d’image, de vidéo, de document et d’autres formats de fichier en différents rendus, y compris des miniatures, du texte extrait, des métadonnées et des archives.

Actuellement, le [!DNL Asset Compute Service] peut uniquement être utilisé en [!DNL Experience Manager] tant que Cloud Service.

## Limites et problèmes connus {#known-limitations}

Pour tester votre application personnalisée à l’aide de l’outil [de](https://github.com/adobe/asset-compute-devtool)développement, vous devez accéder à un conteneur [d’enregistrement](https://github.com/adobe/asset-compute-devtool#prerequisites)cloud.

* L’accès à l’enregistrement de cloud (différent de celui de la [!DNL Experience Manager] banque de données blob) n’est nécessaire que pour l’outil de développement. Vous pouvez toujours créer, tester et déployer des applications personnalisées sans l’outil de développement.
* Il peut s’agir d’un conteneur partagé utilisé par plusieurs développeurs dans différents projets.

## Contribute {#contribute-open-source}

[!DNL Asset Compute Service] l&#39;extensibilité est développée sous un modèle de développement ouvert sur [github.com/adobe](https://github.com/adobe) qui accueille les contributions des développeurs d&#39;extensions. Tous les composants pertinents pour le développement, la création, le test et le déploiement d&#39;applications personnalisées sont open source. Voir [comment et où contribuer au service](contribute-to-compute-service.md)informatique.

<!-- **TBD:**
* Are we versioning the releases?
* Is there any compatibility information to be added? With Project Firefly versions, or AEMaaCS releases, or other offerings/integrations such as InDesign Server?
-->
