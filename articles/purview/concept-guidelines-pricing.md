---
title: Conseils sur les prix Purview
description: Cet article fournit des indications sur la façon de comprendre les différents composants de la tarification de Purview.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: conceptual
ms.date: 10/03/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ee7364637e79b2603c6eca31d986dbcc3e153db3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084309"
---
# <a name="azure-purview-pricing"></a>Tarification d’Azure Purview   

Azure Purview offre une expérience unifiée de gouvernance en fournissant un volet unique pour la gestion de la gouvernance des données grâce à l’analyse automatisée et à la classification des données à l’échelle.


## <a name="intended-audience"></a>Public concerné

- Équipes en charge de la gestion et de la gouvernance des données

## <a name="why-do-you-need-to-understand-the-components-of-the-azure-purview-pricing"></a>Pourquoi devez-vous comprendre les composants de la tarification Azure Purview ? 

- Alors que la tarification pour Azure Purview repose sur un modèle de **paiement à l’accès** basé sur un abonnement, il existe différentes dimensions que vous pouvez prendre en compte lors de la budgétisation de Purview
- Cette recommandation a pour but de vous aider à planifier la budgétisation de Purview en fournissant une vue des différents facteurs de contrôle qui ont un impact sur le budget


## <a name="factors-impacting-azure-pricing"></a>Facteurs ayant une incidence sur la tarification Azure  
Des coûts **directs** et **indirects** doivent être pris en compte lors de la planification de la gestion des budgets et des coûts de Purview.

### <a name="direct-costs"></a>Coûts directs

Les coûts directs ayant un impact sur la tarification d’Azure Purview sont basés sur les trois dimensions suivantes :
- **Mappage de données élastique**
- **Classification et analyse automatisées**
- **Jeux de ressources avancés**

#### <a name="elastic-data-map"></a>Mappage de données élastique

- Le **mappage de données** est la base de l’architecture de Purview et doit donc être à jour pour ce qui est des informations sur les ressources dans le patrimoine de données à tout moment donné

- Le mappage de données est facturé en **unités de capacité** (CU). Le mappage de données est approvisionné à 1 CU si le catalogue stocke jusqu’à 2 Go de stockage de métadonnées et qu’il dessert jusqu’à 25 opérations de mappage de données/s

- Lors de la configuration initiale d’un compte, le mappage de données est toujours approvisionné à 1 CU

- Toutefois, le mappage de données est mis à l’échelle automatiquement entre les limites minimales et maximales de cette fenêtre d’élasticité, pour répondre aux changements du mappage de données par rapport à deux facteurs clés : le **débit d’opérations** et le **stockage des métadonnées**

##### <a name="operation-throughput"></a>Débit d’opérations

- Facteur piloté par les événements basé sur les opérations de création, lecture, mise à jour et suppression effectuées sur le mappage de données
- Voici quelques exemples d’opérations de mappage de données : création d’une ressource dans le mappage de données, ajout d’une relation à un élément multimédia, par exemple propriétaire, gestion, parent ou traçabilité, modification d’un élément multimédia pour ajouter des métadonnées d’entreprise comme une description ou un terme de glossaire, recherche de mots-clés retournant des résultats dans la page de résultats de recherche, importation ou exportation d’informations avec une API
- Si plusieurs requêtes sont exécutées sur le mappage de données, le nombre d’opérations d’E/S augmente également, ce qui entraîne la mise à l’échelle du mappage de données
- Le nombre d’utilisateurs simultanés constitue également un facteur régi par l’unité de capacité du mappage de données
- D’autres facteurs à prendre en compte sont le type de requête de recherche, l’interaction avec les API, les workflows, les approbations, etc.
- Niveau de rafale des données
    - En cas de besoin d’un débit plus grand d’opérations/seconde, le mappage de données peut être mis à l’échelle automatiquement dans la fenêtre d’élasticité pour s’adapter à la charge modifiée
    - Cela constitue la **caractéristique de rafale** qui doit être estimée et planifiée
    - La caractéristique de rafale comprend le **niveau de rafale** et la **durée de rafale** pour laquelle la rafale existe
        - Le **niveau de rafale** est un index multiplicatif de l’élasticité cohérente attendue dans un état stable
        - La **durée de la rafale** est le pourcentage du mois pendant lequel de telles rafales (en élasticité) sont attendues en raison des métadonnées croissantes ou d’un nombre plus élevé d’opérations sur le mappage de données


##### <a name="metadata-storage"></a>Stockage des métadonnées

- Si le nombre de ressources est réduit dans le patrimoine de données et qu’une suppression s’ensuit dans le mappage de données via des analyses incrémentielles ultérieures, le composant de stockage est automatiquement réduit et, par conséquent, le mappage de données est mis à l’échelle


#### <a name="automated-scanning--classification"></a>Classification et analyse automatisées

- Une **analyse complète** traite toutes les ressources au sein d’une étendue sélectionnée d’une source de données, tandis qu’une **analyse incrémentielle** détecte et traite les ressources qui ont été créées, modifiées ou supprimées depuis la dernière analyse réussie 

- Toutes les analyses (complètes ou incrémentielles) récupèrent les ressources **mises à jour, modifiées ou supprimées**

- Il est important de prendre en compte et d’éviter les scénarios dans lesquels plusieurs personnes ou groupes appartenant à différents services configurent des analyses pour la même source de données, ce qui entraîne une facturation supplémentaire pour les analyses en double

- Il est recommandé de planifier des **analyses incrémentielles fréquentes**, après quoi l’analyse complète initiale est alignée sur les modifications apportées à l’espace de données. Cela permet de s’assurer que le mappage de données est toujours à jour et que les analyses incrémentielles consomment moins d’heures de vCore par rapport à une analyse complète

- Le lien **« Afficher les détails »** d'une source de données permettra aux utilisateurs d'exécuter une analyse complète. Toutefois, il est recommandé d’exécuter des analyses incrémentielles après une analyse complète pour optimiser l’analyse, sauf en cas de modification de l’ensemble de règles d’analyse (classifications/types de fichiers)

- Il est recommandé d’**inscrire la source de données auprès d’une collection parente** et d’**étendre les analyses au regroupement enfant** avec différents contrôles pour s’assurer qu’aucun coût d’analyse en double n’est engendré

- Il est recommandé de restreindre les utilisateurs autorisés à inscrire des sources de données pour l’analyse par le biais du **contrôle d'accès affiné** et du rôle **Administrateur de source de données** à l’aide de l’[Autorisation de regroupement](./catalog-permissions.md). Cela permet de s’assurer que seules les sources de données valides sont autorisées à être inscrites et que l’analyse des heures de vCore est contrôlée, ce qui réduit les coûts d’analyse

- Tenez compte du fait que le **type de source de données** et le **nombre de ressources** analysées ont un impact sur la durée de l’analyse

- Nous vous recommandons de **créer des ensembles de règles d’analyse personnalisés** pour inclure uniquement le sous-ensemble de **types de fichiers** disponibles dans vos données, et les **classifications** qui sont pertinentes pour les besoins de votre entreprise afin de garantir une utilisation optimale des analyseurs

- Lors de la création d’une nouvelle analyse pour une source de données, il est recommandé de suivre l’**ordre de préparation** recommandé avant d’exécuter l’analyse. Cela comprend la collecte des exigences pour les **classifications** et les **types de fichiers propres à l’entreprise** (pour les comptes de stockage), afin de permettre la définition d’ensembles de règles d’analyse appropriés afin d’éviter les analyses multiples et de limiter les coûts inutiles des analyses multiples en cas d’exigences manquées

- Nous vous recommandons d’aligner vos planifications d’analyse avec des machines virtuelles à runtime d'intégration auto-hébergé (SHIR) afin d’éviter les coûts supplémentaires liés aux machines virtuelles


#### <a name="advanced-resource-sets"></a>Jeux de ressources avancés

- Azure Purview utilise des **jeux de ressources** pour résoudre le problème de mappage d’un grand nombre de ressources de données à une ressource logique unique en offrant la possibilité d’analyser tous les fichiers du lac de données et de rechercher des modèles (GUID, modèles de localisation, etc.) pour les regrouper en tant que ressource unique dans le mappage de données

- Le **jeu de ressources avancé** est une fonctionnalité facultative qui permet aux clients d’obtenir des informations de jeu de ressources enrichies calculées, comme la taille totale, le nombre de partitions, etc., et permet la personnalisation du regroupement des jeux de ressources via des règles de modèle. Si la fonctionnalité de jeu de ressources avancé n’est pas activée, votre catalogue de données contiendra toujours les ressources de jeu de ressources, mais sans les propriétés agrégées. Dans ce cas, aucun compteur de « jeu de ressources » n’est facturé au client.

- Nous vous recommandons d’utiliser la fonctionnalité de jeu de ressources de base avant de passer aux jeux de ressources avancés dans Purview, afin de vérifier si la configuration requise est respectée

- Envisagez d’activer les jeux de ressources avancés dans les cas suivants :
    - Votre schéma de lacs de données évolue constamment et vous recherchez une valeur supplémentaire au-delà de la fonctionnalité du jeu de ressources de base, pour permettre à Purview de calculer des paramètres tels que le nombre de partitions, la taille de la surface de données, etc., en tant que service
    - Il est nécessaire de personnaliser la façon dont les ressources du jeu de ressources sont regroupées 

- Il est important de noter que la facturation des jeux de ressources avancés est basée sur le calcul utilisé par le niveau hors connexion pour agréger des informations sur les jeux de ressources et dépend de la taille/du nombre de jeux de ressources dans votre catalogue


### <a name="indirect-costs"></a>Coûts indirects   

Les coûts indirects ayant un impact sur la tarification d’Azure Purview sont les suivants :

- [Ressources managées](https://azure.microsoft.com/pricing/details/azure-purview/)
    - Lors de l’approvisionnement d’un compte Purview, un compte de stockage et une file d’attente Event Hub sont créés dans l’abonnement afin de permettre l’analyse sécurisée, qui peut être facturée séparément


- [Point de terminaison Azure](./catalog-private-link.md)
    - Des points de terminaison privés Azure sont utilisés pour les comptes Purview où les utilisateurs d’un réseau virtuel doivent accéder en toute sécurité au catalogue via une liaison privée
    - La configuration requise pour la configuration de points de terminaison privés peut entraîner des frais supplémentaires

- [Coûts liés au runtime d’intégration (IR) auto-hébergé](./manage-integration-runtimes.md) 
    - Le runtime d’intégration auto-hébergé nécessite une infrastructure, ce qui entraîne des coûts supplémentaires
    - Il est requis pour déployer et inscrire le runtime d’intégration auto-hébergé (SHIR) à l’intérieur du même réseau virtuel où les points de terminaison privés d’ingestion Azure Purview sont déployés
    - [Mémoire supplémentaire requise pour l’analyse](./register-scan-sapecc-source.md#create-and-run-scan)
        - Certaines sources de données telles que SAP requièrent de la mémoire supplémentaire sur la machine SHIR pour l’analyse


- [Tailles des machines virtuelles](../virtual-machines/sizes.md)
    - Planifiez le dimensionnement des machines virtuelles afin de répartir la charge de travail d’analyse entre les machines virtuelles afin d’optimiser les vCores utilisés lors de l’exécution des analyses

- [Licence Microsoft 365](./create-sensitivity-label.md) 
    - Les étiquettes de sensibilité Microsoft Information Protection (MIP) peuvent être automatiquement appliquées à vos ressources Azure dans Azure Purview.
    - Les étiquettes de confidentialité MIP sont créées et gérées dans le Centre de sécurité et conformité Microsoft 365.
    - Pour créer des étiquettes de confidentialité à utiliser dans Azure Purview, vous devez disposer d’une licence Microsoft 365 active qui offre l’avantage d’appliquer automatiquement des étiquettes. Pour obtenir la liste complète des licences, consultez le Forum aux questions sur les étiquettes de confidentialité dans Azure Purview. 

- [Alertes Azure](../azure-monitor/alerts/alerts-overview.md)
    - Les alertes Azure peuvent informer les clients des problèmes rencontrés avec l’infrastructure ou les applications à l’aide des données de surveillance dans Azure Monitor
    - Les tarifs supplémentaires pour les alertes Azure sont disponibles [ici](https://azure.microsoft.com/pricing/details/monitor/)

- [Budgets et alertes de gestion des coûts](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md)
    - Les alertes de coût générées automatiquement sont utilisées dans Azure pour surveiller l’utilisation et les dépenses Azure en fonction du moment où les ressources Azure sont consommées
    - Azure vous permet de créer et de gérer des budgets Azure. Consultez le [tutoriel](../cost-management-billing/costs/tutorial-acm-create-budgets.md)

- Frais de sortie multicloud
    - Tenez compte des frais de sortie (coûts minimaux ajoutés dans le cadre de l’abonnement multicloud) associés à l’analyse de sources de données multicloud (AWS, Google) exécutant des services natifs, à l’exception des sources S3 et RDS


## <a name="next-steps"></a>Étapes suivantes
- [Page de tarification d’Azure Purview](https://azure.microsoft.com/pricing/details/azure-purview/)
