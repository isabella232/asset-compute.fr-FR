---
title: Comprenez à propos de l'extension [!DNL Asset Compute Service].
description: Quand et comment étendre [!DNL Asset Compute Service] la fonctionnalité pour effectuer le traitement personnalisé des ressources.
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '275'
ht-degree: 3%

---


# Introduction à l&#39;extensibilité {#introduction-to-extensibilty}

De nombreuses exigences de rendu, telles que la conversion en formats et le redimensionnement des images, sont prises en charge par les Profils de [traitement [!DNL Experience Manager] en tant que Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html). Les besoins plus complexes de l&#39;entreprise peuvent nécessiter une solution personnalisée adaptée aux besoins d&#39;une entreprise. [!DNL Asset Compute Service] peut être étendu en créant des applications personnalisées appelées à partir des Profils de traitement dans [!DNL Experience Manager]. Ces applications personnalisées s’adaptent aux cas [d’utilisation](https://docs.adobe.com/content/help/fr-FR/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)pris en charge.

>[!NOTE]
>
>[!DNL Asset Compute Service] est disponible uniquement pour une utilisation avec [!DNL Experience Manager] comme Cloud Service.

Les applications personnalisées sont des applications [Project Firefly](https://github.com/AdobeDocs/project-firefly) sans en-tête. L’extension [!DNL Asset Compute Service] des applications personnalisées est rendue simple grâce au SDK [](https://github.com/adobe/asset-compute-sdk) Asset Compute et à l’outil de développement Project Firefly. Cela permet aux développeurs de se concentrer sur la logique métier. La création d&#39;applications personnalisées est aussi simple que la création d&#39;une action Adobe I/O Runtime simple sans serveur. Il s’agit d’une fonction JavaScript Node.js unique. L&#39;exemple [d&#39;application personnalisée de](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) base l&#39;illustre.

## Conditions préalables et configuration requise {#prerequisites-and-provisioning}

Assurez-vous de respecter les conditions préalables suivantes :

* Les outils Project Firefly sont installés sur votre machine.
* Une [!DNL Experience Cloud] organisation. Plus d&#39;informations [ici](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials).
* L’organisation de l’expérience doit avoir [!DNL Experience Manager] comme Cloud Service activé.
* [!DNL Adobe Experience Cloud] l&#39;organisation fait partie du programme de la prévisualisation [!DNL Project Firefly] développeur. Voir [comment demander un accès](https://github.com/AdobeDocs/project-firefly/blob/master/overview/getting_access.md).
* Assurez-vous que le développeur dispose d’un rôle de développeur ou d’autorisations d’administrateur dans l’organisation.
* Assurez-vous que l&#39;interface de ligne de commande [E/S](https://github.com/adobe/aio-cli) Adobe est installée localement.

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
