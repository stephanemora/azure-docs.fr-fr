---
title: Personnaliser les paramètres d’évaluation Azure Migrate | Microsoft Docs
description: Décrit comment configurer et exécuter une évaluation de la migration de machines virtuelles VMware vers Azure en utilisant Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200307"
---
# <a name="customize-an-assessment"></a>Personnaliser une évaluation

[Azure Migrate](migrate-overview.md) crée des évaluations avec des propriétés par défaut. Après avoir créé une évaluation, vous pouvez modifier les propriétés par défaut en suivant les instructions indiquées dans cet article.


## <a name="edit-assessment-properties"></a>Modifier les propriétés d’évaluation

1. Dans la page **Évaluations** du projet de migration, sélectionnez l’évaluation et cliquez sur **Modifier des propriétés**.
2. Personnalisez les propriétés d’évaluation basées sur les détails suivants :

    **Paramètre** | **Détails** | **Par défaut**
    --- | --- | ---
    **Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer.<br/><br/> À l’heure actuelle, Azure Migrate prend en charge les 30 régions suivantes : Allemagne Centre, Allemagne Nord-Ouest, Asie Est, Canada Centre, Canada Est, Inde Centre, Chine Est, Chine Nord, Corée Centre, Corée Sud, Australie Est, Europe Ouest, Europe Nord, Inde Ouest, Inde Sud, Japon Est, Japon Ouest, USA Centre Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Brésil Sud, USA Centre Sud, Asie Sud-Est, Australie Sud-Est, US Gov Arizona, US Gov Texas, US Gov Virginie, USA Centre-Ouest, USA Ouest 2, USA Est, USA Est 2, USA Ouest et USA Centre. |  USA Ouest 2 est l’emplacement par défaut.
    **Type de stockage** | Vous pouvez utiliser cette propriété pour spécifier le type de disques que vous souhaitez déplacer dans Azure. Pour un redimensionnement local, vous pouvez spécifier le type de disque cible en tant que disques managés Premium ou disques managés Standard. Pour un redimensionnement basé sur les performances, vous pouvez spécifier le type de disque cible en tant que disques managés automatiques, Premium ou disques managés Standard. Lorsque vous spécifiez le type de stockage automatique, la recommandation de disque s’appuie sur les données de performances des disques (IOPS et débit). Par exemple, si vous voulez obtenir un [contrat SLA de machine virtuelle à instance unique de 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), vous pouvez spécifier le type de stockage comme disques managés Premium. De cette façon, tous les disques de l’évaluation sont recommandés comme disques managés Premium. Notez qu’Azure Migrate prend uniquement en charge les disques managés pour l’évaluation de la migration. | La valeur par défaut correspond aux disques managés Premium (avec *Localement* comme critère de dimensionnement).
    **Instances réservées** |  Vous pouvez aussi spécifier si vous disposez d’[instances réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/) dans Azure ; Azure Migrate estimera le coût en fonction. Les instances réservées ne sont actuellement prises en charge que dans le cadre de l’offre de paiement à l’utilisation d’Azure Migrate. | La valeur par défaut de cette propriété correspond à des instances réservées de 3 ans.
    **Critère de dimensionnement** | Critère utilisé par Azure Migrate pour dimensionner correctement les machines virtuelles pour Azure. Vous pouvez effectuer un dimensionnement *en fonction des performances* ou dimensionner les machines virtuelles *comme localement*, sans tenir compte de l’historique des performances. | Le dimensionnement en fonction des performances est l’option par défaut.
    **Historique des performances** | Durée à prendre en compte pour évaluer les performances des machines virtuelles. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*. | La valeur par défaut est une journée.
    **Utilisation en centile** | Valeur de centile du jeu d’échantillons de performances devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*.  | La valeur par défaut est le 95e centile.
    **Séries de machine virtuelle** | Vous pouvez spécifier les séries de machines virtuelles à prendre en compte pour le dimensionnement. Par exemple, si vous disposez d’un environnement de production que vous ne souhaitez pas migrer vers des machines virtuelles de série A dans Azure, vous pouvez exclure la série A de la liste des séries : le dimensionnement voulu est alors effectué uniquement dans les séries sélectionnées. | Par défaut, toutes les séries de machines virtuelles sont sélectionnées.
    **Facteur de confort** | Azure Migrate considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs. | Le paramètre par défaut est 1.3x.
    **Offer** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) pour laquelle vous êtes inscrit. | [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) est la valeur par défaut.
    **Devise** | Devise de facturation. | La valeur par défaut est le dollar américain.
    **Remise (%)** | Remise propre à un abonnement cumulable avec l’offre Azure. | Le paramètre par défaut est 0 %.
    **Durée de fonctionnement de la machine virtuelle** | Si vos machines virtuelles ne s’exécutent pas 24/7 dans Azure, vous pouvez spécifier leur durée d’exécution (nombre de jours par mois et nombre d’heures par jour) ; l’estimation des coûts est calculée en fonction. | La valeur par défaut est de 31 jours par mois et de 24 heures par jour.
    **Azure Hybrid Benefit** | Spécifiez si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. | La valeur par défaut est Oui.

3. Cliquez sur **Enregistrer** pour mettre à jour l’évaluation.

## <a name="faqs-on-assessment-properties"></a>Questions fréquentes (FAQ) sur les propriétés d’évaluation

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Quelle différence y a-t-il entre le dimensionnement local et le dimensionnement basé sur les performances ?

Lorsque vous spécifiez le critère de dimensionnement sur le dimensionnement local, Azure Migrate ne tient pas compte des données de performances des machines virtuelles et dimensionne ces machines virtuelles en fonction de la configuration locale. Si le critère de dimensionnement est basé sur les performances, le dimensionnement est effectué en fonction des données d’utilisation. Par exemple, s’il existe une machine virtuelle locale avec 4 cœurs et 8 Go de mémoire, présentant 50 % d’utilisation du processeur et 50 % d’utilisation de la mémoire. Si le critère de dimensionnement est défini localement, il est recommandé d’utiliser une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire. Cependant, si le critère de dimensionnement est défini en fonction des performances, il est recommandé d’utiliser une référence SKU de machine virtuelle avec 2 cœurs et 4 Go de mémoire, car le pourcentage d’utilisation est pris en compte lors de la recommandation de la taille.

De même, pour les disques, le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage. Si le critère de dimensionnement est défini en fonction des performances et que le type de stockage est automatique, les IOPS et les valeurs de débit du disque sont prises en compte pour identifier le type de disque cible (Standard ou Premium). Si le critère de dimensionnement est défini en fonction des performances tandis que le type de stockage est Premium, il est recommandé d’utiliser un disque Premium, la référence SKU du disque Premium dans Azure est sélectionnée en fonction de la taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque lorsque le critère de dimensionnement est défini localement et que le type de stockage est Standard ou Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Quel est l’impact de l’utilisation de l’historique des performances et des centiles sur les suggestions de taille ?

Ces propriétés sont uniquement applicables pour le dimensionnement en fonction des performances. Azure Migrate collecte l’historique des performances des machines locales et l’utilise pour recommander la taille de la machine virtuelle et le type de disque dans Azure.

- L’appliance collecteur profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes.
- L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes. Pour créer le point de données unique, l’appliance sélectionne la valeur maximale de tous les échantillons de 20 secondes, puis l’envoie à Azure.
- Lorsque vous créez une évaluation dans Azure, en fonction de la durée des performances et de la valeur de centile de l’historique des performances, Azure Migrate calcule la meilleure valeur d’utilisation et l’utilise pour le dimensionnement.

Par exemple, si vous avez défini la durée des performances à 1 jour et la valeur de centile à 95 centiles, Azure Migrate utilise des points d’échantillonnage de 15 minutes envoyés par le collecteur pour le dernier jour, les trie dans l’ordre croissant et récupère la valeur du 95e centile en tant que meilleure valeur d’utilisation. Le 95e centile permet de s’assurer que vous ignorez les aberrations, celles-ci pouvant apparaître si vous choisissez le 99e centile. Si vous désirez choisir l’utilisation maximale de la période et que vous ne souhaitez pas manquer les aberrations, vous devriez sélectionner le 99e centile.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
