---
title: Calculs d’évaluations dans Azure Migrate | Microsoft Docs
description: Offre une vue d’ensemble des calculs d’évaluation dans le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234289"
---
# <a name="assessment-calculations"></a>Calculs d’évaluation

[Azure Migrate](migrate-services-overview.md) offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure de vos applications et charges de travail locales, et d’instances cloud privées/publiques. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.

Server Assessment est un outil d’Azure Migrate qui évalue les serveurs locaux pour la migration vers Azure. Cet article donne des informations sur le calcul des évaluations.

## <a name="whats-in-an-assessment"></a>Que comprend une évaluation ?

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer.<br/> Server Assessment prend actuellement en charge les régions cibles suivantes : Allemagne Centre, Allemagne Nord-Est, Asie Est, Canada Centre, Canada Est, Inde Centre, Chine Est, Chine Nord, Corée Centre, Corée Sud, Australie Est, Europe Ouest, Europe Nord, Inde Ouest, Inde Sud, Japon Est, Japon Ouest, USA Centre Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Brésil Sud, USA Centre Sud, Asie Sud-Est, Australie Sud-Est, US Gov Arizona, US Gov Texas, US Gov Virginie, USA Centre-Ouest, USA Ouest 2, USA Est, USA Est 2, USA Ouest et USA Centre.
**Type de stockage** | Vous pouvez utiliser cette propriété pour spécifier le type de disques que vous voulez déplacer dans Azure. <br/><br/> Pour un redimensionnement local, vous pouvez spécifier le type de stockage cible comme disques managés Premium, disques SSD Standard managés ou disques HDD Standard managés. Pour un redimensionnement basé sur les performances, vous pouvez spécifier le type de disque cible comme disques automatiques, disques managés Premium, disques managés HDD Standard ou disques managés SSD Standard.<br/><br/> Lorsque vous spécifiez le type de stockage automatique, la recommandation de disque s’appuie sur les données de performances des disques (IOPS et débit). Si vous spécifiez le type de stockage premium/standard, l’évaluation recommandera une référence SKU de disque au sein du type de stockage sélectionné. Si vous voulez obtenir un contrat SLA de machine virtuelle à instance unique de 99,9 %, vous pouvez spécifier le type de stockage comme disques managés Premium. De cette façon, tous les disques de l’évaluation sont recommandés comme disques managés Premium. Notez qu’Azure Migrate prend uniquement en charge les disques managés pour l’évaluation de la migration.
**Instances réservées** | Cette propriété vous aide à spécifier si vous avez des [instances réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/) dans Azure : les estimations de coût de l’évaluation sont alors effectuées en tenant compte des remises des instances réservées. Les instances réservées ne sont actuellement prises en charge que dans le cadre de l’offre de paiement à l’utilisation d’Azure Migrate.
**Critère de dimensionnement** | Critère utilisé pour dimensionner correctement les machines virtuelles pour Azure. Vous pouvez effectuer un dimensionnement *en fonction des performances* ou dimensionner les machines virtuelles *comme des machines locales* sans tenir compte de l’historique des performances.
**Historique des performances** | Durée à prendre en compte pour évaluer les données de performances des machines. Cette propriété s’applique seulement quand le critère de dimensionnement est *basé sur les performances*.
**Utilisation en centile** | Valeur de centile du jeu d’échantillons de performances devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique seulement quand le critère de dimensionnement est le *basé sur les performances*.
**Séries de machine virtuelle** |     Vous pouvez spécifier les séries de machines virtuelles à prendre en compte pour le dimensionnement. Par exemple, si vous disposez d’un environnement de production que vous ne souhaitez pas migrer vers des machines virtuelles de série A dans Azure, vous pouvez exclure la série A de la liste des séries : le dimensionnement voulu est alors effectué uniquement dans les séries sélectionnées.
**Facteur de confort** | Azure Migrate Server Assessment considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs.
**Offer** | L’[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes inscrit. Azure Migrate estime le coût en conséquence.
**Devise** | Devise de facturation.
**Remise (%)** | Remise propre à un abonnement cumulable avec l’offre Azure.<br/> Le paramètre par défaut est 0 %.
**Durée de fonctionnement de la machine virtuelle** | Si vos machines virtuelles ne s’exécutent pas 24/7 dans Azure, vous pouvez spécifier leur durée d’exécution (nombre de jours par mois et nombre d’heures par jour) ; l’estimation des coûts est calculée en fonction.<br/> La valeur par défaut est de 31 jours par mois et de 24 heures par jour.
**Azure Hybrid Benefit** | Spécifiez si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. La valeur par défaut est Oui.

## <a name="how-are-assessments-calculated"></a>Comment les évaluations sont-elles calculées ?

Une évaluation dans Azure Migrate Server Assessment est calculée en utilisant les métadonnées collectées sur les serveurs locaux. Le calcul de l’évaluation s’effectue en trois étapes : pour chaque serveur, le calcul de l’évaluation commence par l’analyse d’adéquation à Azure, suivi du dimensionnement, et enfin d’une estimation des coûts mensuels. Un serveur ne passe au stade suivant que s’il a passé le précédent. Par exemple, si le serveur échoue à la vérification de l’adéquation à Azure, il est marqué comme inadapté à Azure, et le dimensionnement et l’évaluation des coûts ne sont pas effectués pour ce serveur.

## <a name="azure-suitability-analysis"></a>Analyse d’adéquation Azure

Touts les machines ne conviennent pas pour une exécution dans Azure. Azure Migrate Server Assessment évalue l’adéquation de chaque machine locale pour la migration vers Azure, et classe les machines évaluées dans une des catégories d’adéquation suivantes :
- **Disponible pour Azure** : la machine peut être migrée telle quelle vers Azure sans aucune modification. Elle démarrera dans Azure avec une prise en charge complète d’Azure.
- **Préparé pour Azure sous condition** : la machine peut démarrer dans Azure, mais est susceptible de ne pas offrir une prise en charge complète d’Azure. Par exemple, une machine avec une version antérieure du système d’exploitation Windows Server n’est pas prise en charge dans Azure. Vous devez faire attention lors de la migration de ces machines vers Azure, et suivre les instructions de correction suggérées dans l’évaluation afin de résoudre les problèmes de disponibilité avant la migration.
- **Non disponible pour Azure** : la machine ne démarrera pas dans Azure. Par exemple, si une machine locale a un disque dont la taille est supérieure à 64 To, elle ne peut pas être hébergée sur Azure. Vous devez suivre les instructions de correction suggérées dans l’évaluation afin de résoudre les problèmes de disponibilité avant la migration vers Azure. Le dimensionnement adéquat et l’estimation du coût ne sont pas effectués pour les machines marquées comme n’étant pas disponibles pour Azure.
- **État de la préparation inconnu** : Azure Migrate n’a pas pu déterminer l’état de préparation de la machine, car les métadonnées collectées dans l’environnement local sont insuffisantes.

Azure Migrate Server Assessment examine les propriétés de la machine et le système d’exploitation invité pour identifier l’état de préparation Azure de la machine locale.

### <a name="machine-properties"></a>Propriétés de machine virtuelle

Server Assessment passe en revue les propriétés suivantes de la machine virtuelle locale pour déterminer si elle peut s’exécuter sur Azure.

**Propriété** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
**Type de démarrage** | Azure prend en charge les machines virtuelles avec le type de démarrage BIOS, et non UEFI. | Préparé sous condition si le type de démarrage est UEFI.
**Cœurs** | Le nombre de cœurs des machines doit être inférieur ou égal au nombre maximal de cœurs (128) pris en charge pour une machine virtuelle Azure.<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération les cœurs utilisés pour la comparaison. Si un facteur de confort est spécifié dans les paramètres de l’évaluation, le nombre de cœurs utilisés est multiplié par le facteur de confort.<br/><br/> En l’absence d’historique des performances, Azure Migrate utilise les cœurs alloués, sans appliquer le facteur de confort. | Prêt si inférieur ou égal aux limites.
**Mémoire** | La taille de la mémoire de la machine doit être inférieure ou égale à la mémoire maximale (3 892 Go sur la série Azure M Standard_M128m&nbsp;<sup>2</sup>) autorisée pour une machine virtuelle Azure. [Plus d’informations](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération la mémoire utilisée pour la comparaison. Si un facteur de confort est spécifié, la mémoire utilisée est multipliée par le facteur de confort.<br/><br/> En l’absence d’historique, la mémoire allouée est utilisée, sans appliquer le facteur de confort.<br/><br/> | Prêt si dans les limites.
**Disque de stockage** | La taille allouée d’un disque doit être inférieure ou égale à 32 To.<br/><br/> Le nombre de disques attachés à la machine doit être inférieur ou égal à 65, disque du système d’exploitation compris. | Prêt si dans les limites.
**Mise en réseau** | Au maximum 32 cartes réseau doivent être attachées à une machine. | Prêt si dans les limites.

### <a name="guest-operating-system"></a>Système d’exploitation invité
En plus des propriétés des machines virtuelles, Azure Migrate Server Assessment examine le système d’exploitation invité des machines, afin de déterminer si elles peuvent s’exécuter sur Azure.

> [!NOTE]
> Pour les machines virtuelles VMware, Azure Migrate Server Assessment utilise le système d’exploitation spécifié pour la machine virtuelle dans vCenter Server pour effectuer l’analyse du système d’exploitation invité. Pour les machines virtuelles Linux s’exécutant sur VMware, il n’identifie actuellement pas la version exacte du noyau du système d’exploitation invité.

La logique suivante est utilisée par Azure Migrate Server Assessment pour déterminer l’état de préparation Azure en fonction du système d’exploitation.

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
Autres systèmes d’exploitation<br/><br/> Par exemple, Oracle Solaris, Apple Mac OS, FreeBSD, etc. | Azure n’approuve pas ces systèmes d’exploitation. La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est fournie par Azure. | Préparé pour Azure sous condition. Nous vous recommandons d’installer un système d’exploitation pris en charge avant de migrer vers Azure.  
Système d’exploitation spécifié comme **Autre** dans vCenter Server | Azure Migrate ne peut pas identifier le système d’exploitation dans ce cas. | État de la préparation inconnu. Vérifiez que le système d’exploitation en cours d’exécution sur la machine virtuelle est pris en charge dans Azure.
Systèmes d’exploitation 32 bits | La machine peut démarrer dans Azure, mais il est possible qu’Azure ne fournisse pas une prise en charge complète. | Préparé pour Azure sous condition. Envisagez de mettre à niveau le système d’exploitation 32 bits de la machine vers un système d’exploitation 64 bits avant de migrer vers Azure.

## <a name="sizing"></a>Dimensionnement

Une fois qu’une machine est marquée comme prête pour Azure, Server Assessment effectue le dimensionnement, ce qui implique l’identification de la référence SKU de la machine virtuelle et du disque pour la machine virtuelle locale. Les recommandations de taille varient en fonction des propriétés d’évaluation spécifiées.

- Si le dimensionnement utilise le *dimensionnement basé sur les performances*, Azure Migrate prend en compte l’historique des performances de la machine pour identifier la taille et le type de disque de la machine virtuelle dans Azure. Cette méthode est spécialement utile dans les scénarios où vous avez suralloué la machine virtuelle locale, mais où l’utilisation est faible et où vous voulez dimensionner correctement la machine virtuelle dans Azure afin de réduire les coûts. Cette méthode vous permet d’optimiser les tailles lors de la migration.
- Si vous ne voulez pas prendre en compte les données de performances pour le dimensionnement des machines virtuelles et que vous voulez migrer les machines virtuelles telles quelles sur Azure, vous pouvez spécifier le critère de dimensionnement *Localement* : Server Assessment dimensionne alors les machines virtuelles d’après la configuration locale sans prendre en compte les données d’utilisation. Dans ce cas, le dimensionnement du disque est effectué en fonction du type de stockage que vous spécifiez dans les propriétés de l’évaluation (disques HDD Standard, SSD Standard ou Premium).

### <a name="performance-based-sizing"></a>Dimensionnement en fonction des performances

Pour le dimensionnement basé sur les performances, Azure Migrate Server Assessment commence par les disques attachés à la machine virtuelle, poursuit avec les cartes réseau, puis mappe une référence SKU de machine virtuelle Azure en fonction des exigences de calcul de la machine virtuelle locale. L’appliance Azure Migrate profile l’environnement local pour collecter les données de performances du processeur, de la mémoire, des disques et de la carte réseau.

**Collecte des données de performances**

- Pour les machines virtuelles VMware, l’appliance Azure Migrate collecte un point d’échantillonnage en temps réel à des intervalles de 20 secondes ; pour les machines virtuelles Hyper-V, le point d’échantillonnage en temps réel est collecté à des intervalles de 30 secondes.
- L’appliance cumule ensuite les points d’échantillonnage collectés toutes les 10 minutes et envoie la valeur maximale des 10 dernières minutes à Azure Migrate Server Assessment.
- Azure Migrate Server Assessment stocke tous les points d’échantillonnage de 10 minutes pour le dernier mois et, en fonction des propriétés d’évaluation spécifiées pour *Historique des performances* et *Utilisation en centile*, il identifie le point de données approprié qui doit être utilisé pour un dimensionnement correct. Par exemple, si l’historique des performances est défini sur 1 jour et que le centile d’utilisation est le 95e, il utilise les points d’échantillonnage de 10 minutes pour le dernier jour, les trie dans l’ordre croissant, puis sélectionne la valeur du 95e centile pour le dimensionnement correct.
- La valeur ci-dessus est ensuite multipliée par le facteur de confort pour obtenir des données d’utilisation des performances effectives pour chaque métrique (utilisation du processeur, utilisation de la mémoire, IOPS du disque (lecture et écriture), débit du disque (lecture et écriture), débit réseau (entrant et sortant)) collectées par l’appliance.
- Une fois la valeur d’utilisation effective identifiée, le dimensionnement du calcul, du stockage et du réseau s’effectue comme suit :

**Dimensionnement du stockage** : Azure Migrate tente de mapper chaque disque attaché à la machine à un disque dans Azure.

> [!NOTE]
> Azure Migrate : Server Assessment prend en charge seulement les disques managés pour l’évaluation.

  - Les IOPS en lecture et en écriture d’un disque sont additionnés pour obtenir le nombre total d’IOPS nécessaires ; de même, les valeurs du débit en lecture et en écriture sont additionnées pour obtenir le débit total de chaque disque.
  - Si vous avez spécifié le type de stockage en Automatique, en fonction des valeurs effectives de débit et d’IOPS, Azure Migrate Server Assessment détermine ensuite si le disque doit être mappé à un disque HDD Standard, SSD Standard ou Premium dans Azure. Si le type de stockage est défini sur HDD Standard/SSD/Premium, il tente de trouver une référence SKU de disque au sein du type de stockage sélectionné (disques HDD Standard/SSD/Premium).
  - Si Server Assessment ne parvient pas à trouver un disque avec les IOPS et le débit nécessaires, il marque la machine comme ne convenant pas pour Azure.
  - S’il trouve un ensemble de disques adaptés, il sélectionne ceux qui prennent en charge l’emplacement spécifié dans les paramètres d’évaluation.
  - Si plusieurs disques sont éligibles, il sélectionne celui dont le coût est le plus bas.
  - Si les données de performances d’un disque ne sont pas disponibles, les données de configuration du disque (taille du disque) sont utilisées pour rechercher un disque SSD standard dans Azure.

**Dimensionnement du réseau** : Azure Migrate Server Assessment essaie de trouver une machine virtuelle Azure qui peut prendre en charge le nombre de cartes réseau attachées à la machine locale et les performances nécessaires pour ces cartes réseau.
    - Pour obtenir les performances réseau effectives de la machine virtuelle locale, Server Assessment agrège les données transmises par seconde (Mbits/s) à partir de l’ordinateur (données sortantes) pour toutes les cartes réseau, puis applique le facteur de confort. Ce nombre est utilisé pour trouver une machine virtuelle Azure qui prend en charge les performances réseau nécessaires.
    - Outre les performances réseau, il évalue également si la machine virtuelle Azure peut prendre en charge le nombre de cartes réseau requises.
    - Si aucune donnée de performances réseau n’est disponible, seul le nombre de cartes réseau est pris en considération pour le dimensionnement de la machine virtuelle.

**Dimensionnement du calcul** : Une fois les exigences de réseau et de stockage calculées, Azure Migrate Server Assessment prend en compte les exigences pour le processeur et la mémoire afin de trouver une taille de machine virtuelle appropriée dans Azure.
    - Azure Migrate examine les cœurs et la mémoire effectivement utilisés pour rechercher une taille de machine virtuelle appropriée dans Azure.
    - Si aucune taille adaptée n’est trouvée, la machine est marquée comme inadaptée à Azure.
    - Si une taille adaptée est trouvée, Azure Migrate applique les calculs de mise en réseau et de stockage. Il applique ensuite les paramètres d’emplacement et de niveau tarifaire de façon à obtenir la recommandation de taille de machine virtuelle finale.
    - S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.

### <a name="as-on-premises-sizing"></a>Dimensionnement « Localement »

Si vous utilisez de dimensionnement *Localement*, Azure Migrate ne tient pas compte de l’historique des performances des machines virtuelles et des disques, et il alloue une référence SKU de machine virtuelle dans Azure en fonction de la taille allouée localement. De même, pour le dimensionnement du disque, il examine le type de stockage spécifié dans les propriétés de l’évaluation (HDD Standard/SSD/Premium) et recommande le type de disque en conséquence. Le type de stockage par défaut est « Disques Premium ».

## <a name="confidence-ratings"></a>Niveaux de confiance
Chaque évaluation reposant sur les performances dans Azure Migrate est associée à un niveau de confiance, qui va de 1 étoile (le plus faible) à 5 étoiles (le plus élevé).
- Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation.
- Le niveau de confiance d’une évaluation permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate.
- Le niveau de confiance n’est pas applicable aux évaluations locales.
- Pour le dimensionnement basé sur les performances, Azure Migrate Server Assessment a besoin des informations suivantes :
    - Données d’utilisation pour le processeur et la mémoire de la machine virtuelle.
    - Par ailleurs, pour chaque disque attaché à la machine virtuelle, les E/S par seconde du disque et les données de débit sont nécessaires.
    - De même, pour chaque carte réseau attachée à une machine virtuelle, le niveau de confiance a besoin des E/S réseau pour effectuer un dimensionnement basé sur les performances.
    - Si aucune des données d’utilisation chiffrées ci-dessus n’est disponible dans vCenter Server, la recommandation de dimensionnement peut ne pas être fiable.

Selon le pourcentage de points de données disponibles, le niveau de confiance pour l’évaluation est fourni comme suit :

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles

### <a name="low-confidence-ratings"></a>Niveaux de confiance faibles

Voici des raisons pour lesquelles une évaluation pourrait obtenir une note de confiance faible :

- Vous n’avez pas profilé votre environnement pendant la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez l’évaluation avec une durée des performances définie sur 1 jour, vous devez attendre au moins un jour après le démarrage de la découverte pour que tous les points de données soient collectés.
- Des machines virtuelles ont été arrêtées pendant la période pour laquelle l’évaluation est calculée. Si des machines virtuelles ont été mises hors tension pendant un certain temps, Azure Migrate Server Assessment ne peut pas collecter les données de performances pour cette période.
- Des machines virtuelles ont été créées pendant la période de calcul de l’évaluation. Par exemple, si vous créez une évaluation pour l’historique des performances du dernier mois, mais que des machines virtuelles ont été créées dans l’environnement il y a seulement une semaine, l’historique des performances des nouvelles machines virtuelles ne sera pas présent pour la totalité de cette durée.

> [!NOTE]
> Si l’indice de confiance d’une évaluation est inférieur à 5 étoiles, nous vous recommandons d’attendre au moins un jour pour que l’appliance profile l’environnement, puis de recalculer l’évaluation. Si vous ne le faites pas, le dimensionnement basé sur les performances risque de ne pas être fiable, et nous vous recommandons de modifier l’évaluation pour qu’elle utilise le dimensionnement local.

## <a name="monthly-cost-estimation"></a>Estimation des coûts mensuels

Une fois les recommandations de dimensionnement terminées, Azure Migrate calcule les coûts de calcul et de stockage après la migration.

- **Calcul de coût** : à partir de la taille de machine virtuelle Azure recommandée, Azure Migrate utilise l’API de facturation pour calculer le coût mensuel de la machine virtuelle.
    - Le calcul tient compte du système d’exploitation, de Software Assurance, des instances réservées, de la durée de fonctionnement de la machine virtuelle, de l’emplacement et des paramètres de la devise.
    - Il agrège le coût de toutes les machines pour calculer le coût de calcul mensuel total.
- **Coût de stockage** : Le coût de stockage mensuel d’une machine est calculé en additionnant le coût mensuel de tous les disques qui lui sont attachés.
    - Azure Migrate Server Assessment calcule les coûts totaux mensuels du stockage en additionnant les coûts de stockage de toutes les machines.
    - Actuellement, le calcul ne prend pas en compte les offres spécifiées dans les paramètres d’évaluation.

Les coûts sont affichés dans la devise spécifiée dans les paramètres d’évaluation.


## <a name="next-steps"></a>Étapes suivantes

Créer une évaluation pour des [machines virtuelles VMware](tutorial-assess-vmware.md) ou des [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
