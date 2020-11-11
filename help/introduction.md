---
title: Introduction à  [!DNL Asset Compute Service].
description: '[!DNL Asset Compute Service] est un service de traitement des ressources natif dans le cloud destiné à réduire la complexité et à améliorer l’évolutivité.'
translation-type: tm+mt
source-git-commit: 79630efa8cee2c8919d11e9bb3c14ee4ef54d0f3
workflow-type: tm+mt
source-wordcount: '321'
ht-degree: 90%

---


# Présentation d’[!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] est un service évolutif et extensible d’[!DNL Adobe Experience Cloud] destiné à traiter des ressources numériques. Il permet d’obtenir différents rendus à partir des images, des vidéos, des documents et d’autres formats de fichiers, notamment sous forme de miniatures, de texte extrait, de métadonnées et d’archives.

Les développeurs peuvent ainsi ajouter des applications de ressources personnalisées (également appelées programmes de travail personnalisés) pour traiter les cas d’utilisation spécifiques. Le service fonctionne sur [!DNL Adobe I/O] Runtime. Il peut être étendu à l’aide d’applications [!DNL Project Firefly] sans interface utilisateur graphique écrites dans Node.js. Il peut s’agir d’opérations personnalisées telles que des appels à des API externes pour effectuer des opérations sur des images ou pour bénéficier de la prise en charge d’[!DNL Adobe Sensei].

[!DNL Project Firefly] est un framework destiné à créer et déployer des applications web personnalisées sur [!DNL Adobe I/O] Runtime pour étendre les solutions Adobe Experience Cloud. Pour créer des applications personnalisées, les développeurs peuvent tirer parti de [!DNL React Spectrum] (boîte à outils d’interface utilisateur d’Adobe), créer des microservices et des événements personnalisés, et orchestrer les API. Voir la [documentation de Project Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

>[!NOTE]
>
>Actuellement, [!DNL Asset Compute Service] ne peut être utilisé que par [!DNL Experience Manager] as a Cloud Service. Les administrateurs créent des profils de traitement qui peuvent appeler [!DNL Asset Compute Service] pour transmettre des ressources à des fins de traitement. Voir [Utilisation des microservices de ressources et des profils de traitement](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html).

## Cas d’utilisation pris en charge par [!DNL Asset Compute Service] {#possible-use-cases-benefits}

[!DNL Asset Compute Service] prend en charge quelques cas d’utilisation métier courants, tels que le traitement de base d’images, les conversions spécifiques aux applications d’Adobe et la création d’applications personnalisées qui orchestrent des besoins métier complexes.

Vous pouvez utiliser le service web [!DNL Asset Compute] afin de générer des miniatures pour différents types de fichiers, ainsi que des rendus d’image de haute qualité pour les [formats de fichiers pris en charge](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/file-format-support.html). Voir [Cas d’utilisation pris en charge par le biais d’une configuration personnalisée](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html).

>[!NOTE]
>
>Le service n’assure pas le stockage des ressources. Les utilisateurs le fournissent et donnent des références aux emplacements de fichiers source et de rendu dans l’espace de stockage dans le cloud.

<!-- TBD: Should this be mentioned in the docs?

|Asset Compute Service does not do this|Expectations from implementing client|
|---|---|
| Binary uploads or API-based asset ingestion. | Use other methods to ingest assets. |
| Store binaries or any persisted data across processing requests.| Each request is independent so treat it as a standalone request by sharing binary and processing instructions. |
| Store any configurations such as processing rules or settings for a user or an organization's account. | Add processing request to each request/instruction. |
| Direct event handling of asset creation events from storage systems and processing completed notifications, and errors. | Use Adobe I/O Events and other methods. |

-->

>[!MORELIKETHIS]
>
>* [Présentation du traitement des ressources à l’aide de microservices de ressources dans Adobe Experience Manager as a Cloud Service](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/asset-microservices-overview.html).
>* [Documentation de Project Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).
>* [Formats de fichiers pris en charge pour le traitement](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/file-format-support.html).
>* [Notes de mise à jour d’Asset Compute Service](release-notes.md)


<!-- **TBD:**
* Clarify the service can only be used within AEM as Cloud Service. The docs provided as context for custom application developers. Not to be used as a standalone service.
  ** and API as that plays a role in custom applications (accepting standard params, invoking Nui itself in the future, etc. (this is an outlook))

* link to aem as cloud service docs on asset ingestion and customization with processing profiles.
-->
