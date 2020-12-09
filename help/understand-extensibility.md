---
title: Comprendre l’extension d’ [!DNL Asset Compute Service].
description: Quand et comment étendre les fonctionnalités d’ [!DNL Asset Compute Service]  pour effectuer un traitement personnalisé des ressources.
translation-type: ht
source-git-commit: c392b8588929f7b13db13e42a3f17bbc4f68a376
workflow-type: ht
source-wordcount: '265'
ht-degree: 100%

---


# Introduction à l’extensibilité {#introduction-to-extensibilty}

De nombreuses exigences de rendu, comme la conversion des formats et le redimensionnement des images, sont prises en charge par les [Profils de traitement dans  [!DNL Experience Manager]  as a  [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/asset-microservices-overview.html?lang=fr). Les exigences plus complexes peuvent nécessiter une solution personnalisée adaptée aux besoins d’une entreprise. Il est ainsi possible d’étendre [!DNL Asset Compute Service] en créant des applications personnalisées appelées à partir de profils de traitement dans [!DNL Experience Manager]. Ces applications personnalisées s’adaptent aux [cas d’utilisation pris en charge](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html?lang=fr).

>[!NOTE]
>
>[!DNL Asset Compute Service] est disponible uniquement pour une utilisation avec [!DNL Experience Manager] as a [!DNL Cloud Service].

Les applications personnalisées sont des applications [Project Firefly](https://github.com/AdobeDocs/project-firefly) sans interface utilisateur graphique. Il est plus simple d’étendre [!DNL Asset Compute Service] à l’aide d’applications personnalisées grâce au [SDK Asset Compute](https://github.com/adobe/asset-compute-sdk) et à l’outil de développement Project Firefly. Les développeurs peuvent ainsi se consacrer essentiellement à la logique commerciale. La création d’applications personnalisées est aussi simple que la création d’une action Adobe I/O Runtime sans serveur de type ordinaire. Il s’agit d’une fonction JavaScript Node.js unique. L’[exemple d’application personnalisée de base](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) en est une illustration.

## Conditions préalables et exigences de configuration {#prerequisites-and-provisioning}

Veillez à respecter les conditions préalables suivantes :

* Les outils Project Firefly sont installés sur votre machine.
* Une organisation [!DNL Experience Cloud]. Plus d’informations [ici](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials).
* [!DNL Experience Manager] as a [!DNL Cloud Service] doit être activé pour l’organisation Experience.
* L’organisation [!DNL Adobe Experience Cloud] fait partie du programme de préversion pour les développeurs [!DNL Project Firefly]. Voir [Comment demander l’accès](https://github.com/AdobeDocs/project-firefly/blob/master/overview/getting_access.md).
* Veillez à ce que le développeur ait un rôle de développeur ou des autorisations d’administrateur au sein de l’organisation.
* Veillez aussi à ce que l’[Adobe I/O CLI](https://github.com/adobe/aio-cli) soit installée localement.

<!-- TBD for later:

* What all accesses and licenses are required?
* What all permissions are required to create, debug, and deploy custom applications?
* How do developers get access and provision the required apps?
* What is repository management?
* Anything on security and data transfer?
* What about handling personal or sensitive information?
* Custom application SLA is dependent on SLAs of various services it depends on.
* Document how the devs can get to know the KPIs of their custom applications. The KPIs are dependent on the performance at Adobe's side, amongst other things.
-->
