---
title: Présentation d’Azure Stream Analytics
description: Découvrez Stream Analytics, un service géré qui vous permet d’analyser les données de diffusion en continu à partir de l’Internet des objets (IoT) en temps réel.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 3399cc47af20df05d6315a4bd77965f799eaf5f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426183"
---
# <a name="what-is-azure-stream-analytics"></a>Qu’est-ce qu’Azure Stream Analytics ?

Azure Stream Analytics est un moteur complexe d’analyse et de traitement d’événements en temps réel conçu pour analyser et traiter de grands volumes de données diffusées, ou de données diffusées rapidement à partir de nombreuses sources à la fois. Les modèles et les relations peuvent être identifiés dans des informations extraites de plusieurs sources d’entrée, notamment des appareils, des capteurs, des journaux de parcours de site web, des flux de médias sociaux et des applications. Ces modèles peuvent être utilisés pour déclencher des actions est initier des flux de travail, comme la création d’alertes, l’envoi d’informations vers un outil de création de rapports ou bien le stockage pour une utilisation ultérieure. En outre, Stream Analytics est disponible sur le runtime Azure IoT Edge et prend en charge exactement le même langage ou la même syntaxe que le cloud. 

Les scénarios suivants sont des exemples d’utilisation d’Azure Stream Analytics :

* Analyser les flux de données de télémétrie en temps réel à partir d’appareils IoT
* Analyse de journaux d’activité/parcours web
* Analyses géospatiales pour la gestion de flotte et les véhicules sans pilote
* Surveillance à distance et maintenance prédictive de ressources stratégiques
* Analyse en temps réel sur les données de point de vente pour le contrôle des stocks et la détection d’anomalies

## <a name="how-does-stream-analytics-work"></a>Comment fonctionne Stream Analytics ?

Un travail Azure Stream Analytics se compose d’une entrée, d’une requête et d’une sortie. Stream Analytics peut ingérer des données d’Azure Event Hubs, d’Azure IoT Hub et d’Azure Blob Storage. La requête, qui est basée sur un langage de requête de type SQL, peut être utilisée pour filtrer, trier, agréger et joindre facilement des données de streaming sur une période de temps donnée. Vous pouvez également étendre ce langage SQL avec JavaScript et les fonctions C# définies par l’utilisateur. Vous pouvez facilement modifier l’ordre des événements et la durée des fenêtres de temps lors des opérations d’agrégation via de simples constructions de langage et/ou configurations.

Chaque travail a une sortie pour les données transformées, et vous pouvez définir ce qui arrive en fonction des informations que vous avez analysées. Vous pouvez par exemple :

* Envoyez des données à des services tels qu’Azure Functions ou Rubriques/Files d'attente Service Bus pour déclencher des communications ou flux de travail personnalisés en aval.
* Envoyer des données à un tableau de bord Power BI pour un tableau de bord en temps réel.
* Stocker des données dans d’autres services de stockage Azure, afin d’effectuer l’apprentissage d’un modèle Machine Learning basé sur des données historiques ou d’effectuer des analyses en mode batch.

L’illustration suivante montre comment les données sont envoyées à Stream Analytics, analysées, puis transmises dans le cadre d’autres actions telles que le stockage, ou une présentation :

![Pipeline d’introduction de Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Avantages et fonctionnalités clés

Azure Stream Analytics est conçu pour être facile à utiliser, flexible, fiable et évolutif selon le volume du travail. Il est disponible dans plusieurs régions Azure. L’image suivante illustre les fonctionnalités clés d’Azure Stream Analytics :

![Fonctionnalités clés de Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Prise en main rapide

Azure Stream Analytics est prêt à fonctionner. Quelques clics suffisent pour se connecter à différents récepteurs et sources de données et créer un pipeline de bout en bout. Stream Analytics peut se connecter directement à [Azure Event Hubs](/azure/event-hubs/) et à [Azure IoT Hub](/azure/iot-hub/) pour l’ingestion de données diffusées en continu, ainsi qu’au [stockage Blob Azure](/azure/storage/storage-introduction) pour l’ingestion de données historiques. L’entrée du travail peut également inclure des données de référence statiques ou à variation lente du stockage blob Azure ou de [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database), que vous pouvez joindre aux données de streaming pour effectuer des opérations de recherche.

Stream Analytics peut acheminer la sortie du travail vers de nombreux systèmes de stockage, tels que [Stockage Blob Azure](/azure/storage/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/) et [Azure CosmosDB](/azure/cosmos-db/introduction). Vous pouvez exécuter des analyses en mode batch sur une sortie stockée à l’aide d’Azure HDInsight ou envoyer la sortie vers un autre service, tel que Event Hubs, à des fins de consommation ou [Power BI](https://docs.microsoft.com/power-bi/) pour une visualisation en temps réel.

Pour obtenir la liste complète des sorties Stream Analytics, consultez [Comprendre les sorties d’Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Productivité du programmeur

Azure Stream Analytics utilise un langage de requête SQL simple qui intègre des contraintes temporelles puissantes pour analyser les données en mouvement. Pour définir les transformations de travail, vous utilisez un [langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) simple et déclaratif qui vous permet de créer des requêtes temporelles complexes ainsi que des opérations d’analyse à l’aide de constructions SQL simples. Étant donné que le langage de requête Stream Analytics est similaire au langage SQL, une bonne connaissance de SQL est suffisante pour commencer à créer des travaux. Vous pouvez également créer des travaux à l’aide d’outils de développement tels qu’Azure PowerShell, [les outils Stream Analytics pour Visual Studio](stream-analytics-tools-for-visual-studio-install.md), l’[extension Stream Analytics pour Visual Studio Code](quick-create-vs-code.md) ou les modèles Azure Resource Manager. Les outils de développement vous permettent de développer des requêtes de transformation en mode hors connexion et d’utiliser le [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) pour envoyer des travaux vers Azure.

Le langage de requête Stream Analytics offre une large gamme de fonctions pour analyser et traiter des données de streaming. Ce langage de requête prend en charge la manipulation de données standard, des fonctions d’agrégation et des fonctions géospatiales complexes. Vous pouvez modifier les requêtes dans le portail et les tester à l’aide d’exemples de données extraits à partir d’un stream en direct.

Vous pouvez étendre les fonctionnalités du langage de requête en définissant et en appelant des fonctions supplémentaires. Vous pouvez définir des appels de fonction dans Azure Machine Learning pour tirer parti des solutions d’Azure Machine Learning et intégrer des fonctions JavaScript ou C# définies par l’utilisateur ou des agrégats définis par l’utilisateur pour effectuer des calculs complexes dans le cadre d’une requête Stream Analytics.

## <a name="fully-managed"></a>Gestion intégrale

Azure Stream Analytics est une plate-forme entièrement gérée et sans serveur (PaaS) disponible sur Azure. Vous n’avez pas besoin d’investir dans un matériel spécifique ou de gérer des clusters pour exécuter vos travaux. Azure Stream Analytics gère intégralement votre travail en configurant des clusters de calcul complexes dans le cloud et en se chargeant du réglage des performances nécessaire pour exécuter le travail. L’intégration à Azure Event Hubs et Azure IoT Hub permet à votre travail d’ingérer des millions d’événements par seconde. Ces événements proviennent de sources diverses pour inclure des appareils connectés, des flux de clics et des fichiers journaux. À l’aide de la fonction de partition d’Event Hubs, vous pouvez partitionner des calculs en étapes logiques, chacune avec la possibilité d’être partitionnée pour augmenter l’évolutivité.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Exécuter dans le cloud ou à la périphérie intelligente

Azure Stream Analytics peut être exécuté dans le cloud pour effectuer des analyses à grande échelle, ou sur IoT Edge pour effectuer des analyses à très faible latence. Azure Stream Analytics utilise le même langage de requête dans le cloud et à la périphérie. Ainsi, les développeurs peuvent créer des architectures véritablement hybrides à des fins de traitement de flux. 

## <a name="low-total-cost-of-ownership"></a>Faible coût total de possession

Comme un service cloud, Stream Analytics est optimisé pour réduire le coût. Aucun frais initial ne s’applique. Vous payez uniquement pour les [unités de streaming que vous consommez](stream-analytics-streaming-unit-consumption.md) et pour la quantité de données traitées. Aucun engagement ni aucun approvisionnement de cluster requis, et vous pouvez faire évoluer le travail vers le haut ou vers le bas en fonction des besoins de votre entreprise.

## <a name="mission-critical-ready"></a>Intégration stratégique immédiate

Le moteur Azure Stream Analytics est disponible dans plusieurs régions du monde entier ; il est conçu pour l’exécution des charges de travail critiques tout en prenant en charge les exigences de conformité, de sécurité et de fiabilité.

### <a name="reliability"></a>Fiabilité

Azure Stream Analytics garantit un traitement des événements en « exactement une fois », et une remise des événements « une fois au minimum », ce qui évite la perte d’événements. Le traitement en une fois exactement est garanti avec une sortie sélectionnée, comme décrit dans [Garanties de remise d’événement](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics dispose de fonctionnalités de récupération intégrées, en cas d’échec de la remise d’un événement. Stream Analytics fournit également des points de contrôle intégrés pour gérer l’état de votre travail et fournir des résultats reproductibles.

Stream Analytics est un service managé qui garantit le traitement des événements avec une disponibilité de 99,9 % au niveau de granularité Minute. Pour plus d’informations, consultez la page [Contrat de niveau de service Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/). 

### <a name="security"></a>Sécurité

En termes de sécurité, Azure Stream Analytics chiffre toutes les communications entrantes et sortantes, il prend également en charge TLS 1.2. Les points de contrôle intégrés sont également chiffrés. Stream Analytics ne stocke pas les données entrantes dans la mesure où tout le traitement s’effectue en mémoire.

### <a name="compliance"></a>Conformité

Azure Stream Analytics suit plusieurs certifications de conformité, comme décrit dans la [vue d’ensemble de la conformité Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Performances

Stream Analytics peut traiter des millions d’événements par seconde et fournir des résultats avec une latence ultra faible. Il vous permet de mettre à l’échelle vos travaux pour prendre en charge des applications volumineuses de traitement des événements complexes et en temps réel. Stream Analytics offre de meilleures performances à l’aide du partitionnement, permettant de paralléliser des requêtes complexes et de les exécuter sur plusieurs nœuds de streaming. Azure Stream Analytics repose sur [Trill](https://github.com/Microsoft/Trill), un moteur haute performance d’analyse de streaming en mémoire développé en collaboration avec Microsoft Research.

## <a name="next-steps"></a>Étapes suivantes

Vous connaissez désormais les notions de base sur Azure Stream Analytics. Pour approfondir ces connaissances et créer votre premier travail Stream Analytics, consultez les articles suivants :

* [Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md).
* [Créer un travail Stream Analytics à l’aide d’Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Créer un travail Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md).
* [Créer un travail Stream Analytics à l’aide de Visual Studio Code](quick-create-vs-code.md).
