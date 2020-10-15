---
title: Introduction aux [!DNL Asset Compute Service].
description: '[!DNL Asset Compute Service] est un service de traitement des ressources natif du cloud qui réduit la complexité et améliore l’évolutivité.'
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '332'
ht-degree: 2%

---


# Présentation de [!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] est un service évolutif et extensible de [!DNL Adobe Experience Cloud] traitement des ressources numériques. Il peut transformer des formats d’image, de vidéo, de document et d’autres formats de fichier en différents rendus, y compris des miniatures, du texte extrait, des métadonnées et des archives.

Les développeurs peuvent ajouter des applications de ressources personnalisées (également appelées travailleurs personnalisés) pour traiter les cas d’utilisation personnalisée. Le service fonctionne sur l’ [!DNL Adobe I/O] exécution. Il peut être étendu via des applications [!DNL Project Firefly] sans tête écrites dans Node.js. Il peut s’agir d’opérations personnalisées telles que l’appel d’API externes pour effectuer des opérations d’image ou pour tirer parti de la prise en charge [!DNL Adobe Sensei] .

[!DNL Project Firefly] est une structure permettant de créer et de déployer des applications Web personnalisées lors de l’ [!DNL Adobe I/O] exécution pour étendre les solutions Adobe Experience Cloud. Pour créer des applications personnalisées, les développeurs peuvent tirer parti [!DNL React Spectrum] (kit d’outils de l’interface utilisateur de l’Adobe), créer des microservices, créer des événements personnalisés et orchestrer les API. Voir la [documentation du projet Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

>[!NOTE]
>
>Actuellement, l’ [!DNL Asset Compute Service] outil ne peut être utilisé que par [!DNL Experience Manager] le biais d’un Cloud Service. Les administrateurs créent des profils de traitement qui peuvent appeler le [!DNL Asset Compute Service] pour transmettre des ressources à des fins de traitement. Voir [Utilisation de microservices de ressources et de profils](https://docs.adobe.com/content/help/fr-FR/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)de traitement.

## Cas d’utilisation pris en charge de [!DNL Asset Compute Service] {#possible-use-cases-benefits}

[!DNL Asset Compute Service] prend en charge quelques cas d&#39;utilisation commerciale courants, tels que le traitement d&#39;images de base ; les conversions spécifiques aux applications d&#39;Adobe ; création d&#39;applications personnalisées qui orchestrent des besoins métiers complexes.

Vous pouvez utiliser [!DNL Asset Compute] le service Web pour générer des miniatures pour différents types de fichiers, des rendus d’image de haute qualité pour les formats [de fichiers](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html)pris en charge. Voir les cas [d’utilisation pris en charge par le biais d’une configuration](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html#custom-config)personnalisée.

>[!NOTE]
>
>Le service ne fournit pas d’enregistrement de ressources. Les utilisateurs le fournissent et fournissent des références aux emplacements de fichiers source et de rendu dans l’enregistrement cloud.

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
>* [Présentation du traitement des fichiers à l’aide de microservices de ressources à Adobe Experience Manager en tant que Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html).
>* [Documentation du projet Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).
>* [Formats de fichier pris en charge pour le traitement](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html).
>* [Notes de mise à jour du service Asset Compute](release-notes.md)


<!-- **TBD:**
* Clarify the service can only be used within AEM as Cloud Service. The docs provided as context for custom application developers. Not to be used as a standalone service.
  ** and API as that plays a role in custom applications (accepting standard params, invoking Nui itself in the future, etc. (this is an outlook))

* link to aem as cloud service docs on asset ingestion and customization with processing profiles.
-->
