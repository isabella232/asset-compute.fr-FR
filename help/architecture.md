---
title: Architecture de [!DNL Asset Compute Service].
description: ' [!DNL Asset Compute Service] HowAPI, applications et SDK fonctionnent ensemble pour fournir un service de traitement des ressources natif au cloud.'
translation-type: tm+mt
source-git-commit: 0fb256f7d9f83fbae564d9fd52ee6b2f34c5d7e9
workflow-type: tm+mt
source-wordcount: '496'
ht-degree: 0%

---


# Architecture de [!DNL Asset Compute Service] {#overview}

Il [!DNL Asset Compute Service] est construit sur la plate-forme Adobe I/O Runtime sans serveur. Il fournit une prise en charge des ressources par les services de contenu Adobe Sensei. Le client appelant (uniquement [!DNL Experience Manager] en tant que Cloud Service pris en charge) reçoit les informations générées par Adobe Sensei qu’il a recherchées pour la ressource. Les informations renvoyées sont au format JSON.

[!DNL Asset Compute Service] est extensible en créant des applications personnalisées basées sur [!DNL Project Firefly]. Ces applications personnalisées sont des applications sans [!DNL Project Firefly] tête et comportent des tâches telles que l’ajout d’outils de conversion personnalisés ou l’appel d’API externes pour effectuer des opérations d’image.

[!DNL Project Firefly] est une structure permettant de créer et de déployer des applications Web personnalisées au moment de l’ [!DNL Adobe I/O] exécution. Pour créer des applications personnalisées, les développeurs peuvent tirer parti [!DNL React Spectrum] (kit d’outils de l’interface utilisateur de l’Adobe), créer des microservices, créer des événements personnalisés et orchestrer les API. Voir la [documentation du projet Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

Les fondements sur lesquels repose l&#39;architecture sont les suivants :

* La modularité des applications - ne contenant que ce qui est nécessaire pour une tâche donnée - permet de découpler les applications les unes des autres et de les garder légères.

* Le concept sans serveur de Adobe I/O Runtime présente de nombreux avantages : le traitement asynchrone, hautement évolutif, isolé et basé sur les tâches, qui est idéal pour le traitement des ressources.

* L’enregistrement cloud binaire fournit les fonctionnalités nécessaires pour stocker et accéder individuellement aux fichiers et aux rendus de ressources, sans exiger d’autorisations d’accès complètes à l’enregistrement, en utilisant des références URL pré-signées. L’accélération du transfert, la mise en cache CDN et la co-localisation des applications informatiques avec l’enregistrement cloud permettent un accès optimal au contenu à faible latence. Les nuages AWS et Azure sont pris en charge.

![Architecture du service Asset Compute](assets/architecture-diagram.png)

*Figure : Architecture de [!DNL Asset Compute Service] et de la manière dont elle s’intègre à [!DNL Experience Manager]l’application de traitement, d’enregistrement et d’application.*

L&#39;architecture se compose des parties suivantes :

* **Une couche** d’API et d’orchestration reçoit des requêtes (au format JSON) qui demandent au service de transformer un fichier source en plusieurs rendus. Ces requêtes sont asynchrones et renvoyées avec un ID d’activation (appelé &quot;ID de tâche&quot;). Les instructions sont purement déclaratives et, pour tous les travaux de traitement standard (par exemple, la génération de miniatures, l’extraction de texte), les consommateurs ne précisent que le résultat souhaité, mais pas les applications qui traitent certains rendus. Les fonctionnalités d&#39;API génériques telles que l&#39;authentification, l&#39;analyse, la limitation de débit, sont gérées à l&#39;aide de la passerelle API d&#39;Adobe devant le service et gèrent toutes les requêtes allant à l&#39;exécution d&#39;E/S. Le routage de l’application est effectué dynamiquement par la couche d’orchestration. Les clients peuvent spécifier une application personnalisée pour des rendus spécifiques et inclure des paramètres personnalisés. L&#39;exécution des applications peut être entièrement parallélisée car il s&#39;agit de fonctions distinctes sans serveur dans l&#39;exécution des E/S.

* **Applications de traitement des ressources** spécialisées dans certains types de formats de fichier ou de rendus de cible. D&#39;un point de vue conceptuel, une application ressemble au concept de tuyau Unix : un fichier d’entrée est transformé en un ou plusieurs fichiers de sortie.

* **Une bibliothèque [d’applications](https://github.com/adobe/asset-compute-sdk)** courante gère les tâches courantes telles que le téléchargement du fichier source, le transfert des rendus, le rapports d’erreur, l’envoi de événements et la surveillance. Il est conçu de manière à ce que le développement d&#39;une application reste aussi simple que possible, suivant l&#39;idée sans serveur, et puisse être limité aux interactions du système de fichiers local.

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
