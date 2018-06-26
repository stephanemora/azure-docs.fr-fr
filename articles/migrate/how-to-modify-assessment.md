---
title: Personnaliser les paramètres d’évaluation Azure Migrate | Microsoft Docs
description: Décrit comment configurer et exécuter une évaluation de la migration de machines virtuelles VMware vers Azure en utilisant Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/31/2018
ms.author: raynew
ms.openlocfilehash: 73dab9c7eca53ecce44d43a9607fcc7426f9de8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715504"
---
# <a name="customize-an-assessment"></a>Personnaliser une évaluation

[Azure Migrate](migrate-overview.md) crée des évaluations avec des propriétés par défaut. Après avoir créé une évaluation, vous pouvez modifier les propriétés par défaut en suivant les instructions indiquées dans cet article.


## <a name="edit-assessment-properties"></a>Modifier les propriétés d’évaluation

1. Dans la page **Évaluations** du projet de migration, sélectionnez l’évaluation et cliquez sur **Modifier des propriétés**.
2. Modifiez les propriétés conformément au tableau suivant :

    **Paramètre** | **Détails** | **Par défaut**
    --- | --- | ---
    **Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer.<br/><br/> À l’heure actuelle, Azure Migrate prend en charge les 30 régions suivantes : Allemagne Centre, Allemagne Nord-Ouest, Asie de l’Est, Canada Centre, Canada Est, Centre de l’Inde, Chine Est, Chine Nord, Corée Centre, Corée Sud, Est de l’Australie, Europe de l’Ouest, Europe du Nord, Inde de l’Ouest, Inde du Sud, Japon de l’Est, Japon de l’Ouest, Nord du centre des États-Unis, Royaume-Uni Ouest, Royaume-Uni Sud, Sud du Brésil, Sud du centre des États-Unis, Sud-Est asiatique, Sud-Est de l’Australie, US Gov Arizona, US Gov Texas, US Gov Virginie, États-Unis Centre-Ouest, États-Unis Ouest 2, États-Unis de l’Est, États-Unis de l’Est 2, États-Unis de l’Ouest et États-Unis du Centre. |  Ouest des États-Unis 2 est l’emplacement par défaut.
    **Type de stockage** | Vous pouvez spécifier le type de disques que vous souhaitez allouer dans Azure. Cette propriété s’applique lorsque le critère de dimensionnement est en dimensionnement local. Vous pouvez spécifier le type de disque cible en tant que disques managés Premium ou disques managés Standard. Pour le dimensionnement basé sur les performances, la suggestion du disque est automatiquement effectuée selon les données de performances des machines virtuelles. Notez qu’Azure Migrate prend uniquement en charge les disques managés pour l’évaluation de la migration. | La valeur par défaut correspond aux disques managés Premium (avec un critère *Dimensionnement « Localement »*).
    **Critère de dimensionnement** | Critère utilisé par Azure Migrate pour dimensionner correctement les machines virtuelles pour Azure. Vous pouvez effectuer un dimensionnement *en fonction des performances* ou dimensionner les machines virtuelles *comme localement*, sans tenir compte de l’historique des performances. | Le dimensionnement en fonction des performances est l’option par défaut.
    **Historique des performances** | Durée à prendre en compte pour évaluer les performances des machines virtuelles. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*. | La valeur par défaut est une journée.
    **Utilisation en centile** | Valeur de centile du jeu d’échantillons de performances devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*.  | La valeur par défaut est le 95e centile.
    **Séries de machine virtuelle** | Vous pouvez spécifier les séries de machines virtuelles à prendre en compte pour le dimensionnement. Par exemple, si vous disposez d’un environnement de production que vous ne souhaitez pas migrer vers des machines virtuelles de série A dans Azure, vous pouvez exclure la série A de la liste de séries : le dimensionnement sera alors effectué uniquement dans les séries sélectionnées. | Par défaut, toutes les séries de machines virtuelles sont sélectionnées.
    **Niveau tarifaire** | Vous pouvez spécifier le [niveau tarifaire (de base/standard)](../virtual-machines/windows/sizes-general.md) des machines virtuelles Azure cibles. Par exemple, si vous envisagez de migrer un environnement de production, vous pouvez prendre en compte le niveau Standard, qui fournit des machines virtuelles avec une faible latence, mais est sans doute plus coûteux. En revanche, dans un environnement de développement et de test, vous pouvez prendre en compte le niveau de base, qui fournit des machines virtuelles avec une latence plus élevée, moins coûteuses. | Par défaut le niveau [Standard](../virtual-machines/windows/sizes-general.md) est utilisé.
    **Facteur de confort** | Azure Migrate considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs. | Le paramètre par défaut est 1.3x.
    **Offer** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) pour laquelle vous êtes inscrit. | [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) est la valeur par défaut.
    **Devise** | Devise de facturation. | La valeur par défaut est le dollar américain.
    **Remise (%)** | Remise propre à un abonnement cumulable avec l’offre Azure. | Le paramètre par défaut est 0 %.
    **Azure Hybrid Benefit** | Spécifiez si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. | La valeur par défaut est Oui.

3. Cliquez sur **Enregistrer** pour mettre à jour l’évaluation.


## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
