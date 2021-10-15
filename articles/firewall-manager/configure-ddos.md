---
title: Configurer un plan de protection Azure DDoS à l’aide d’Azure Firewall Manager
description: Apprenez à utiliser Azure Firewall Manager pour configurer un plan de protection Azure DDoS standard
author: vhorne
ms.author: victorh
ms.service: firewall-manager
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 4ec6c08fab14c2c08ed719d616b84f1a4d744eb9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368314"
---
# <a name="configure-an-azure-ddos-protection-plan-using-azure-firewall-manager-preview"></a>Configurer un plan de protection Azure DDoS à l’aide d’Azure Firewall Manager (préversion)

Azure Firewall Manager est une plateforme qui permet de gérer et de protéger vos ressources réseau à grande échelle. Vous pouvez associer vos réseaux virtuels à un plan de protection DDoS dans Azure Firewall Manager.

> [!IMPORTANT]
> L’utilisation dAzure Firewall Manager pour configurer un plan de protection Azure DDoS est actuellement en PRÉVERSION.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

> [!TIP]
> Actuellement, la Protection DDoS standard ne prend pas en charge les réseaux étendus virtuels. Toutefois, vous pouvez contourner cette limitation en forçant le tunneling du trafic Internet vers un pare-feu Azure dans un réseau virtuel auquel un plan de protection DDoS est associé.

Sous un seul locataire, les plans de protection DDoS peuvent être appliqués à des réseaux virtuels sur plusieurs abonnements. Pour plus d’informations sur les plans de protection DDoS, consultez [Vue d’ensemble du service Azure DDoS Protection standard](../ddos-protection/ddos-protection-overview.md).

Pour voir comment cela fonctionne, vous allez créer une stratégie de pare-feu, puis un réseau virtuel sécurisé avec un pare-feu Azure. Vous allez ensuite créer un plan de protection DDoS, puis l’associer au réseau virtuel.

## <a name="create-a-firewall-policy"></a>Créer une stratégie de pare-feu

Firewall Manager permet de créer une stratégie de pare-feu.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez Firewall Manager.
1. Sélectionnez **Stratégies de Pare-feu Azure**.
1. Sélectionnez **Créer une stratégie de pare-feu Azure**.
1. Pour **Groupe de ressources**, sélectionnez **DDoS-Test-rg**.
1. Sous **Détails de la stratégie**, **Nom**, tapez **fw-pol-01**.
1. Pour **Région**, sélectionnez **USA Ouest 2**.
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).


## <a name="create-a-secured-virtual-network"></a>Créer un réseau virtuel sécurisé

Firewall Manager permet de créer un réseau virtuel sécurisé.

1. Ouvrez Firewall Manager.
1. Sélectionnez **Réseaux virtuels**.
1. Sélectionnez **Créer un réseau virtuel sécurisé**.
1. Pour **Groupe de ressources**, sélectionnez **DDoS-Test-rg**.
1. Pour **Région**, sélectionnez **USA Ouest 2**.
1. Pour **Nom du réseau virtuel hub**, tapez **Hub-vnet-01**.
1. Pour **Plage d’adresses**, tapez **10.0.0.0/16**.
1. Sélectionnez **Suivant : Pare-feu Azure**.
1. Pour **Adresse IP publique**, sélectionnez **Ajouter un nouveau**, tapez le nom **fw-pip** et sélectionn **OK**.
1. Pour **Espace d'adressage de sous-réseau du pare-feu**, tapez **10.0.0.0/24**.
1. Sélectionnez le **fw-pol-01** pour la **stratégie de pare-feu**.
1. Sélectionnez **Suivant : Vérifier + créer**.
1. Sélectionnez **Create** (Créer).

## <a name="create-a-ddos-protection-plan"></a>Créer un plan de protection DDoS

Créez un plan de protection DDoS à l’aide de Firewall Manager. Vous pouvez utiliser la page **Plans de protection DDoS** pour créer et gérer vos plans de protection Azure DDoS.

:::image type="content" source="media/configure-ddos/firewall-ddos.png" alt-text="Capture d’écran de la page Plans de protection DDoS de Firewall Manager":::

1. Ouvrez Firewall Manager.
1. Sélectionnez **Plans de protection DDoS**.
1. Sélectionnez **Create** (Créer).
1. Pour **Groupe de ressources**, sélectionnez **Créer**.
1. Tapez **DDos-Test-rg** pour le nom du groupe de ressources.
1. Sous **Détails de l’instance**,**Nom**, tapez **DDoS-plan-01**.
1. Pour **Région**, sélectionnez **(USA) USA Ouest 2**.
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

## <a name="associate-a-ddos-protection-plan"></a>Associer un plan de protection DDoS

Vous pouvez maintenant associer le plan de protection DDoS au réseau virtuel sécurisé.

1. Ouvrez Firewall Manager.
1. Sélectionnez **Réseaux virtuels**.
1. Cochez la case en regard de **Hub-vnet-01**.
1. Sélectionnez **Gérer la sécurité**, **Ajouter un plan de protection DDoS**.
1. Pour **Protection DDoS standard**, sélectionnez **Activer**.
1. Pour **Plan de protection DDoS**, sélectionnez **DDoS-plan-01**.
1. Sélectionnez **Ajouter**.
1. Une fois le déploiement terminé, sélectionnez **Actualiser**.

Vous devez maintenant voir que le réseau virtuel est associé à un plan de protection DDoS.

:::image type="content" source="media/configure-ddos/ddos-protection.png" alt-text="Capture d’écran montrant le réseau virtuel avec le plan de protection DDoS":::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les plans de protection DDoS, consultez :

- [Vue d’ensemble du service Protection DDos Standard Azure](../ddos-protection/ddos-protection-overview.md)