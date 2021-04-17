---
title: Déplacer des ressources de mise en réseau Azure vers un nouveau groupe de ressources ou d’abonnements
description: Utilisez Azure Resource Manager pour déplacer des réseaux virtuels et d’autres ressources de mise en réseau vers un nouveau groupe de ressources ou abonnement.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: b7aaf01b696b13136a0f4077f315b137c8917906
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120127"
---
# <a name="move-guidance-for-networking-resources"></a>Conseils sur le déplacement de ressources réseau

Cet article décrit comment déplacer des réseaux virtuels et d’autres ressources de mise en réseau pour des scénarios spécifiques.

## <a name="dependent-resources"></a>Ressources dépendantes

> [!NOTE]
> Notez que les passerelles VPN associées à des adresses IP publiques ne sont pas en mesure de se déplacer actuellement entre des groupes de ressources ou des abonnements.

Lorsque vous déplacez une ressource, vous devez également déplacer ses ressources dépendantes (par exemple, les adresses IP publiques, les passerelles de réseau virtuel, toutes les ressources de connexion associées). Les passerelles de réseau locales peuvent se trouver dans un autre groupe de ressources.

Si vous déplacez une machine virtuelle avec une carte d’interface réseau vers un nouvel abonnement, vous devez déplacer toutes les ressources dépendantes. Déplacer le réseau virtuel de la carte d’interface réseau, toutes les autres cartes d’interface réseau dépendantes du réseau virtuel ainsi que les passerelles VPN.

Pour plus d’informations, consultez [Scénario de déplacement entre des abonnements](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Réseau virtuel appairé

Pour déplacer un réseau virtuel appairé, vous devez d’abord désactiver le peering du réseau virtuel. Une fois l’homologation désactivée, vous pouvez déplacer le réseau virtuel. Après le déplacement, réactivez le peering du réseau virtuel.

## <a name="subnet-links"></a>Liens de sous-réseau

Vous ne pouvez pas déplacer un réseau virtuel vers un autre abonnement s’il contient un sous-réseau avec des liens de navigation dans les ressources. Par exemple, si une ressource de Cache Azure pour Redis est déployée dans un sous-réseau, ce sous-réseau possède un lien de navigation dans les ressources.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../move-resource-group-and-subscription.md).
