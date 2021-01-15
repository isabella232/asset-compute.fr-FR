---
title: Architecture d’ [!DNL Asset Compute Service].
description: Comment l’API, les applications et le SDK  [!DNL Asset Compute Service]  fonctionnent ensemble pour fournir un service de traitement des ressources natif dans le cloud.
translation-type: tm+mt
source-git-commit: d26ae470507e187249a472ececf5f08d803a636c
workflow-type: tm+mt
source-wordcount: '485'
ht-degree: 82%

---


# Architecture d’[!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] est construit sur la plate-forme d&#39;exécution [!DNL Adobe I/O] sans serveur. Il permet la prise en charge des ressources par les services de contenu Adobe Sensei. Le client appelant (uniquement si [!DNL Experience Manager] as a [!DNL Cloud Service] est pris en charge) reçoit les informations générées par Adobe Sensei qu’il a recherchées pour la ressource. Les informations renvoyées sont au format JSON.

[!DNL Asset Compute Service] est extensible en créant des applications personnalisées basées sur [!DNL Project Firefly]. Ces applications personnalisées sont des applications [!DNL Project Firefly] sans interface utilisateur graphique. Elles effectuent des tâches comme l’ajout d’outils de conversion personnalisés ou l’appel d’API externes pour réaliser des opérations sur des images.

[!DNL Project Firefly] est un framework destiné à créer et déployer des applications web personnalisées sur [!DNL Adobe I/O] Runtime. Pour créer des applications personnalisées, les développeurs peuvent tirer parti de [!DNL React Spectrum] (boîte à outils d’interface utilisateur d’Adobe), créer des microservices et des événements personnalisés, et orchestrer les API. Voir la [documentation de Project Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

Les fondements de l’architecture sont les suivants :

* La modularité des applications (qui ne contiennent que le nécessaire pour une tâche donnée) permet de les découpler les unes des autres et d’en maintenir la légèreté.

* Le concept sans serveur de l’exécution [!DNL Adobe I/O] présente de nombreux avantages : le traitement asynchrone, hautement évolutif, isolé et basé sur les tâches, qui est idéal pour le traitement des ressources.

* Le stockage des contenus binaires dans le cloud apporte les fonctionnalités nécessaires pour stocker et accéder individuellement aux fichiers et aux rendus de ressources, sans exiger d’autorisations d’accès complètes au stockage grâce à des références à des URL présignées. L’accélération du transfert, la mise en cache du réseau de diffusion de contenu et la co-localisation des applications informatiques avec l’espace de stockage dans le cloud permettent un accès aux contenus optimal à faible latence. Les clouds AWS et Azure sont pris en charge.

![Architecture d’Asset Compute Service](assets/architecture-diagram.png)

*Figure : Architecture d’[!DNL Asset Compute Service] et intégration avec [!DNL Experience Manager], le stockage et l’application de traitement.*

L’architecture se compose des parties suivantes :

* **Une couche d’API et d’orchestration** reçoit les requêtes (au format JSON) qui demandent au service de transformer une ressource source en différents rendus. Les requêtes sont asynchrones et renvoyées avec un ID d’activation, c’est-à-dire un ID de tâche. Les instructions sont purement déclaratives et, pour tous les travaux de traitement standard (par exemple, génération de miniatures, extraction de texte), les consommateurs ne précisent que le résultat souhaité, mais pas les applications qui traitent certains rendus. Les fonctionnalités d&#39;API génériques telles que l&#39;authentification, les analyses, la limitation de débit, sont gérées à l&#39;aide de la passerelle API d&#39;Adobe devant le service et gèrent toutes les requêtes allant à l&#39;exécution [!DNL Adobe I/O]. Le routage de l’application est effectué dynamiquement par la couche d’orchestration. Les clients peuvent spécifier une application personnalisée pour des rendus spécifiques et inclure des paramètres personnalisés. L&#39;exécution de l&#39;application peut être entièrement mise en parallèle car il s&#39;agit de fonctions sans serveur distinctes dans [!DNL Adobe I/O] Runtime.

* **Applications de traitement des ressources** spécialisées dans certains types de formats de fichiers ou de rendus de cible. D’un point de vue conceptuel, une application ressemble au concept pipe d’Unix : un fichier d’entrée est transformé en un ou plusieurs fichiers de sortie.

* **Une [bibliothèque d’applications communes](https://github.com/adobe/asset-compute-sdk)** gère les tâches courantes comme le téléchargement du fichier source, le chargement des rendus, les rapports d’erreurs, l’envoi d’événements et la surveillance. Elle est conçue de manière à ce que le développement d’une application reste aussi simple que possible, conformément à l’idée de l’absence de serveur, et puisse être limité aux interactions avec le système de fichiers local.

<!-- TBD:

* About the YAML file?
* See [https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application).

* minimize description to custom applications
* remove all internal stuff (e.g. Photoshop application, API Gateway) from text and diagram
* update diagram to focus on 3rd party custom applications ONLY
* Explain important transactions/handshakes?
* Flow of assets/control? See the illustration on the Nui diagrams wiki.
* Illustrations. See the SVG shared by Alex.
* Exceptions? Limitations? Call-outs? Gotchas?
* Do we want to add what basic processing is not available currently, that is expected by existing AEM customers?
-->
