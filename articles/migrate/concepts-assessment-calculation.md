---
title: Calculs d’évaluations dans Azure Migrate | Microsoft Docs
description: Offre une vue d’ensemble des calculs d’évaluation dans le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 4511c42514a5399d41029b61297bd4c1b0b63d9a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827545"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Calculs d’évaluation dans Azure Migrate

[Azure Migrate](migrate-services-overview.md) fournit un hub central pour effectuer le suivi de la découverte, de l’évaluation et de la migration de vos applications et charges de travail locales. Il effectue également le suivi de vos instances de cloud privées et publiques vers Azure. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.

Server Assessment est un outil d’Azure Migrate qui évalue les serveurs locaux pour la migration vers Azure. Cet article donne des informations sur le calcul des évaluations.

## <a name="whats-in-an-assessment"></a>Que comprend une évaluation ?

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Spécifie l’emplacement Azure vers lequel vous souhaitez effectuer la migration.<br/><br/>Server Assessment prend actuellement en charge les régions cibles suivantes : Allemagne Centre, Allemagne Nord-Est, Asie Est, Canada Centre, Canada Est, Inde Centre, Chine Est, Chine Nord, Corée Centre, Corée Sud, Australie Est, Europe Ouest, Europe Nord, Inde Ouest, Inde Sud, Japon Est, Japon Ouest, USA Centre Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Brésil Sud, USA Centre Sud, Asie Sud-Est, Australie Sud-Est, US Gov Arizona, US Gov Texas, US Gov Virginie, USA Centre-Ouest, USA Ouest 2, USA Est, USA Est 2, USA Ouest et USA Centre.
**Type de stockage** | Spécifie le type de disque que vous souhaitez utiliser pour le stockage dans Azure. <br/><br/> Pour le dimensionnement local, vous pouvez spécifier le type de disque de stockage cible comme géré par Premium, géré par SSD Standard ou géré par HDD Standard. Pour le dimensionnement basé sur les performances, vous pouvez spécifier le type de disque de stockage cible : automatique, géré par Premium, géré par HDD Standard ou géré par SSD Standard. Lorsque vous spécifiez le type de stockage automatique, la suggestion de disque est basée sur les données de performances des disques : les opérations d’entrée/sortie par seconde (IOPS) et le débit. <br/><br/>Si vous spécifiez le type de stockage Premium ou Standard, l’évaluation recommandera une référence SKU de disque au sein du type de stockage sélectionné. Si vous voulez obtenir un contrat SLA de machine virtuelle à instance unique de 99,9 %, vous pouvez spécifier le type de stockage comme disques managés Premium. De cette façon, tous les disques de l’évaluation sont recommandés comme disques managés Premium. Notez qu’Azure Migrate prend uniquement en charge les disques managés pour l’évaluation de la migration.
**Instances réservées (RI)** | Cette propriété vous permet de spécifier des [instances réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/) dans Azure. Les estimations de coût dans l’évaluation prennent alors en compte les remises RI. Les instances réservées ne sont actuellement prises en charge que dans le cadre de l’offre de paiement à l’utilisation d’Azure Migrate.
**Critère de dimensionnement** | Définit les critères à utiliser pour *dimensionner correctement* les machines virtuelles pour Azure. Vous pouvez effectuer un dimensionnement *en fonction des performances* ou dimensionner les machines virtuelles *comme des machines locales* sans tenir compte de l’historique des performances.
**Historique des performances** | Définit la durée à prendre en compte pour évaluer les données de performances des machines. Cette propriété s’applique uniquement lorsque le critère de dimensionnement est *basé sur les performances*.
**Utilisation en centile** | Spécifie la valeur de centile de l’échantillon de performance devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique uniquement lorsque le dimensionnement est basé sur les performances.
**Séries de machine virtuelle** | Vous permet de spécifier la série de machines virtuelles que vous souhaitez prendre en compte pour le dimensionnement adéquat. Par exemple, si vous avez un environnement de production que vous ne prévoyez pas de migrer vers des machines virtuelles de série A dans Azure, vous pouvez exclure une série de la liste ou de la série, et le dimensionnement adéquat est effectué uniquement dans la série sélectionnée.
**Facteur de confort** | Azure Migrate Server Assessment considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs.
**Offer** | Affiche l'[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes inscrit. Azure Migrate estime le coût en conséquence.
**Devise** | Affiche la devise de facturation de votre compte.
**Remise (%)** | Répertorie les remises spécifiques à l’abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
**Durée de fonctionnement de la machine virtuelle** | Si vos machines virtuelles ne s’exécutent pas 24 heures sur 24, 7 jours sur 7 dans Azure, vous pouvez spécifier la durée (nombre de jours par mois et nombre d’heures par jour) pendant lequel elles s’exécutent, et les estimations de coût sont gérées en conséquence. La valeur par défaut est de 31 jours par mois et de 24 heures par jour.
**Azure Hybrid Benefit** | Spécifie si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. Le paramètre par défaut est Oui.

## <a name="how-are-assessments-calculated"></a>Comment les évaluations sont-elles calculées ?

Une évaluation de Azure Migrate Server Assessment est calculée en utilisant les métadonnées collectées sur les serveurs locaux. Le calcul de l’évaluation est géré en trois étapes. Pour chaque serveur, le calcul de l’évaluation commence par une analyse de la pertinence Azure, suivie du dimensionnement et enfin, d’une estimation du coût mensuel. Un serveur se déplace vers une étape ultérieure uniquement s’il passe l’étape précédente avec succès. Par exemple, si un serveur ne parvient pas à vérifier la pertinence d’Azure, il est marqué comme inapproprié pour Azure, et le dimensionnement et l’évaluation des coûts ne sont pas effectués pour ce serveur.

## <a name="azure-suitability-analysis"></a>Analyse d’adéquation Azure

Toutes les machines ne conviennent pas pour s’exécuter dans Azure. L’évaluation de serveur évalue chaque machine locale pour son adéquation à la migration Azure. Il attribue également chaque machine évaluée à l’une des catégories de pertinence suivantes :
- **Disponible pour Azure** : la machine peut être migrée telle quelle vers Azure sans aucune modification. Elle démarrera dans Azure avec une prise en charge complète d’Azure.
- **Préparé pour Azure sous condition**: La machine peut démarrer dans Azure, mais risque de ne pas être totalement prise en charge. Par exemple, un ordinateur qui exécute une version antérieure de Windows Server n’est pas pris en charge dans Azure. Vous devez faire attention avant de migrer ces ordinateurs vers Azure et suivre les instructions de correction suggérées dans l’évaluation pour résoudre les problèmes de disponibilité.
- **Non disponible pour Azure** : La machine ne démarre pas dans Azure. Par exemple, si un disque de plus de 64 téraoctets (To) est attaché à une machine locale, il ne peut pas être hébergé sur Azure. Vous devez suivre les instructions de correction suggérées dans l’évaluation pour résoudre le problème de préparation avant de migrer l’ordinateur vers Azure. Le dimensionnement adéquat et l’estimation du coût ne sont pas effectués pour les machines marquées comme n’étant pas disponibles pour Azure.
- **État de la préparation inconnu** : Azure Migrate n’a pas pu déterminer l’état de préparation de la machine, car les métadonnées collectées dans l’environnement local sont insuffisantes.

Azure Migrate Server Assessment examine les propriétés de l’ordinateur et le système d’exploitation invité pour déterminer la disponibilité Azure de la machine locale.

### <a name="machine-properties"></a>Propriétés de machine virtuelle

Server Assessment examine les propriétés suivantes de la machine virtuelle locale pour déterminer si elle peut s’exécuter sur Azure.

**Propriété** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
**Type de démarrage** | Azure prend en charge les machines virtuelles avec un type de démarrage BIOS, et non UEFI. | Prêt à l’emploi conditionnel si le type de démarrage est UEFI.
**Cœurs** | Le nombre de cœurs des machines doit être inférieur ou égal au nombre maximal de cœurs (128) pris en charge pour une machine virtuelle Azure.<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération les cœurs utilisés pour la comparaison. Si un facteur de confort est spécifié dans les paramètres de l’évaluation, le nombre de cœurs utilisés est multiplié par le facteur de confort.<br/><br/> En l’absence d’historique des performances, Azure Migrate utilise les cœurs alloués, sans appliquer le facteur de confort. | Prêt si inférieur ou égal aux limites.
**Mémoire** | La taille de la mémoire de la machine doit être inférieure ou égale à la mémoire maximale (3892 Go sur la série Azure M Standard_M128m&nbsp;<sup>2</sup>) autorisée pour une machine virtuelle Azure. [Plus d’informations](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération la mémoire utilisée pour la comparaison. Si un facteur de confort est spécifié, la mémoire utilisée est multipliée par le facteur de confort.<br/><br/> En l’absence d’historique, la mémoire allouée est utilisée sans appliquer le facteur de confort.<br/><br/> | Prêt si dans les limites.
**Disque de stockage** | La taille allouée d’un disque doit être inférieure ou égale à 32 To. Bien qu’Azure prenne en charge des disques 64 To avec des disques SSD Ultra, Azure Migrate : Server Assessment vérifie actuellement les 32 To comme limites de taille de disque, car il ne prend pas encore en charge SSD Ultra. <br/><br/> Le nombre de disques attachés à l’ordinateur doit être de 65 ou moins, y compris le disque du système d’exploitation. | Prêt si dans les limites.
**Mise en réseau** | Un ordinateur doit avoir 32 ou moins d’interfaces réseau (NIC) associées. | Prêt si dans les limites.

### <a name="guest-operating-system"></a>Système d’exploitation invité
Outre les propriétés de machine virtuelle, Server Assessment examine le système d’exploitation invité des ordinateurs pour déterminer s’il peut s’exécuter sur Azure.

> [!NOTE]
> Pour les machines virtuelles VMware, Server Assessment utilise le système d’exploitation spécifié pour la machine virtuelle dans vCenter Server pour gérer l’analyse du système d’exploitation invité. Pour les machines virtuelles Linux s’exécutant sur VMware, il n’identifie actuellement pas la version exacte du noyau du système d’exploitation invité.

La logique suivante est utilisée par Server Assessment pour identifier la préparation d’Azure en fonction du système d’exploitation.

**Système d’exploitation** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
Windows Server 2016 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2012 R2 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2012 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2008 R2 avec tous les Service Packs | Azure assure un support complet.| Disponible pour Azure
Windows Server 2008 (32 bits et 64 bits) | Azure assure un support complet. | Disponible pour Azure
Windows Server 2003, 2003 R2 | La date limite de prise en charge de ces systèmes d’exploitation est passée, et leur prise en charge dans Azure nécessite un [Custom Support Agreement (CSA)](https://aka.ms/WSosstatement). | Préparé pour Azure sous condition Envisagez de mettre à niveau le système d’exploitation avant de migrer vers Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | La date de fin de prise en charge de ces systèmes d’exploitation est passée. La machine peut démarrer dans Azure, mais Azure ne fournit aucune prise en charge du système d’exploitation. | Préparé pour Azure sous condition Nous vous recommandons de mettre à niveau le système d’exploitation avant de migrer vers Azure.
Clients Windows 7, 8 et 10 | Azure assure un support avec [abonnement Visual Studio uniquement](https://docs.microsoft.com/azure/virtual-machines/windows/client-images). | Préparé pour Azure sous condition
Windows 10 Pro Desktop | Azure fournit la prise en charge avec les [droits d’hébergement multilocataire](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment). | Préparé pour Azure sous condition
Windows Vista, XP Professionnel | La date de fin de prise en charge de ces systèmes d’exploitation est passée. La machine peut démarrer dans Azure, mais Azure ne fournit aucune prise en charge du système d’exploitation. | Préparé pour Azure sous condition Nous vous recommandons de mettre à niveau le système d’exploitation avant de migrer vers Azure.
Linux | Azure approuve ces [systèmes d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). D’autres systèmes d’exploitation Linux peuvent démarrer dans Azure, mais nous vous recommandons de mettre à niveau le système d’exploitation vers une version approuvée avant la migration vers Azure. | Disponible pour Azure si la version est approuvée.<br/><br/>Préparé pour Azure sous condition si la version n’est pas approuvée.
Autres systèmes d’exploitation<br/><br/> Par exemple, Oracle Solaris, Apple Mac OS, FreeBSD, etc. | Azure n’approuve pas ces systèmes d’exploitation. La machine peut démarrer dans Azure, mais Azure ne fournit aucune prise en charge du système d’exploitation. | Préparé pour Azure sous condition Nous vous recommandons d’installer un système d’exploitation pris en charge avant de migrer vers Azure.  
Système d’exploitation spécifié comme **Autre** dans vCenter Server | Azure Migrate ne peut pas identifier le système d’exploitation dans ce cas. | État de la préparation inconnu. Vérifiez que le système d’exploitation en cours d’exécution sur la machine virtuelle est pris en charge dans Azure.
Systèmes d’exploitation 32 bits | La machine peut démarrer dans Azure, mais Azure peut ne pas fournir une prise en charge complète. | Préparé pour Azure sous condition Envisagez de mettre à niveau le système d'exploitation 32 bits de la machine vers un système d'exploitation 64 bits avant la migration vers Azure

## <a name="sizing"></a>Dimensionnement

Une fois qu’une machine est marquée comme prête pour Azure, Server Assessment effectue des suggestions de dimensionnement, ce qui implique l’identification de la machine virtuelle Azure et de la référence SKU de disque pour la machine virtuelle locale. Ces suggestions varient en fonction des propriétés d’évaluation spécifiées.

- Si le dimensionnement utilise le *dimensionnement basé sur les performances*, Azure Migrate prend en compte l’historique des performances de la machine pour identifier la taille et le type de disque de la machine virtuelle dans Azure. Cette méthode est particulièrement utile si vous avez dépassé l’allocation de la machine virtuelle locale, mais que l’utilisation est faible et que vous souhaitez dimensionner correctement la machine virtuelle dans Azure pour réduire les coûts. Cette méthode vous permet d’optimiser les tailles lors de la migration.
- Si vous ne souhaitez pas prendre en compte les données de performances pour le dimensionnement des machines virtuelles et que vous souhaitez prendre les machines locales telles quelles dans Azure, vous pouvez définir les critères de dimensionnement de la même façon que *localement*. Ensuite, Server Assessment dimensionnera les machines virtuelles en fonction de la configuration locale sans tenir compte des données d’utilisation. Dans ce cas, les activités de dimensionnement de disque sont basées sur le type de stockage que vous spécifiez dans les propriétés d’évaluation (HDD Standard, SSD Standard ou disques Premium).

### <a name="performance-based-sizing"></a>Dimensionnement en fonction des performances

Pour le dimensionnement basé sur les performances, Server Assessment commence par les disques attachés à la machine virtuelle, suivis par les cartes réseau. Il mappe ensuite une référence SKU de machine virtuelle Azure en fonction des exigences de calcul de la machine virtuelle locale. L’appliance Azure Migrate profile l’environnement local pour collecter les données de performances du processeur, de la mémoire, des disques et de la carte réseau.

**Étapes de la collecte des données de performances :**

1. Pour les machines virtuelles VMware, l’appliance de Azure Migrate collecte un point d’échantillonnage en temps réel à chaque intervalle de 20 secondes. Pour les machines virtuelles Hyper-V, le point d’échantillonnage en temps réel est collecté à chaque intervalle de 30 secondes.
1. L’appliance cumule les points d’échantillonnage collectés toutes les 10 minutes et envoie la valeur maximale des 10 dernières minutes à Server Assessment.
1. Server Assessment stocke tous les points d’échantillonnage de 10 minutes pour le dernier mois. Ensuite, selon les propriétés d’évaluation spécifiées pour l’*historique des performances* et l’*utilisation du centile*, il identifie le point de données approprié à utiliser pour le dimensionnement adéquat. Par exemple, si l’historique des performances est défini sur 1 jour et que l’utilisation du centile est le 95e centile, Server Assessment utilise les points d’échantillonnage de 10 minutes pour le dernier jour, les trie dans l’ordre croissant et sélectionne la valeur du 95e centile pour le dimensionnement adéquat.
1. Cette valeur est multipliée par le facteur de confort pour obtenir les données d’utilisation des performances effectives pour chaque indicateur de performance (utilisation du processeur, utilisation de la mémoire, IOPS du disque (lecture et écriture), débit du disque (lecture et écriture) et débit réseau (entrée et sortie) collectées par l’appliance.

Une fois la valeur d’utilisation effective déterminée, le stockage, le réseau et le dimensionnement du calcul sont gérés comme suit.

**Dimensionnement du stockage** : Azure Migrate tente de mapper chaque disque attaché à la machine à un disque dans Azure.

> [!NOTE]
> Azure Migrate Server Assessment ne prend en charge que les disques managés à des fins d’évaluation.

  - Server Assessment ajoute les IOPS en lecture et en écriture d’un disque pour obtenir le nombre total d’IOPS requis. De même, il ajoute les valeurs de débit de lecture et d’écriture pour obtenir le débit total de chaque disque.
  - Si vous avez spécifié le type de stockage sur Automatique, en fonction des valeurs de débit et d’IOPS effectives, Server Assessment détermine si le disque doit être mappé à un disque dur standard, un disque SSD standard ou un disque Premium dans Azure. Si le type de stockage est défini sur HDD Standard/SSD/Premium, Server Assessment tente de trouver une référence (SKU) de disque au sein du type de stockage sélectionné (disques HDD Standard/SSD/Premium).
  - Si Server Assessment ne parvient pas à trouver un disque avec les IOPS et le débit nécessaires, il marque la machine comme ne convenant pas pour Azure.
  - Si Server Assessment trouve un ensemble de disques adaptés, il sélectionne ceux qui prennent en charge l’emplacement spécifié dans les paramètres d’évaluation.
  - S’il existe plusieurs disques éligibles, Server Assessment sélectionne le disque dont le coût est le plus faible.
  - Si les données de performances d’un disque ne sont pas disponibles, les données de configuration du disque (taille du disque) sont utilisées pour rechercher un disque SSD standard dans Azure.

**Dimensionnement du réseau** : Server Assessment essaie de trouver une machine virtuelle Azure qui peut prendre en charge le nombre de cartes réseau attachées à la machine locale et les performances nécessaires pour ces cartes réseau.
- Pour obtenir les performances réseau effectives de la machine virtuelle locale, Server Assessment agrège les données transmises par seconde (Mbits/s) à partir de l’ordinateur (données sortantes) pour toutes les cartes réseau, puis applique le facteur de confort. Il utilise ce nombre pour trouver une machine virtuelle Azure qui peut prendre en charge les performances réseau requises.
- Outre les performances du réseau, Server Assessment détermine également si la machine virtuelle Azure peut prendre en charge le nombre de cartes réseau requis.
- Si aucune donnée de performances réseau n’est disponible, Server Assessment considère uniquement le nombre de cartes réseau pour le dimensionnement de la machine virtuelle.

**Dimensionnement du calcul** : Après avoir calculé les exigences de stockage et de réseau, Server Assessment prend en compte les besoins en processeur et en mémoire pour trouver une taille de machine virtuelle appropriée dans Azure.
- Azure Migrate examine les cœurs et la mémoire effectivement utilisés pour rechercher une taille de machine virtuelle appropriée dans Azure.
- Si aucune taille adaptée n’est trouvée, la machine est marquée comme inadaptée à Azure.
- Si une taille adaptée est trouvée, Azure Migrate applique les calculs de mise en réseau et de stockage. Il applique ensuite les paramètres d’emplacement et de niveau tarifaire de façon à obtenir la suggestion de taille de machine virtuelle finale.
- S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.

### <a name="as-on-premises-sizing"></a>Dimensionnement « Localement »

Si vous utilisez *en tant que dimensionnement local*, Server Assessment ne prend pas en compte l’historique des performances des machines virtuelles et des disques. Au lieu de cela, il alloue une référence SKU de machine virtuelle dans Azure en fonction de la taille allouée localement. De même que pour le dimensionnement du disque, Server Assessment examine le type de stockage spécifié dans les propriétés d’évaluation (HDD Standard/SSD/Premium) et recommande le type de disque en conséquence. Le type de stockage par défaut est « Disques Premium ».

## <a name="confidence-ratings"></a>Niveaux de confiance
Chaque évaluation reposant sur les performances dans Azure Migrate est associée à un niveau de confiance, qui va de 1 étoile (le plus faible) à 5 étoiles (le plus élevé).
- Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation.
- Le niveau de confiance d’une évaluation permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate.
- Les évaluations de confiance ne s’appliquent pas aux évaluations *locales*.
- Pour le dimensionnement basé sur les performances, Server Assessment a besoin :
    - Données d’utilisation pour le processeur et la mémoire de la machine virtuelle.
    - Les données IOPS et de débit du disque pour chaque disque attaché à la machine virtuelle.
    - Les E/S réseau pour gérer le dimensionnement basé sur les performances pour chaque carte réseau attachée à une machine virtuelle.

   Si l’un de ces numéros d’utilisation n’est pas disponible dans vCenter Server, la suggestion de taille peut ne pas être fiable.

Selon le pourcentage de points de données disponibles, le niveau de confiance de l’évaluation se présente comme suit.

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0-20 % | 1 étoile
   21-40 % | 2 étoiles
   41-60 % | 3 étoiles
   61-80 % | 4 étoiles
   81-100 % | 5 étoiles

### <a name="low-confidence-ratings"></a>Niveaux de confiance faibles

Voici quelques raisons pour lesquelles une évaluation peut avoir un niveau de confiance faible :

- Vous n’avez pas profilé votre environnement pendant la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez l’évaluation avec une durée des performances définie sur 1 jour, vous devez attendre au moins un jour après le démarrage de la découverte pour que tous les points de données soient collectés.
- Certaines machines virtuelles ont été arrêtées pendant la période de calcul de l’évaluation. Si des machines virtuelles sont désactivées pendant une certaine durée, Server Assessment ne peut pas collecter les données de performances pour cette période.
- Certaines machines virtuelles ont été créées au cours de la période pour laquelle l’évaluation a été calculée. Par exemple, si vous avez créé une évaluation de l’historique des performances du mois précédent, mais que certaines machines virtuelles ont été créées dans l’environnement qu’il y a une semaine, l’historique des performances des nouvelles machines virtuelles n’existera pas pour la durée totale.

> [!NOTE]
> Si le niveau de confiance d’une évaluation est inférieur à cinq étoiles, nous vous recommandons d’attendre au moins un jour pour que l’appliance profile l’environnement, puis de recalculer l’évaluation. À défaut, il se peut que le dimensionnement basé sur les performances ne soit pas fiable. Dans ce cas, nous vous recommandons de faire passer l’évaluation au dimensionnement local.

## <a name="monthly-cost-estimation"></a>Estimation des coûts mensuels

Une fois les recommandations de dimensionnement terminées, Azure Migrate calcule les coûts de calcul et de stockage après la migration.

- **Calcul de coût** : à partir de la taille de machine virtuelle Azure recommandée, Azure Migrate utilise l’API de facturation pour calculer le coût mensuel de la machine virtuelle.
    - Le calcul tient compte du système d’exploitation, de Software Assurance, des instances réservées, de la durée de fonctionnement de la machine virtuelle, de l’emplacement et des paramètres de la devise.
    - Il agrège le coût de toutes les machines pour calculer le coût de calcul mensuel total.
- **Coût de stockage** : Le coût de stockage mensuel d’une machine est calculé en additionnant le coût mensuel de tous les disques attachés à la machine, comme suit :
    - Server Assessment calcule les coûts totaux mensuels du stockage en additionnant les coûts de stockage de toutes les machines.
    - Actuellement, le calcul ne prend pas en compte les offres spécifiées dans les paramètres d’évaluation.

Les coûts sont affichés dans la devise spécifiée dans les paramètres d’évaluation.


## <a name="next-steps"></a>Étapes suivantes

Créer une évaluation pour des [machines virtuelles VMware](tutorial-assess-vmware.md) ou des [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
