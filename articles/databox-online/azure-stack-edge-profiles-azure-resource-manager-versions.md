---
title: Versions du profil de l’API du fournisseur de ressources pour Azure Stack Edge | Microsoft Docs
description: Découvrez les versions de l’API Azure Resource Manager prises en charge par les profils dans Azure Stack Edge.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2021
ms.author: alkohli
ms.openlocfilehash: 485b1f8dccf104a9d31abb9aa79fd1ea4eb3940a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965341"
---
# <a name="resource-provider-api-profile-versions-for-azure-stack-edge"></a>Versions du profil de l’API du fournisseur de ressources pour Azure Stack Edge


[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Vous trouverez dans cet article le fournisseur de ressources et le numéro de version de chaque profil d’API utilisé par Azure Stack Edge. Les tableaux de cet article répertorient les versions prises en charge pour chaque fournisseur de ressources et les versions API des profils. Chaque fournisseur de ressources contient un ensemble de types de ressources et de numéros de version spécifiques.

Le profil de l’API utilise trois conventions d’affectation de noms :

- **le plus récent**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**


## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Vue d’ensemble du profil 2019-03-01-hybrid

|Fournisseur de ressources                                   |Version d'API |
|----------------------------------------------------|------------|
|Microsoft.Compute                                   |2017-12-01  |
|Microsoft.Network                                   |2017-10-01<br>La passerelle VPN sera 2017-10-01 |
|Microsoft.Storage (plan de données)                      |2019-07-07  |
|Microsoft.Storage (plan de contrôle)                   |2019-06-01  |
|Microsoft.Resources (Azure Resource Manager lui-même) |2020-06-01  |
|Microsoft.Authorization (opérations de stratégie)         |2016-09-01  |

Pour une liste des versions pour chaque type de ressource pour les fournisseurs dans le profil de l’API, consultez [Détails du profil 2019-03-01-hybrid](/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions?view=azs-2008&preserve-view=true#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Détails du profil 2019-03-01-hybrid

### <a name="microsoftcompute"></a>Microsoft.Compute

Les API Calcul Azure vous donnent un accès par programmation aux machines virtuelles et à leurs ressources de prise en charge. Pour plus d’informations, consultez [Calcul Azure](/rest/api/compute/).

|Type de ressource               |Version d'API |
|----------------------------|------------|
|Emplacements                   |2017-12-01  |
|Emplacements/Tailles de machines virtuelles           |2017-12-01  |
|Machines Virtuelles            |2017-12-01  |
|Machines virtuelles/extensions |2017-12-01  |

### <a name="microsoftnetwork"></a>Microsoft.Network

Le résultat de l’appel des opérations consiste en une représentation de la liste des opérations cloud du réseau disponibles. Pour plus d’informations, consultez [API REST Operation](/rest/api/operation/).

|Types de ressources     |Versions des API|
|-------------------|------------|
|Interfaces réseau |2017-10-01  |
|Virtual Network   |2017-10-01  |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager vous permet de déployer et de gérer l’infrastructure pour vos solutions Azure. Vous organisez les ressources associées dans des groupes de ressources et vous déployez vos ressources avec des modèles JSON. Pour une introduction au déploiement et à la gestion des ressources avec Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/management/overview.md).

|Types de ressources                        |Versions des API|
|--------------------------------------|------------|
|Déploiements                           |2020-06-01  |
|Déploiements/opérations                |2020-06-01  |
|Liens                                 |2020-06-01  |
|Emplacements                             |2020-06-01  |
|Opérations                            |2020-06-01  |
|Fournisseurs                             |2020-06-01  |
|Groupes de ressources                        |2020-06-01  |
|Ressources                             |2020-06-01  |
|Abonnements                         |2018-09-01  |
|Abonnements/emplacements               |2018-09-01  |
|Abonnements/résultats des opérations        |2020-06-01  |
|Abonnements/fournisseurs               |2020-06-01  |
|Abonnements/groupes de ressources          |2020-06-01  |
|Subscriptions/resourceGroups/resources|2020-06-01  |
|Abonnements/ressources               |2020-06-01  |
|Subscriptions/tagNames                |2020-06-01  |
|Subscriptions/tagNames/tagValues      |2020-06-01  |
|Locataires                               |2018-09-01  |

### <a name="microsoftstorage"></a>Microsoft.Storage

Le fournisseur de ressources de stockage (SRP) vous permet de gérer par programmation votre compte de stockage et vos clés. Pour plus d’informations, consultez [Informations de référence sur l’API REST du fournisseur de ressources de stockage Azure](/rest/api/storagerp/).

|Types de ressources       |Versions des API|
|---------------------|------------|
|Vérification de la disponibilité des noms|2019-06-01  |
|Emplacements            |2019-06-01  |
|Emplacements/quotas     |2019-06-01  |
|Opérations           |2019-06-01  |
|StorageAccounts      |2019-06-01  |
|Utilisations               |2019-06-01  |

## <a name="next-steps"></a>Étapes suivantes

- [Gérer un appareil Azure Stack Edge Pro avec GPU via Windows PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md)