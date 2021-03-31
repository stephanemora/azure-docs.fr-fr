---
title: Personnaliser les évaluations pour Azure Migrate | Microsoft Docs
description: Décrit comment personnaliser des évaluations crées avec Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785204"
---
# <a name="customize-an-assessment"></a>Personnaliser une évaluation

Cet article décrit comment personnaliser des évaluations créées par l’outil de découverte et d’évaluation d’Azure Migrate.

[Azure Migrate](migrate-services-overview.md) offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure de vos applications et charges de travail locales, ainsi que de machines virtuelles cloud privées/publiques. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.

L’outil de découverte et d’évaluation d’Azure Migrate permet de créer des évaluations pour des machines virtuelles VMware et Hyper-V locales afin de les préparer pour une migration vers Azure. L’outil de découverte et d’évaluation évalue des serveurs locaux en vue de leur migration vers des machines virtuelles IaaS Azure et Azure VMware Solution (AVS). 

## <a name="about-assessments"></a>À propos des évaluations

Les évaluations que vous créez avec l’outil de découverte et d’évaluation sont une capture instantanée de données à un point dans le temps. Il existe deux types d’évaluations que vous pouvez créer à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.

**Type d’évaluation** | **Détails**
--- | --- 
**Microsoft Azure** | Évaluations pour migrer vos serveurs locaux vers des machines virtuelles Azure. <br/><br/> Vous pouvez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md), [machines virtuelles Hyper-V](how-to-set-up-appliance-hyper-v.md) et [serveurs physiques](how-to-set-up-appliance-physical.md) locaux pour la migration vers Azure à l’aide de ce type d’évaluation.(concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Évaluations pour migrer vos serveurs locaux vers [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Vous pouvez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md) locales pour la migration vers Azure VMware Solution (AVS) avec ce type d’évaluation. [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md)

Options de critères de dimensionnement dans les évaluations d’Azure Migrate :

**Critère de dimensionnement** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations qui donnent des recommandations en fonction des données de performances collectées | **Évaluation des machines virtuelles Azure** : Les recommandations concernant les machines virtuelles sont fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> Les recommandations concernant le type de disque (HDD/SSD ou disque managé premium) sont fonction de l’IOPS et du débit des disques locaux.<br/><br/>**Évaluation d’Azure SQL** : la configuration d’Azure SQL est basée sur les données de performances d’instances et de bases de données SQL, à savoir : utilisation du processeur, utilisation de la mémoire, IOPS (fichiers de données et fichiers journaux), débit et latence des opérations d’E/S.<br/><br/>**Évaluation d’Azure VMware Solution (AVS)**  : Les suggestions concernant les nœuds AVS sont basées sur les données d’utilisation du processeur et de la mémoire.
**Telle quelle locale** | Évaluations qui n’utilisent pas de données de performances pour formuler des recommandations. | **Évaluation des machines virtuelles Azure** : Les recommandations concernant la taille des machines virtuelles sont basées sur la taille de la machines virtuelle locale.<br/><br> Le type de disque recommandé est basé sur ce que vous sélectionnez dans le paramètre type de stockage pour l’évaluation.<br/><br/> **Évaluation d’Azure VMware Solution (AVS)**  : Les suggestions concernant les nœuds AVS sont basées sur la taille des machines virtuelles locales.

## <a name="how-is-an-assessment-done"></a>Comment sont effectuées les évaluations ?

Une évaluation dans l’outil de découverte et d’évaluation d’Azure Migrate est effectuée en trois étapes. Elle commence par une analyse de l’adéquation, suivie du dimensionnement et pour finir, d’une estimation du coût mensuel. Une machine ne passe au stade suivant que si elle réussit le précédent. Par exemple, si elle rate la vérification d’adéquation Azure, elle est marquée comme inadaptée à Azure, et le dimensionnement et l’évaluation des coûts ne seront pas effectués. [En savoir plus.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Qu’est-ce qu’une évaluation de machine virtuelle Azure ?

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer.<br/> L’évaluation de machine virtuelle Azure prend actuellement en charge les régions cibles suivantes : Allemagne Centre, Allemagne Nord-Est, Asie Est, Canada Centre, Canada Est, Inde Centre, Chine Est, Chine Nord, Corée Centre, Corée Sud, Australie Est, Europe Ouest, Europe Nord, Inde Ouest, Inde Sud, Japon Est, Japon Ouest, USA Centre Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Brésil Sud, USA Centre Sud, Asie Sud-Est, Australie Sud-Est, US Gov Arizona, US Gov Texas, US Gov Virginie, USA Centre-Ouest, USA Ouest 2, USA Est, USA Est 2, USA Ouest et USA Centre.
**Type de stockage** | Vous pouvez utiliser cette propriété pour spécifier le type de disques que vous voulez déplacer dans Azure.<br/><br/> Pour un redimensionnement local, vous pouvez spécifier le type de stockage cible comme disques managés Premium, disques SSD Standard managés ou disques HDD Standard managés. Pour un redimensionnement basé sur les performances, vous pouvez spécifier le type de disque cible comme disques automatiques, disques managés Premium, disques managés HDD Standard ou disques managés SSD Standard.<br/><br/> Lorsque vous spécifiez le type de stockage automatique, la recommandation de disque s’appuie sur les données de performances des disques (IOPS et débit). Si vous spécifiez le type de stockage premium/standard, l’évaluation recommandera une référence SKU de disque au sein du type de stockage sélectionné. Si vous voulez obtenir un contrat SLA de machine virtuelle à instance unique de 99,9 %, vous pouvez spécifier le type de stockage comme disques managés Premium. De cette façon, tous les disques de l’évaluation sont recommandés comme disques managés Premium. Azure
**Instances réservées** | Cette propriété vous aide à spécifier si vous avez des [instances réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/) dans Azure : les estimations de coût de l’évaluation sont alors effectuées en tenant compte des remises des instances réservées. Les instances réservées ne sont actuellement prises en charge que dans le cadre de l’offre de paiement à l’utilisation d’Azure Migrate.
**Critère de dimensionnement** | Critère utilisé pour dimensionner correctement les machines virtuelles pour Azure. Vous pouvez effectuer un dimensionnement *en fonction des performances* ou dimensionner les machines virtuelles *comme des machines locales* sans tenir compte de l’historique des performances.
**Historique des performances** | Durée à prendre en compte pour évaluer les données de performances des machines. Cette propriété s’applique seulement quand le critère de dimensionnement est *basé sur les performances*.
**Utilisation en centile** | Valeur de centile du jeu d’échantillons de performances devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique seulement quand le critère de dimensionnement est le *basé sur les performances*.
**Séries de machine virtuelle** |     Vous pouvez spécifier les séries de machines virtuelles à prendre en compte pour le dimensionnement. Par exemple, si vous disposez d’un environnement de production que vous ne souhaitez pas migrer vers des machines virtuelles de série A dans Azure, vous pouvez exclure la série A de la liste des séries : le dimensionnement voulu est alors effectué uniquement dans les séries sélectionnées.
**Facteur de confort** | Une évaluation de machine virtuelle Azure considère une mémoire tampon (facteur de confort). Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs.
**Offer** | L’[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes inscrit. Azure Migrate estime le coût en conséquence.
**Devise** | Devise de facturation.
**Remise (%)** | Remise propre à un abonnement cumulable avec l’offre Azure.<br/> Le paramètre par défaut est 0 %.
**Durée de fonctionnement de la machine virtuelle** | Si vos machines virtuelles ne s’exécutent pas 24/7 dans Azure, vous pouvez spécifier leur durée d’exécution (nombre de jours par mois et nombre d’heures par jour) ; l’estimation des coûts est calculée en fonction.<br/> La valeur par défaut est de 31 jours par mois et de 24 heures par jour.
**Azure Hybrid Benefit** | Spécifiez si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. La valeur par défaut est Oui.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Qu’est-ce qu’une évaluation Azure VMware Solution (AVS) ?

Voici le contenu d’une évaluation AVS :


| **Propriété** | **Détails** |
| - | - |
| **Emplacement cible** | Spécifie l’emplacement du cloud privé AVS vers lequel vous souhaitez effectuer la migration.<br/><br/> Une évaluation AVS prend actuellement en charge les régions cibles suivantes : USA Est, Europe Ouest, USA Ouest. |
| **Type de stockage** | Spécifie le moteur de stockage à utiliser dans AVS.<br/><br/> Notez que les évaluations AVS prennent uniquement en charge le vSAN comme type de stockage par défaut. |
**Instances réservées (RI)** | Cette propriété vous permet de spécifier des instances réservées dans AVS. Les instances réservées non prises en charge pour les nœuds AVS. |
**Type de nœud** | Spécifie le [type de nœud AVS](../azure-vmware/concepts-private-clouds-clusters.md) utilisé pour mapper les machines virtuelles locales. Notez que le type de nœud par défaut est AV36. <br/><br/> Azure Migrate recommandera un nombre requis de nœuds pour les machines virtuelles à migrer vers AVS. |
**Paramètre FTT, niveau RAID** | Spécifie les combinaisons de Failure to Tolerate (Échec à tolérer) et RAID applicables. L’option FTT sélectionnée associée à la configuration de disque de machine virtuelle locale détermine le stockage vSAN total requis dans AVS. |
**Critère de dimensionnement** | Définit les critères à utiliser pour _dimensionner correctement_ les machines virtuelles pour AVS. Vous pouvez effectuer un dimensionnement _en fonction des performances_ ou dimensionner les machines virtuelles _comme des machines locales_ sans tenir compte de l’historique des performances. |
**Historique des performances** | Définit la durée à prendre en compte pour évaluer les données de performances des machines. Cette propriété s’applique uniquement lorsque le critère de dimensionnement est _basé sur les performances_. |
**Utilisation en centile** | Spécifie la valeur de centile de l’échantillon de performance devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique uniquement lorsque le dimensionnement est basé sur les performances.|
**Facteur de confort** | Azure Migrate considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs. |
**Offer** | Affiche l'[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes inscrit. Azure Migrate estime le coût en conséquence.|
**Devise** | Affiche la devise de facturation de votre compte. |
**Remise (%)** | Répertorie les remises spécifiques à l’abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %. |
**Azure Hybrid Benefit** | Spécifie si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Bien que cela n’ait aucun impact sur les prix des solutions VMware Azure en raison du prix basé sur les nœuds, les clients peuvent toujours appliquer leurs licences de système d’exploitation locales (basées sur Microsoft) dans AVS à l’aide d’Azure Hybrid Benefits. D’autres fournisseurs de systèmes d’exploitation logiciels devront fournir leurs propres termes de licence, tels que RHEL, par exemple. |
**Surabonnement pour les processeurs virtuels** | Spécifie le rapport entre le nombre de cœurs virtuels lié à 1 cœur physique dans le nœud AVS. La valeur par défaut dans les calculs est de 4 processeurs virtuels : 1 cœur physique dans AVS. <br/><br/> Les utilisateurs de l’API peuvent définir cette valeur comme un entier. Notez qu’un surabonnement des processeurs virtuels > 4:1 peut commencer à entraîner une dégradation des performances, mais peut être utilisé pour les charges de travail de type serveur web. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Quelles sont les propriétés utilisées pour créer et personnaliser une évaluation Azure SQL ?

Voici ce qu’incluent les propriétés d’évaluation d’Azure SQL :

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Région Azure vers laquelle vous souhaitez opérer la migration. Les recommandations en matière de configuration et de coût d’Azure SQL sont basées sur l’emplacement que vous spécifiez.
**Type de déploiement cible** | Type de déploiement cible sur lequel vous souhaitez exécuter l’évaluation : <br/><br/> Sélectionnez **Recommandé** si vous souhaitez qu’Azure Migrate évalue l’état de préparation de vos serveurs SQL pour la migration vers Azure SQL MI et Azure SQL DB, et recommande l’option de déploiement cible, le niveau cible, la configuration d’Azure SQL et les estimations mensuelles les mieux adaptés.<br/><br/>Sélectionnez **Azure SQL DB**, si vous souhaitez évaluer vos serveurs SQL pour la migration uniquement vers des bases de données Azure SQL, et examiner le niveau cible, la configuration d’Azure SQL DB et les estimations mensuelles.<br/><br/>Sélectionnez **Azure SQL MI**, si vous souhaitez évaluer vos serveurs SQL pour la migration uniquement vers des bases de données Azure SQL, et examiner le niveau cible, la configuration d’Azure SQL MI et les estimations mensuelles.
**Capacité réservée** | Spécifie une capacité de réserve afin que les estimations de coût dans l’évaluation la prenne en compte.<br/><br/> Si vous sélectionnez une option capacité de réserve, vous ne pouvez pas spécifier de « Remise (%) ».
**Critère de dimensionnement** | Cette propriété permet de dimensionner correctement la configuration d’Azure SQL. <br/><br/> Par défaut, elle est **basée sur les performances**, ce qui signifie que l’évaluation collecte les métriques de performances des instances et des bases de données SQL Server afin de recommander une Azure SQL Managed Instance de taille optimale et/ou un niveau ou une configuration d’Azure SQL Database.
**Historique des performances** | L’historique des performances spécifie la durée utilisée lors de l’évaluation des données de performances.
**Utilisation en centile** | L’utilisation en centile spécifie la valeur de centile de l’échantillon de performances utilisé pour le dimensionnement adéquat.
**Facteur de confort** | Mémoire tampon utilisée pendant l’évaluation. Il prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une instance à 10 cœurs avec 20 % d’utilisation correspond normalement à une instance à deux cœurs. Avec un facteur de confort de 2.0, le résultat est plutôt une instance à quatre cœurs.
**Programme d’offre/de licences** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) dans laquelle vous êtes inscrit. Pour le moment, vous ne pouvez choisir qu’entre le paiement à l’utilisation et le Dev/Test de paiement à l’utilisation. Notez que vous pouvez bénéficier d’une remise supplémentaire en demandant une capacité de réserve et Azure Hybrid Benefit en plus de l’offre de paiement à l’utilisation.
**Niveau de service** | Option de niveau de service la plus appropriée en fonction des besoins de votre entreprise pour la migration vers Azure SQL Database et/ou Azure SQL Managed Instance.<br/><br/>**Recommandé** si vous souhaitez qu’Azure Migrate recommande le niveau de service le plus adapté à vos serveurs. Ce peut être Usage général ou Critique pour l’entreprise. <br/><br/> **Usage général** si vous souhaitez une configuration d’Azure SQL conçue pour des charges de travail axées sur le budget. [En savoir plus](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Critique pour l’entreprise** si vous souhaitez une configuration d’Azure SQL conçue pour des charges de travail à faible latence avec une haute résilience aux échecs et aux basculements rapides. [En savoir plus](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Devise** | Devise de facturation de votre compte.
**Remise (%)** | Toute remise propre à un abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
**Azure Hybrid Benefit** | Spécifie si vous disposez déjà d’une licence SQL Server. <br/><br/> Si c’est le cas et si vous êtes couvert par une Software Assurance active des abonnements SQL Server, vous pouvez demander Azure Hybrid Benefit au moment d’importer les licences dans Azure.

## <a name="edit-assessment-properties"></a>Modifier les propriétés d’évaluation

Pour modifier les propriétés de l’évaluation après avoir créé une évaluation, procédez comme suit :

1. Dans le projet Azure Migrate, cliquez sur **Serveurs**.
2. Dans **Azure Migrate : découverte et évaluation**, cliquez sur le nombre d’évaluations.
3. Dans **Évaluation**, cliquez sur l’évaluation appropriée > **Modifier les propriétés**.
5. Personnalisez les propriétés d’évaluation conformément aux tableaux ci-dessus.
6. Cliquez sur **Enregistrer** pour mettre à jour l’évaluation.


Vous pouvez également modifier les propriétés d’évaluation lors de la création de l’évaluation.


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](concepts-assessment-calculation.md) sur le mode de calcul des évaluations de machines virtuelles Azure.
- [En savoir plus](concepts-azure-sql-assessment-calculation.md) sur le mode de calcul des évaluations d’Azure SQL.
- [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md) sur le mode de calcul des évaluations AVS.

