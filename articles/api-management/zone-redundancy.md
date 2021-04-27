---
title: Prise en charge des zones de disponibilité pour la Gestion des API Azure
description: Découvrez comment améliorer la résilience de votre instance de service Gestion des API Azure dans une région en activant la redondance de zone.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558628"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Prise en charge des zones de disponibilité pour la Gestion des API Azure 

Cet article explique comment activer la redondance de zone pour votre instance de Gestion des API à l’aide du portail Azure. La [redondance de zone ](../availability-zones/az-overview.md#availability-zones) offre résilience et haute disponibilité à une instance de service dans une région (emplacement) Azure spécifique. Avec la redondance de zone, la passerelle et le plan de contrôle de votre instance Gestion des API (API de gestion, portail des développeurs, configuration Git) sont répliqués dans les centres de données de zones physiquement séparées, ce qui les rend résilientes à une défaillance de zone. 

La Gestion des API prend également en charge les [déploiements multirégions](api-management-howto-deploy-multi-region.md), ce qui permet de réduire la latence des requêtes perçue par les consommateurs d’API géographiquement répartis et améliore la disponibilité du composant de passerelle si une région passe hors connexion. La combinaison de zones de disponibilité pour la redondance au sein d’une région et du déploiement dans plusieurs régions, pour améliorer la disponibilité de la passerelle en cas de panne régionale, contribue à améliorer la fiabilité et les performances de votre instance Gestion des API.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Régions prises en charge

La configuration de la Gestion des API pour la redondance de zone est actuellement prise en charge dans les régions Azure suivantes.

* Australie Est
* Brésil Sud
* Centre du Canada
* Inde centrale
* USA Est
* USA Est 2
* France Centre
* Japon Est
* États-Unis - partie centrale méridionale
* Asie Sud-Est
* Sud du Royaume-Uni
* USA Ouest 2
* USA Ouest 3

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API](get-started-create-service-instance.md). Sélectionnez le niveau de service Premium.
* Si votre instance Gestion des API est déployée dans un [réseau virtuel](api-management-using-with-vnet.md), veillez à configurer un réseau virtuel, un sous-réseau et une adresse IP publique dans tout nouvel emplacement où vous envisagez d’activer la redondance de zone.

## <a name="enable-zone-redundancy---portal"></a>Activer la redondance de zone - portail

Dans le portail, vous pouvez éventuellement activer la redondance de zone lorsque vous ajoutez un emplacement à votre service Gestion des API ou que vous mettez à jour la configuration d’un emplacement existant.

1. Dans le portail Azure, accédez à votre service Gestion des API, puis sélectionnez **Emplacements** dans le menu.
1. Sélectionnez un emplacement existant ou **+ Ajouter** dans la barre supérieure. L’emplacement doit [prendre en charge les zones de disponibilité](#supported-regions).
1. Sélectionnez le nombre d’ **[Unités](upgrade-and-scale.md)** d’échelle dans l’emplacement.
1. Dans **Zones de disponibilité**, sélectionnez une ou plusieurs zones. Le nombre d’unités sélectionnées doit être réparti uniformément entre les zones de disponibilité. Par exemple, si vous avez sélectionné 3 unités, sélectionnez 3 zones afin que chaque zone héberge une unité.
1. Si l’instance Gestion des API est déployée dans un [réseau virtuel](api-management-using-with-vnet.md), configurez les paramètres de réseau virtuel dans l’emplacement. Sélectionnez un réseau virtuel, un sous-réseau et une adresse IP publique existants qui sont disponibles à l’emplacement.
1. Sélectionnez **Appliquer**, puis **Enregistrer**.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Activer la redondance de zone":::

> [!IMPORTANT]
> L’adresse IP publique dans l’emplacement change quand vous activez, ajoutez ou supprimez des zones de disponibilité. Lors de la mise à jour des zones de disponibilité dans une région avec des paramètres réseau, vous devez configurer une ressource d’adresse IP publique différente de celle que vous avez configurée précédemment.

> [!NOTE]
> L’application de la modification à votre instance Gestion des API peut prendre de 15 à 45 minutes.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [déployer une instance de service Gestion des API Azure dans plusieurs régions Azure](api-management-howto-deploy-multi-region.md).
* Vous pouvez également activer la redondance de zone à l’aide d’un [modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones).
* Pour plus d’informations, consultez [Services Azure prenant en charge les zones de disponibilité](../availability-zones/az-region.md).
* En savoir plus sur la construction de la [fiabilité](/azure/architecture/framework/resiliency/overview) dans Azure.