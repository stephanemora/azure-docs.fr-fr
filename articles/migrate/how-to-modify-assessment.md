---
title: Personnaliser les évaluations avec l’évaluation de serveur Azure Migrate | Microsoft Docs
description: Décrit comment personnaliser une évaluation avec l’outil d’évaluation de serveur Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234270"
---
# <a name="customize-an-assessment"></a>Personnaliser une évaluation

Cet article décrit comment personnaliser une évaluation avec l’outil d’évaluation de serveur Azure Migrate.

[Azure Migrate](migrate-services-overview.md) offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure de vos applications et charges de travail locales, ainsi que de machines virtuelles cloud privées/publiques. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.

Vous pouvez utiliser l’outil d’évaluation de serveur Azure Migrate pour créer des évaluations pour les machines virtuelles VMware et les machines virtuelles Hyper-V locales en préparation de la migration vers Azure.

## <a name="about-assessments"></a>À propos des évaluations

Il existe deux types d’évaluations que vous pouvez exécuter avec Azure Migrate Server Assessment.

**Évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les données de performances collectées | **Taille de machine virtuelle recommandée** : En fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> **Type de disque recommandé (disque managé Standard ou Premium)**  : En fonction des IOPS et du débit des disques locaux.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de machine virtuelle recommandée** : En fonction de la taille de machine virtuelle locale<br/><br> **Type de disque recommandé** : En fonction du paramètre de type de stockage que vous sélectionnez pour l’évaluation.


## <a name="how-is-an-assessment-done"></a>Comment sont effectuées les évaluations ?

Les évaluations effectuées dans l’évaluation du serveur Azure Migrate comporte trois étapes. Elle commence par une analyse de l’adéquation, suivie du dimensionnement et pour finir, d’une estimation du coût mensuel. Une machine ne passe au stade suivant que si elle réussit le précédent. Par exemple, si elle rate la vérification d’adéquation Azure, elle est marquée comme inadaptée à Azure, et le dimensionnement et l’évaluation des coûts ne seront pas effectués. [En savoir plus.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Que comprend une évaluation ?

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer.<br/> Server Assessment prend actuellement en charge les régions cibles suivantes : Allemagne Centre, Allemagne Nord-Est, Asie Est, Canada Centre, Canada Est, Inde Centre, Chine Est, Chine Nord, Corée Centre, Corée Sud, Australie Est, Europe Ouest, Europe Nord, Inde Ouest, Inde Sud, Japon Est, Japon Ouest, USA Centre Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Brésil Sud, USA Centre Sud, Asie Sud-Est, Australie Sud-Est, US Gov Arizona, US Gov Texas, US Gov Virginie, USA Centre-Ouest, USA Ouest 2, USA Est, USA Est 2, USA Ouest et USA Centre.
**Type de stockage** | Vous pouvez utiliser cette propriété pour spécifier le type de disques que vous voulez déplacer dans Azure.<br/><br/> Pour un redimensionnement local, vous pouvez spécifier le type de stockage cible comme disques managés Premium, disques SSD Standard managés ou disques HDD Standard managés. Pour un redimensionnement basé sur les performances, vous pouvez spécifier le type de disque cible comme disques automatiques, disques managés Premium, disques managés HDD Standard ou disques managés SSD Standard.<br/><br/> Lorsque vous spécifiez le type de stockage automatique, la recommandation de disque s’appuie sur les données de performances des disques (IOPS et débit). Si vous spécifiez le type de stockage premium/standard, l’évaluation recommandera une référence SKU de disque au sein du type de stockage sélectionné. Si vous voulez obtenir un contrat SLA de machine virtuelle à instance unique de 99,9 %, vous pouvez spécifier le type de stockage comme disques managés Premium. De cette façon, tous les disques de l’évaluation sont recommandés comme disques managés Premium. Azure
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


## <a name="edit-assessment-properties"></a>Modifier les propriétés d’évaluation

Pour modifier les propriétés de l’évaluation après avoir créé une évaluation, procédez comme suit :

1. Dans le projet Azure Migrate, cliquez sur **Serveurs**.
2. Dans **Azure Migrate : évaluation de serveur**, cliquez sur le nombre d’évaluations.
3. Dans **Évaluation**, cliquez sur l’évaluation appropriée > **Modifier les propriétés**.
5. Personnalisez les propriétés d’évaluation conformément au tableau ci-dessus.
6. Cliquez sur **Enregistrer** pour mettre à jour l’évaluation.


Vous pouvez également modifier les propriétés d’évaluation lors de la création de l’évaluation.


## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
