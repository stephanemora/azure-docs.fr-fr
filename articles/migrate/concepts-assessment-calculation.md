---
title: Évaluations dans Azure Migrate
description: En savoir plus sur les évaluations dans Azure Migrate.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 5fc61d9987c9e728a5d83cb3ab3f91b8e8f5f740
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76833326"
---
# <a name="about-assessments-in-azure-migrate"></a>À propos des évaluations dans Azure Migrate

Cet article explique comment les évaluations sont calculées dans [Azure Migrate : Évaluation de serveur](migrate-services-overview.md#azure-migrate-server-assessment-tool). Vous exécutez des évaluations sur des groupes de machines locales pour déterminer si elles sont prêtes pour la migration vers Azure Migrate.

## <a name="how-do-i-run-an-assessment"></a>Comment exécuter une évaluation ?
Vous pouvez exécuter une évaluation à l’aide d’Azure Migrate : Server Assessment ou d’un autre outil Azure ou tiers. Après avoir créé un projet Azure Migrate, vous ajoutez l’outil dont vous avez besoin. [En savoir plus](how-to-add-tool-first-time.md)

### <a name="collect-compute-data"></a>Collecter des données de calcul

Les données de performances pour les paramètres de calcul sont collectées comme suit :

1. L’[appliance Azure Migrate](migrate-appliance.md) collecte un point d’échantillonnage en temps réel :

    - **Machines virtuelles VMware** : Pour les machines virtuelles VMware, l’appliance de Azure Migrate collecte un point d’échantillonnage en temps réel à chaque intervalle de 20 secondes.
    - **Machines virtuelles Hyper-V** : Pour les machines virtuelles Hyper-V, le point d’échantillonnage en temps réel est collecté à chaque intervalle de 30 secondes.
    - **Serveurs physiques** : Pour les serveurs physiques, le point d’échantillonnage en temps réel est collecté à chaque intervalle de cinq minutes. 
    
2. L’appliance cumule les points d’échantillonnage (20 secondes, 30 secondes, cinq minutes) pour créer un point de données unique toutes les 10 minutes. Pour créer le point de données unique, l’appliance sélectionne la valeur maximale de tous les échantillons, puis l’envoie à Azure.
3. Server Assessment stocke tous les points d’échantillonnage de 10 minutes pour le dernier mois.
4. Lorsque vous créez une évaluation, Server Assessment identifie le point de données approprié à utiliser pour le dimensionnement adéquat, en fonction des valeurs de centile pour l’*historique des performances* et l’*utilisation du centile*.

    - Par exemple, si l’historique des performances est défini sur une semaine et que l’utilisation du centile est le 95e centile, Server Assessment trie les points d’échantillonnage de 10 minutes pour la dernière semaine dans l’ordre croissant et sélectionne la valeur du 95e centile pour le dimensionnement adéquat. 
    - Le 95e centile vous permet d’ignorer les valeurs hors norme qui peuvent être incluses quand vous choisissez le 99e centile.
    - Si vous souhaitez choisir l’utilisation maximale de la période et si vous ne souhaitez pas ignorer les valeurs hors norme, vous devez sélectionner le 99e centile comme utilisation en centile.

5. Cette valeur est multipliée par le facteur de confort pour obtenir les données d’utilisation des performances effectives pour chaque indicateur de performance (utilisation du processeur, utilisation de la mémoire, IOPS du disque (lecture et écriture), débit du disque (lecture et écriture) et débit réseau (entrée et sortie) collectées par l’appliance.

Pour exécuter des évaluations dans Server Assessment, vous préparez l’évaluation locale et dans Azure, et vous configurez l’appliance Azure Migrate pour découvrir en permanence les machines locales. Une fois les machines découvertes, vous pouvez les rassembler dans des groupes pour les évaluer. Pour obtenir des évaluations plus précises et hautement fiables, vous pouvez visualiser et mapper les dépendances entre les machines afin de déterminer comment les migrer.

- En savoir plus sur l’exécution d’évaluations pour les [machines virtuelles VMware](tutorial-prepare-vmware.md), les [machines virtuelles Hyper-V](tutorial-prepare-hyper-v.md) et les [serveurs physiques](tutorial-prepare-physical.md).
- En savoir plus sur l’évaluation de serveurs [importés avec un fichier CSV](tutorial-assess-import.md).
- En savoir plus sur la configuration de la [visualisation des dépendances](concepts-dependency-visualization.md).

## <a name="assessments-in-server-assessment"></a>Évaluations dans Server Assessment 

Les évaluations que vous créez avec Azure Migrate Server Assessment sont une capture instantanée de données à un moment donné. L’outil Server Assessment fournit deux types d’évaluations.

**Type d’évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations qui donnent des recommandations en fonction des données de performances collectées | Les recommandations concernant les machines virtuelles sont fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> Les recommandations concernant le type de disque (HDD/SSD ou disque managé premium) sont fonction de l’IOPS et du débit des disques locaux.
**Telle quelle locale** | Évaluations qui n’utilisent pas de données de performances pour formuler des recommandations. | Les recommandations concernant la taille des machines virtuelles sont basées sur la taille de la machines virtuelle locale.<br/><br> Le type de disque recommandé est basé sur le type de stockage sélectionné pour l’évaluation.

## <a name="collecting-performance-data"></a>Collecte de données de performances

Les données de performances sont collectées comme suit :

1. L’[appliance Azure Migrate](migrate-appliance.md) collecte un point d’échantillonnage en temps réel :

    - **Machines virtuelles VMware** : Pour les machines virtuelles VMware, l’appliance de Azure Migrate collecte un point d’échantillonnage en temps réel à chaque intervalle de 20 secondes.
    - **Machines virtuelles Hyper-V** : Pour les machines virtuelles Hyper-V, le point d’échantillonnage en temps réel est collecté à chaque intervalle de 30 secondes.
    - **Serveurs physiques** : Pour les serveurs physiques, le point d’échantillonnage en temps réel est collecté à chaque intervalle de cinq minutes. 
    
2. L’appliance cumule les points d’échantillonnage (20 secondes, 30 secondes, cinq minutes) pour créer un point de données unique toutes les 10 minutes. Pour créer le point de données unique, l’appliance sélectionne la valeur maximale de tous les échantillons, puis l’envoie à Azure.
3. Server Assessment stocke tous les points d’échantillonnage de 10 minutes pour le dernier mois.
4. Lorsque vous créez une évaluation, Server Assessment identifie le point de données approprié à utiliser pour le dimensionnement adéquat, en fonction des valeurs de centile pour l’*historique des performances* et l’*utilisation du centile*.

    - Par exemple, si l’historique des performances est défini sur une semaine et que l’utilisation du centile est le 95e centile, Server Assessment trie les points d’échantillonnage de 10 minutes pour la dernière semaine dans l’ordre croissant et sélectionne la valeur du 95e centile pour le dimensionnement adéquat. 
    - Le 95e centile vous permet d’ignorer les valeurs hors norme qui peuvent être incluses quand vous choisissez le 99e centile.
    - Si vous souhaitez choisir l’utilisation maximale de la période et si vous ne souhaitez pas ignorer les valeurs hors norme, vous devez sélectionner le 99e centile comme utilisation en centile.

5. Cette valeur est multipliée par le facteur de confort pour obtenir les données d’utilisation des performances effectives pour chaque indicateur de performance (utilisation du processeur, utilisation de la mémoire, IOPS du disque (lecture et écriture), débit du disque (lecture et écriture) et débit réseau (entrée et sortie) collectées par l’appliance.
## <a name="whats-in-an-assessment"></a>Que comprend une évaluation ?

Voici ce qui est inclus dans une évaluation dans Azure Migrate : Server Assessment.

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Emplacement vers lequel vous souhaitez effectuer la migration. Server Assessment prend actuellement en charge les régions Azure cibles suivantes :<br/><br/> Allemagne Centre, Allemagne Nord-Est, Asie Est, Canada Centre, Canada Est, Inde Centre, Chine Est, Chine Nord, Corée Centre, Corée Sud, Australie Est, Europe Ouest, Europe Nord, Inde Ouest, Inde Sud, Japon Est, Japon Ouest, USA Centre Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Brésil Sud, USA Centre Sud, Asie Sud-Est, Australie Sud-Est, US Gov Arizona, US Gov Texas, US Gov Virginie, USA Centre-Ouest, USA Ouest 2, USA Est, USA Est 2, USA Ouest et USA Centre.
*Disque de stockage cible (dimensionnement tel quel)* * | Type de disque à utiliser pour le stockage dans Azure. <br/><br/> Spécifiez le disque de stockage cible en tant que disque managé Premium, disque SSD managé standard ou HDD managé standard.
**Disque de stockage cible (dimensionnement basé sur les performances)** | Spécifiez le disque de stockage cible en tant que disque automatique, managé Premium, HDD managé standard ou disque SDD managé standard.<br/><br/> **Automatique** : La recommandation de disque est basée sur les données de performances des disques (les opérations d’entrée/sortie par seconde (IOPS) et le débit).<br/><br/>**Premium/standard** :  L’évaluation recommande une référence SKU de disque au sein du type de stockage sélectionné.<br/><br/> Si vous souhaitez obtenir un contrat de niveau de service de machine virtuelle à une seule instance de 99,9 %, envisagez d’utiliser des disques managés Premium. De cette façon, tous les disques de l’évaluation sont recommandés comme disques managés Premium.<br/><br/> Azure Migrate prend uniquement en charge les disques managés pour l’évaluation de la migration.
**Instances réservées (RI)** | Spécifiez des [instances réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/) dans Azure de sorte que les estimations de coût dans l’évaluation prennent en compte les remises RI.<br/><br/> Les instances réservées ne sont actuellement prises en charge que dans le cadre de l’offre de paiement à l’utilisation d’Azure Migrate.
**Critère de dimensionnement** | Utilisé pour dimensionner de manière adéquate la machine virtuelle dans Azure.<br/><br/> Utilisez le dimensionnement tel quel ou le dimensionnement basé sur les performances.
**Historique des performances** | Utilisé avec le dimensionnement basé sur les performances. Spécifiez la durée utilisée lors de l’évaluation des données de performances.
**Utilisation en centile** | Utilisé avec le dimensionnement basé sur les performances. Spécifie la valeur de centile de l’échantillon de performances à utiliser pour le dimensionnement adéquat. 
**Séries de machine virtuelle** | Spécifiez la série de machines virtuelles que vous souhaitez prendre en compte pour le dimensionnement adéquat. Par exemple, si vous ne disposez pas d’un environnement de production nécessitant des machines virtuelles de série A dans Azure, vous pouvez exclure la série A de la liste ou de la série.
**Facteur de confort** | Mémoire tampon utilisée pendant l’évaluation. Appliqué en plus des données d’utilisation d’ordinateur pour les machines virtuelles (UC, mémoire, disque et réseau). Il prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à deux cœurs. Avec un facteur de confort de 2.0x, le résultat est une machine virtuelle à quatre cœurs.
**Offer** | Affiche l’[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes inscrit. Server Assessment estime le coût en conséquence.
**Devise** | Devise de facturation de votre compte.
**Remise (%)** | Répertorie les remises spécifiques propres à un abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
**Durée de fonctionnement de la machine virtuelle** | Si les machines virtuelles Azure ne fonctionnent pas 24 heures sur 24, 7 jours sur 7, vous pouvez spécifier la durée (jours par mois et heures par jour) pendant laquelle elles fonctionneront. Les estimations de coût sont gérées en conséquence.<br/><br/> La valeur par défaut est de 31 jours par mois et de 24 heures par jour.
**Azure Hybrid Benefit** | Spécifie si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui (par défaut), les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows.

[Passez en revue les meilleures pratiques](best-practices-assessment.md) pour la création d’une évaluation avec Server Assessment.

## <a name="how-are-assessments-calculated"></a>Comment les évaluations sont-elles calculées ? 

Les évaluations dans Azure Migrate : Server Assessment sont calculées en utilisant les métadonnées collectées sur les machines locales. Si vous exécutez une évaluation sur des machines importées à l’aide d’un fichier .CSV, vous fournissez les métadonnées pour le calcul. Les calculs se font en trois étapes :

1. **Calculer l’état de préparation pour Azure** : Évaluez si les machines sont adaptées à la migration vers Azure.
2. **Calculer les recommandations de dimensionnement** : Estimez le dimensionnement du calcul, du stockage et du réseau. 
2. **Calculer les coûts mensuels** : Calculez les coûts mensuels de calcul et de stockage estimés pour l’exécution des machines dans Azure après la migration.

Les calculs se font dans cet ordre, et une machine serveur ne passe à une étape ultérieure que s’il passe l’étape précédente avec succès. Par exemple, si un serveur ne parvient pas à vérifier l’état de préparation pour Azure, il est marqué comme inapproprié pour Azure, et le dimensionnement et l’évaluation des coûts ne sont pas effectués pour ce serveur.



## <a name="calculate-readiness"></a>Calculer l’état de préparation

Toutes les machines ne conviennent pas pour s’exécuter dans Azure. Server Assessment évalue chaque machine locale et lui attribue une catégorie d’état de préparation. 
- **Disponible pour Azure** : la machine peut être migrée telle quelle vers Azure sans aucune modification. Elle démarrera dans Azure avec une prise en charge complète d’Azure.
- **Préparé pour Azure sous condition**: La machine peut démarrer dans Azure, mais risque de ne pas être totalement prise en charge. Par exemple, un ordinateur qui exécute une version antérieure de Windows Server n’est pas pris en charge dans Azure. Vous devez faire preuve de vigilance avant de migrer ces machines vers Azure. Suivez les instructions de correction suggérées dans l’évaluation afin de résoudre les problèmes d’état de préparation.
- **Non disponible pour Azure** : La machine ne démarrera pas dans Azure. Par exemple, si un disque de machine locale a une capacité supérieure à 64 To, il ne peut pas être hébergé sur Azure. Suivez les instructions de correction afin de résoudre le problème avant la migration. 
- **État de la préparation inconnu** : Azure Migrate n’a pas pu déterminer l’état de préparation d’une machine en raison de métadonnées insuffisantes.

Pour calculer l’état de préparation, Server Assessment examine les propriétés de la machine et les paramètres du système d’exploitation résumés dans les tableaux suivants. 

### <a name="machine-properties"></a>Propriétés de machine virtuelle

Server Assessment examine les propriétés suivantes de la machine virtuelle locale pour déterminer si elle peut s’exécuter sur Azure.

**Propriété** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
**Type de démarrage** | Azure prend en charge les machines virtuelles avec un type de démarrage BIOS, et non UEFI. | Prêt à l’emploi conditionnel si le type de démarrage est UEFI.
**Cœurs** | Le nombre de cœurs des machines doit être inférieur ou égal au nombre maximal de cœurs (128) pris en charge pour une machine virtuelle Azure.<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération les cœurs utilisés pour la comparaison. Si un facteur de confort est spécifié dans les paramètres de l’évaluation, le nombre de cœurs utilisés est multiplié par le facteur de confort.<br/><br/> En l’absence d’historique des performances, Azure Migrate utilise les cœurs alloués, sans appliquer le facteur de confort. | Prêt si inférieur ou égal aux limites.
**Mémoire** | La taille de la mémoire de la machine doit être inférieure ou égale à la mémoire maximale (3892 Go sur la série Azure M Standard_M128m&nbsp;<sup>2</sup>) autorisée pour une machine virtuelle Azure. [Plus d’informations](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération la mémoire utilisée pour la comparaison. Si un facteur de confort est spécifié, la mémoire utilisée est multipliée par le facteur de confort.<br/><br/> En l’absence d’historique, la mémoire allouée est utilisée sans appliquer le facteur de confort.<br/><br/> | Prêt si dans les limites.
**Disque de stockage** | La taille allouée d’un disque doit être inférieure ou égale à 32 To. Bien qu’Azure prenne en charge des disques de 64 To avec des disques SSD Ultra, Azure Migrate : Server Assessment vérifie actuellement les 32 To comme limites de taille de disque, car il ne prend pas encore en charge les SSD Ultra. <br/><br/> Le nombre de disques attachés à l’ordinateur doit être de 65 ou moins, y compris le disque du système d’exploitation. | Prêt si dans les limites.
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

## <a name="calculate-sizing-as-is-on-premises"></a>Calculer le dimensionnement (local tel quel)

Une fois qu’une machine est marquée comme prête pour Azure, Server Assessment fait des recommandations de dimensionnement afin d’identifier la machine virtuelle Azure et la référence SKU de disque. Si vous utilisez le dimensionnement tel quel, Server Assessment ne prend pas en compte l’historique des performances des machines virtuelles et des disques.

**Dimensionnement du calcul** : Il alloue une référence SKU de machine virtuelle Azure basée sur la taille allouée localement.
**Dimensionnement du stockage/disque** : Server Assessment examine le type de stockage spécifié dans les propriétés d’évaluation (HDD Standard/SSD/Premium) et recommande le type de disque en conséquence. Le type de stockage par défaut est « Disques Premium ».
**Dimensionnement du réseau** : Server Assessment prend en compte la carte réseau sur la machine locale.


## <a name="calculate-sizing-performance-based"></a>Calculer le dimensionnement (basé sur les performances)

Une fois qu’une machine est marquée comme prête pour Azure, si vous utilisez le dimensionnement basé sur les performances, Server Assessment fait les recommandations de dimensionnement suivantes :

- Server Assessment prend en compte l’historique des performances de la machine afin d’identifier la taille de la machine virtuelle et le type de disque dans Azure.
- Si des serveurs ont été importés à l’aide d’un fichier CSV, les valeurs que vous spécifiez sont utilisées. Cette méthode est particulièrement utile dans les scénarios où vous avez suralloué la machine locale, où l’utilisation est faible et où vous voulez dimensionner correctement la machine virtuelle dans Azure afin de réduire les coûts. 
- Si vous ne souhaitez pas utiliser les données de performances, réinitialisez les critères de dimensionnement sur un dimensionnement local tel quel, comme décrit dans la section précédente.

### <a name="calculate-storage-sizing"></a>Calculer le dimensionnement du stockage

Pour le dimensionnement du stockage, Azure Migrate tente de mapper chaque disque attaché à la machine à un disque dans Azure, et procède comme suit :

1. Server Assessment ajoute les IOPS en lecture et en écriture d’un disque pour obtenir le nombre total d’IOPS requis. De même, il ajoute les valeurs de débit de lecture et d’écriture pour obtenir le débit total de chaque disque.
2. Si vous avez spécifié le type de stockage sur Automatique, en fonction des valeurs de débit et d’IOPS effectives, Server Assessment détermine si le disque doit être mappé à un HDD standard, un disque SSD standard ou un disque Premium dans Azure. Si le type de stockage est défini sur HDD Standard/SSD/Premium, Server Assessment tente de trouver une référence (SKU) de disque au sein du type de stockage sélectionné (disques HDD Standard/SSD/Premium).
3. Les disques sont sélectionnés comme suit :
    - Si Server Assessment ne parvient pas à trouver un disque avec les IOPS et le débit nécessaires, il marque la machine comme ne convenant pas pour Azure.
    - Si Server Assessment trouve un ensemble de disques adaptés, il sélectionne ceux qui prennent en charge l’emplacement spécifié dans les paramètres d’évaluation.
    - S’il existe plusieurs disques éligibles, Server Assessment sélectionne le disque dont le coût est le plus faible.
    - Si les données de performances d’un disque ne sont pas disponibles, les données de configuration du disque (taille du disque) sont utilisées pour rechercher un disque SSD standard dans Azure.

### <a name="calculate-network-sizing"></a>Calculer le dimensionnement du réseau

Server Assessment essaie de trouver une machine virtuelle Azure qui peut prendre en charge le nombre de cartes réseau attachées à la machine locale et les performances nécessaires pour ces cartes réseau.
- Pour obtenir les performances réseau effectives de la machine virtuelle locale, Server Assessment agrège les données transmises par seconde (Mbits/s) à partir de l’ordinateur (données sortantes) pour toutes les cartes réseau, puis applique le facteur de confort. Il utilise ce nombre pour trouver une machine virtuelle Azure qui peut prendre en charge les performances réseau requises.
- Outre les performances du réseau, Server Assessment détermine également si la machine virtuelle Azure peut prendre en charge le nombre de cartes réseau requis.
- Si aucune donnée de performances réseau n’est disponible, Server Assessment considère uniquement le nombre de cartes réseau pour le dimensionnement de la machine virtuelle.


### <a name="calculate-compute-sizing"></a>Calculer le dimensionnement du calcul

Après avoir calculé les exigences de stockage et de réseau, Server Assessment prend en compte les besoins en processeur et en mémoire pour trouver une taille de machine virtuelle appropriée dans Azure.
- Azure Migrate examine les cœurs et la mémoire effectivement utilisés pour rechercher une taille de machine virtuelle appropriée dans Azure.
- Si aucune taille adaptée n’est trouvée, la machine est marquée comme inadaptée à Azure.
- Si une taille adaptée est trouvée, Azure Migrate applique les calculs de mise en réseau et de stockage. Il applique ensuite les paramètres d’emplacement et de niveau tarifaire de façon à obtenir la suggestion de taille de machine virtuelle finale.
- S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.


### <a name="calculate-confidence-ratings"></a>Calculer les niveaux de confiance

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

> [!NOTE]
> Les niveaux de confiance ne sont pas attribués aux évaluations des serveurs importés à l’aide d’un fichier CSV dans Azure Migrate. 

#### <a name="low-confidence-ratings"></a>Niveaux de confiance faibles

Voici quelques raisons pour lesquelles une évaluation peut avoir un niveau de confiance faible :

- Vous n’avez pas profilé votre environnement pendant la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez l’évaluation avec une durée des performances définie sur 1 jour, vous devez attendre au moins un jour après le démarrage de la découverte pour que tous les points de données soient collectés.
- Certaines machines virtuelles ont été arrêtées pendant la période de calcul de l’évaluation. Si des machines virtuelles sont désactivées pendant une certaine durée, Server Assessment ne peut pas collecter les données de performances pour cette période.
- Certaines machines virtuelles ont été créées au cours de la période pour laquelle l’évaluation a été calculée. Par exemple, si vous avez créé une évaluation de l’historique des performances du mois précédent, mais que certaines machines virtuelles ont été créées dans l’environnement qu’il y a une semaine, l’historique des performances des nouvelles machines virtuelles n’existera pas pour la durée totale.

> [!NOTE]
> Si le niveau de confiance d’une évaluation est inférieur à cinq étoiles, nous vous recommandons d’attendre au moins un jour pour que l’appliance profile l’environnement, puis de recalculer l’évaluation. À défaut, il se peut que le dimensionnement basé sur les performances ne soit pas fiable. Dans ce cas, nous vous recommandons de faire passer l’évaluation au dimensionnement local.

## <a name="calculate-monthly-costs"></a>Calculer les coûts mensuels

Une fois les recommandations de dimensionnement terminées, Azure Migrate calcule les coûts de calcul et de stockage après la migration.

- **Calcul de coût** : à partir de la taille de machine virtuelle Azure recommandée, Azure Migrate utilise l’API de facturation pour calculer le coût mensuel de la machine virtuelle.
    - Le calcul tient compte du système d’exploitation, de Software Assurance, des instances réservées, de la durée de fonctionnement de la machine virtuelle, de l’emplacement et des paramètres de la devise.
    - Il agrège le coût de toutes les machines pour calculer le coût de calcul mensuel total.
- **Coût de stockage** : Le coût de stockage mensuel d’une machine est calculé en additionnant le coût mensuel de tous les disques attachés à la machine, comme suit :
    - Server Assessment calcule les coûts totaux mensuels du stockage en additionnant les coûts de stockage de toutes les machines.
    - Actuellement, le calcul ne prend pas en compte les offres spécifiées dans les paramètres d’évaluation.

Les coûts sont affichés dans la devise spécifiée dans les paramètres d’évaluation.


## <a name="next-steps"></a>Étapes suivantes

[Passez en revue](best-practices-assessment.md) les meilleures pratiques pour la création d’évaluations. 
