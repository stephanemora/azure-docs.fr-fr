---
title: En savoir plus sur les modes d’orchestration pour les groupes de machines virtuelles identiques dans Azure
description: Apprenez-en plus sur les modes d’orchestration pour les groupes de machines virtuelles identiques dans Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 10/23/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 54515b347a95b9315ca9ba87568fb2104c3b2b45
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737000"
---
# <a name="orchestration-modes-preview"></a>Modes d’orchestration (préversion)

> [!CAUTION]
> Merci à tous ceux qui ont participé à cette préversion publique. Nous avons pu recueillir des commentaires précieux de la part de notre communauté. Cette préversion est maintenant **clôturée** pour les nouveaux participants et ce, afin d’intégrer les commentaires. Nous allons mettre à jour cet espace avec les nouvelles informations.

Les groupes identiques de machines virtuelles fournissent un regroupement logique des machines virtuelles gérées par la plateforme. Avec les groupes identiques, vous créez un modèle de configuration de machine virtuelle, ajoutez ou supprimez automatiquement des instances supplémentaires en fonction de la charge de l’UC ou de la mémoire, puis effectuez une mise à niveau automatique vers la dernière version du système d’exploitation. Traditionnellement, les groupes identiques vous permettent de créer des machines virtuelles à l’aide d’un modèle de configuration de machine virtuelle fourni lors de la création d’un groupe identique, et le groupe identique peut uniquement gérer des machines virtuelles qui sont créées implicitement en fonction du modèle de configuration.

Avec le mode d’orchestration du groupe identique (préversion), vous pouvez maintenant choisir si le groupe identique doit orchestrer des machines virtuelles créées explicitement en dehors d’un modèle de configuration de groupe identique, ou des instances de machine virtuelle créées implicitement sur la base du modèle de configuration. Le mode d’orchestration du groupe identique vous aide également à concevoir votre infrastructure de machine virtuelle pour la haute disponibilité en déployant ces machines virtuelles dans des domaines d’erreur et des Zones de disponibilité.


Les groupes de machines virtuelles identiques prendront en charge deux modes d’orchestration distincts :

- ScaleSetVM : les instances de machine virtuelle ajoutées au groupe identique sont basées sur le modèle de configuration de groupe identique. Le cycle de vie de l’instance de machine virtuelle (création, mise à jour, suppression) est géré par le groupe identique.
- Machines virtuelles : les machines virtuelles créées en dehors du groupe identique peuvent être ajoutées explicitement au groupe identique. 
 

> [!IMPORTANT]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement. 
> 
> Cette fonctionnalité des groupes de machines virtuelles identiques est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Modes d’orchestration

|                             | “orchestrationMode” : « VM » (VirtualMachine) | “orchestrationMode” : “ScaleSetVM” (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Modèle de configuration des machines virtuelles      | None                                       | Obligatoire |
| Ajout d’une nouvelle machine virtuelle à un groupe identique  | Les machines virtuelles sont ajoutées explicitement au groupe identique lors de la création de la machine virtuelle. | Les machines virtuelles sont implicitement créées et ajoutées au groupe identique en fonction du modèle de configuration de machine virtuelle, du nombre d’instances et des règles de mise à l’échelle automatique | |
| Supprimer une machine virtuelle                   | Les machines virtuelles doivent être supprimées individuellement, le groupe identique ne sera pas supprimé s’il comporte des machines virtuelles. | Les machines virtuelles peuvent être supprimées individuellement, la suppression du groupe identique entraîne la suppression de toutes les instances de machine virtuelle.  |
| Attacher/Détacher des machines virtuelles           | Non pris en charge                              | Non pris en charge |
| Cycle de vie d’une instance (création par suppression) | Les machines virtuelles et leurs artefacts (tels que les disques et les cartes réseau) peuvent être gérés indépendamment. | Les instances et leurs artefacts (tels que les disques et les cartes réseau) sont implicites pour les instances de groupe identique qui les créent. Ils ne peuvent pas être détachés ou gérés séparément en dehors du groupe identique |
| Domaines d’erreur               | Peut définir les domaines d’erreur. 2 ou 3 en fonction du support régional, et 5 pour la zone de disponibilité. | Peut définir des domaines d’erreur allant de 1 à 5 |
| Domaines de mise à jour              | Les domaines de mise à jour sont automatiquement mappés aux domaines d’erreur | Les domaines de mise à jour sont automatiquement mappés aux domaines d’erreur |
| Zones de disponibilité          | Prend en charge le déploiement régional ou les machines virtuelles dans une zone de disponibilité | Prend en charge le déploiement régional ou plusieurs zones de disponibilité ; peut définir la stratégie d’équilibrage de zone |
| Mise à l'échelle automatique                   | Non pris en charge                              | Prise en charge |
| Mise à niveau du système d’exploitation                  | Non pris en charge                              | Prise en charge |
| Mises à jour de modèle               | Non pris en charge                              | Prise en charge |
| Contrôle d’instance            | Contrôle complet des machines virtuelles. Les machines virtuelles possèdent un URI complet qui prend en charge la gamme complète des fonctionnalités de gestion des machines virtuelles Azure (par exemple, Azure Policy, Sauvegarde Azure et Azure Site Recovery) | Les machines virtuelles sont des ressources dépendantes du groupe identique. Les instances sont accessibles pour la gestion uniquement par le biais du groupe identique. |
| Modèle d’instance              | Définition de modèle Microsoft. Compute/VirtualMachines. | Définition de modèle Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines. |
| Capacité                    | Un groupe identique vide peut être créé ; jusqu’à 200 machines virtuelles peuvent être ajoutées au groupe identique | Les groupes identiques peuvent être définis avec un nombre d’instances compris entre 0 et 1000 |
| Déplacer                        | Prise en charge                                  | Prise en charge |
| Single placement group == false | Non pris en charge                          | Prise en charge |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez [Vue d’ensemble des zones de disponibilité](availability.md).
