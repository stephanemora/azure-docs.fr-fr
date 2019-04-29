---
title: Calculs d’évaluations dans Azure Migrate | Microsoft Docs
description: Offre une vue d’ensemble des calculs d’évaluation dans le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 012a352b00de2e2d1bf64fd18125ddd10faba5cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679117"
---
# <a name="assessment-calculations"></a>Calculs d’évaluation

[Azure Migrate](migrate-overview.md) évalue les charges de travail sur site pour la migration vers Azure. Cet article donne des informations sur le calcul des évaluations.


## <a name="overview"></a>Vue d'ensemble

Une évaluation Azure Migrate comporte trois étapes. Elle commence par une analyse de l’adéquation, suivie du dimensionnement et pour finir, d’une estimation du coût mensuel. Une machine ne passe au stade suivant que si elle réussit le précédent. Par exemple, si elle rate la vérification d’adéquation Azure, elle est marquée comme inadaptée à Azure, et le dimensionnement et l’évaluation des coûts ne seront pas effectués.

## <a name="azure-suitability-analysis"></a>Analyse d’adéquation Azure

Toutes les machines ne conviennent pas à une exécution dans le cloud, car celui-ci a ses propres limitations et configuration requise. Azure Migrate évalue l’adéquation de chaque machine locale pour la migration vers Azure, et classe les machines dans l’une des catégories suivantes :
- **Disponible pour Azure** : la machine peut être migrée telle quelle vers Azure sans aucune modification. Elle démarrera dans Azure avec une prise en charge complète d’Azure.
- **Préparé pour Azure sous condition** : la machine peut démarrer dans Azure, mais est susceptible de ne pas offrir une prise en charge complète d’Azure. Par exemple, une machine avec une version antérieure du système d’exploitation Windows Server n’est pas prise en charge dans Azure. Vous devez faire attention lors de la migration de ces machines vers Azure, et suivre les instructions de correction suggérées dans l’évaluation afin de résoudre les problèmes de disponibilité avant la migration.
- **Non disponible pour Azure** : la machine ne démarrera pas dans Azure. Par exemple, si une machine locale a un disque dont la taille est supérieure à 4 To, elle ne peut pas être hébergée sur Azure. Vous devez suivre les instructions de correction suggérées dans l’évaluation afin de résoudre les problèmes de disponibilité avant la migration vers Azure. Le dimensionnement adéquat et l’estimation du coût ne sont pas effectués pour les machines marquées comme n’étant pas disponibles pour Azure.
- **État de la préparation inconnu** : Azure Migrate n’a pas pu déterminer l’état de préparation de la machine car les données disponibles dans vCenter Server sont insuffisantes.

Azure Migrate passe en revue les propriétés de la machine et le système d’exploitation invité pour identifier l’état de préparation Azure de la machine locale.

### <a name="machine-properties"></a>Propriétés de machine virtuelle
Azure Migrate passe en revue les propriétés suivantes de la machine virtuelle locale pour déterminer si une machine virtuelle peut s’exécuter sur Azure.

**Propriété** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
**Type de démarrage** | Azure prend en charge les machines virtuelles avec le type de démarrage BIOS, et non UEFI. | Préparé sous condition si le type de démarrage est UEFI.
**Cœurs** | Le nombre de cœurs des machines doit être inférieur ou égal au nombre maximal de cœurs (128) pris en charge pour une machine virtuelle Azure.<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération les cœurs utilisés pour la comparaison. Si un facteur de confort est spécifié dans les paramètres de l’évaluation, le nombre de cœurs utilisés est multiplié par le facteur de confort.<br/><br/> En l’absence d’historique des performances, Azure Migrate utilise les cœurs alloués, sans appliquer le facteur de confort. | Prêt si inférieur ou égal aux limites.
**Mémoire** | La taille de la mémoire de la machine doit être inférieure ou égale à la mémoire maximale (3 892 Go sur la série Azure M Standard_M128m&nbsp;<sup>2</sup>) autorisée pour une machine virtuelle Azure. [Plus d’informations](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération la mémoire utilisée pour la comparaison. Si un facteur de confort est spécifié, la mémoire utilisée est multipliée par le facteur de confort.<br/><br/> En l’absence d’historique, la mémoire allouée est utilisée, sans appliquer le facteur de confort.<br/><br/> | Prêt si dans les limites.
**Disque de stockage** | La taille allouée d’un disque doit être inférieure ou égale à 4 To (4 096 Go).<br/><br/> Le nombre de disques attachés à la machine doit être inférieur ou égal à 65, disque du système d’exploitation compris. | Prêt si dans les limites.
**Mise en réseau** | Au maximum 32 cartes réseau doivent être attachées à une machine. | Prêt si dans les limites.

### <a name="guest-operating-system"></a>Système d’exploitation invité
Outre les propriétés de la machine virtuelle, Azure Migrate examine également le système d’exploitation invité de la machine virtuelle locale afin de déterminer si celle-ci peut s’exécuter sur Azure.

> [!NOTE]
> Azure Migrate considère le système d’exploitation spécifié dans vCenter Server pour effectuer l’analyse suivante. La détection effectuée par Azure Migrate étant basée sur l’appareil, il n’a aucun moyen de vérifier si le système d’exploitation en cours d’exécution sur la machine virtuelle est identique à celui spécifié dans vCenter Server.

La logique suivante est utilisée par Azure Migrate pour déterminer l’état de préparation Azure de la machine virtuelle en fonction du système d’exploitation.

**Système d’exploitation** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
Windows Server 2016 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2012 R2 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2012 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2008 R2 avec tous les Service Packs | Azure assure un support complet.| Disponible pour Azure
Windows Server 2008 (32 bits et 64 bits) | Azure assure un support complet. | Disponible pour Azure
Windows Server 2003, 2003 R2 | La date limite de prise en charge de ces systèmes d’exploitation est passée, et leur prise en charge dans Azure nécessite un [Custom Support Agreement (CSA)](https://aka.ms/WSosstatement). | Préparé pour Azure sous condition. Envisagez une mise à niveau du système d’exploitation avant de migrer vers Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | La date limite de prise en charge de ces systèmes d’exploitation est passée. La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est fournie par Azure. | Préparé pour Azure sous condition. Nous vous recommandons d’effectuer une mise à niveau du système d’exploitation avant de migrer vers Azure.
Clients Windows 7, 8 et 10 | Azure assure un support avec [abonnement Visual Studio uniquement](https://docs.microsoft.com/azure/virtual-machines/windows/client-images). | Préparé pour Azure sous condition
Windows 10 Pro Desktop | Azure fournit la prise en charge avec les [droits d’hébergement multilocataire](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment). | Préparé pour Azure sous condition
Windows Vista, XP Professionnel | La date limite de prise en charge de ces systèmes d’exploitation est passée. La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est fournie par Azure. | Préparé pour Azure sous condition. Nous vous recommandons d’effectuer une mise à niveau du système d’exploitation avant de migrer vers Azure.
Linux | Azure approuve ces [systèmes d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). D’autres systèmes d’exploitation Linux peuvent démarrer dans Azure, mais nous vous recommandons d’effectuer une mise à niveau du système d’exploitation vers une version approuvée avant de migrer vers Azure. | Disponible pour Azure si la version est approuvée.<br/><br/>Préparé pour Azure sous condition si la version n’est pas approuvée.
Autres systèmes d’exploitation<br/><br/> (par exemple, Oracle Solaris, Apple Mac OS, FreeBSD, etc.) | Azure n’approuve pas ces systèmes d’exploitation. La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est fournie par Azure. | Préparé pour Azure sous condition. Nous vous recommandons d’installer un système d’exploitation pris en charge avant de migrer vers Azure.  
Système d’exploitation spécifié comme **Autre** dans vCenter Server | Azure Migrate ne peut pas identifier le système d’exploitation dans ce cas. | État de la préparation inconnu. Vérifiez que le système d’exploitation en cours d’exécution sur la machine virtuelle est pris en charge dans Azure.
Systèmes d’exploitation 32 bits | La machine peut démarrer dans Azure, mais il est possible qu’Azure ne fournisse pas une prise en charge complète. | Préparé pour Azure sous condition. Envisagez de mettre à niveau le système d’exploitation 32 bits de la machine vers un système d’exploitation 64 bits avant de migrer vers Azure.

## <a name="sizing"></a>Dimensionnement

Une fois qu’une machine a été marquée comme disponible pour Azure, Azure Migrate dimensionne la machine virtuelle et ses disques pour Azure. Si le critère de dimensionnement spécifié dans les propriétés de l’évaluation est le dimensionnement basé sur les performances, Azure Migrate prend en compte l’historique des performances de la machine pour identifier la taille et le type de disque de la machine virtuelle dans Azure. Cette méthode est utile dans les scénarios où vous avez suralloué la machine virtuelle locale, mais où l’utilisation est faible et vous souhaitez dimensionner correctement les machines virtuelles dans Azure afin de réduire les coûts.

Si vous ne souhaitez pas prendre en compte l’historique des performances pour le dimensionnement des machines virtuelles et que vous voulez migrer la machine virtuelle telle quelle sur Azure, vous pouvez spécifier le critère de dimensionnement *Localement*. Dans ce cas, Azure Migrate dimensionne les machines virtuelles d’après la configuration locale sans prendre en compte les données d’utilisation. Dans ce cas, le dimensionnement du disque est effectué en fonction du type de stockage que vous spécifiez dans les propriétés de l’évaluation (disque Standard ou disque Premium).

### <a name="performance-based-sizing"></a>Dimensionnement en fonction des performances

Pour le dimensionnement basé sur les performances, Azure Migrate commence par les disques attachés à la machine virtuelle, suivi des cartes réseau, puis il mappe une machine virtuelle Azure en fonction des exigences de calcul de la machine virtuelle locale.

- **Stockage** : Azure Migrate tente de mapper chaque disque attaché à la machine à un disque dans Azure.

    > [!NOTE]
    > Azure Migrate prend uniquement en charge les disques managés pour l’évaluation.

    - Pour obtenir les E/S disque par seconde (IOPS) et le débit (MBps) effectifs, Azure Migrate multiplie les E/S disque par seconde et le débit par le facteur de confort. En fonction des valeurs effectives d’IOPS et de débit, Azure Migrate détermine si le disque doit être mappé à un disque standard ou premium dans Azure.
    - Si Azure Migrate ne parvient pas à trouver un disque avec les IOPS et le débit requis, il marque la machine comme inadaptée à Azure. [En savoir plus](../azure-subscription-service-limits.md#storage-limits) sur les limites par disque et par machine virtuelle dans Azure.
    - S’il trouve un ensemble de disques adaptés, Azure Migrate sélectionne ceux qui prennent en charge la méthode de redondance du stockage et l’emplacement spécifié dans les paramètres d’évaluation.
    - Si plusieurs disques sont éligibles, il sélectionne celui dont le coût est le plus bas.
    - Si les données de performances des disques ne sont pas disponibles, tous les disques sont mappés à des disques standard dans Azure.

- **Réseau** : Azure Migrate essaie de trouver une machine virtuelle Azure qui peut prendre en charge le nombre de cartes réseau raccordées à la machine locale et les performances requises par ces cartes réseau.
    - Pour obtenir les performances réseau effectives de la machine virtuelle locale, Azure Migrate regroupe les données transmises par seconde (Mbits/s) à partir de l’ordinateur (données sortantes), parmi toutes les cartes réseau, et applique le facteur de confort. Ce nombre est utilisé pour trouver une machine virtuelle Azure qui prend en charge les performances réseau nécessaires.
    - Outre les performances réseau, il évalue également si la machine virtuelle Azure peut prendre en charge le nombre de cartes réseau requises.
    - Si aucune donnée de performances réseau n’est disponible, seul le nombre de cartes réseau est pris en considération pour le dimensionnement de la machine virtuelle.

- **Calcul** : une fois les exigences de réseau et de stockage calculées, Azure Migrate prend en compte les besoins en UC et en mémoire afin de trouver une taille de machine virtuelle appropriée dans Azure.
    - Azure Migrate examine les cœurs et la mémoire utilisés, et applique le facteur de confort pour obtenir les cœurs et la mémoire effectifs. En fonction de ce nombre, il s’efforce de trouver une taille de machine virtuelle adaptée dans Azure.
    - Si aucune taille adaptée n’est trouvée, la machine est marquée comme inadaptée à Azure.
    - Si une taille adaptée est trouvée, Azure Migrate applique les calculs de mise en réseau et de stockage. Il applique ensuite les paramètres d’emplacement et de niveau tarifaire de façon à obtenir la recommandation de taille de machine virtuelle finale.
    - S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.

### <a name="as-on-premises-sizing"></a>Dimensionnement « Localement »
Si le critère de dimensionnement est *Localement*, Azure Migrate ne tient pas compte de l’historique des performances des machines virtuelles et des disques, et alloue une référence (SKU) de machine virtuelle dans Azure en fonction de la taille allouée localement. De même, pour le dimensionnement du disque, il examine le type de stockage spécifié dans les propriétés de l’évaluation (Standard/Premium) et recommande le type de disque en conséquence. Le type de stockage par défaut est Disques Premium.

### <a name="confidence-rating"></a>Niveau de confiance
Chaque évaluation reposant sur des performances dans Azure Migrate est associée à un niveau de confiance allant de 1 étoile à 5 étoiles (1 étoile constituant la valeur la plus faible, et 5 étoiles la valeur la plus élevée). Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation. Le niveau de confiance d’une évaluation permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate. Le niveau de confiance n’est pas applicable aux évaluations locales.

Pour le dimensionnement basé sur les performances, Azure Migrate a besoin des données d’utilisation relatives au processeur et à la mémoire de la machine virtuelle. Par ailleurs, pour chaque disque attaché à la machine virtuelle, les E/S par seconde du disque et les données de débit sont nécessaires. De même, pour chaque carte réseau jointe à une machine virtuelle, Azure Migrate doit permettre au réseau entrant/sortant de respecter un dimensionnement basé sur les performances. Si aucun des chiffres d’utilisation ci-dessus n’est disponible dans vCenter Server, la recommandation de dimensionnement effectuée par Azure Migrate peut ne pas être fiable. Selon le pourcentage de points de données disponibles, le niveau de confiance pour l’évaluation est fourni comme suit :

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles

   Voici les raisons pour lesquelles une évaluation pourrait obtenir un niveau de confiance faible :

- Vous n’avez pas profilé votre environnement pour la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez l’évaluation avec une durée des performances définie sur 1 jour, vous devez attendre au moins un jour après le démarrage de la découverte pour collecter tous les points de données.

- Plusieurs machines virtuelles ont été arrêtées pendant la période de calcul de l’évaluation. Si des machines virtuelles ont été mises hors tension pendant un certain temps, vous ne pourrez pas collecter les données de performances pour cette période.

- Quelques machines virtuelles ont été créées pendant la période de calcul de l’évaluation. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques machines virtuelles dans l’environnement ne remonte qu’à une semaine. Dans ce cas, l’historique des performances des nouvelles machines virtuelles ne sera pas disponible pour toute la durée définie.

  > [!NOTE]
  > Si l’indice de confiance d’une évaluation est inférieur à 5 étoiles, nous vous recommandons d’attendre au moins un jour que l’appliance profile l’environnement, puis *recalculez* l’évaluation. Si vous ne pouvez pas effectuer ce qui précède, le dimensionnement basé sur les performances est susceptible de manquer de fiabilité ; nous vous recommandons alors de basculer vers le *dimensionnement local* en changeant les propriétés de l’évaluation.

## <a name="monthly-cost-estimation"></a>Estimation des coûts mensuels

Une fois les recommandations de dimensionnement terminées, Azure Migrate calcule les coûts de calcul et de stockage après la migration.

- **Calcul de coût** : à partir de la taille de machine virtuelle Azure recommandée, Azure Migrate utilise l’API de facturation pour calculer le coût mensuel de la machine virtuelle. Le calcul tient compte du système d’exploitation, de Software Assurance, des instances réservées, de la durée de fonctionnement de la machine virtuelle, de l’emplacement et des paramètres de la devise. Il agrège le coût de toutes les machines pour calculer le coût de calcul mensuel total.
- **Coût de stockage** : le coût de stockage mensuel d’une machine est calculé en additionnant le coût mensuel de tous les disques qui lui sont attachés. Azure Migrate calcule les coûts totaux de stockage mensuel en additionnant le coût de stockage de toutes les machines. Actuellement, le calcul ne prend pas en compte les offres spécifiées dans les paramètres d’évaluation.

Les coûts sont affichés dans la devise spécifiée dans les paramètres d’évaluation.


## <a name="next-steps"></a>étapes suivantes

[Créer une évaluation pour des machines virtuelles VMware locales](tutorial-assessment-vmware.md)
