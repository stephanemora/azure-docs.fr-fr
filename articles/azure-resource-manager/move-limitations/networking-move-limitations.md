---
title: Déplacer des ressources de mise en réseau Azure vers un nouveau groupe de ressources ou abonnement | Microsoft Docs
description: Utilisez Azure Resource Manager pour déplacer des réseaux virtuels et d’autres ressources de mise en réseau vers un nouveau groupe de ressources ou abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626118"
---
# <a name="move-guidance-for-networking-resources"></a>Conseils sur le déplacement de ressources réseau

Cet article décrit comment déplacer des réseaux virtuels et d’autres ressources de mise en réseau pour des scénarios spécifiques.

## <a name="dependent-resources"></a>Ressources dépendantes

Lors de la migration d’un réseau virtuel, vous devez également migrer ses ressources dépendantes. Pour les passerelles VPN, vous devez déplacer les adresses IP, les passerelles de réseau virtuel et toutes les ressources de connexion associées. Les passerelles de réseau locales peuvent se trouver dans un autre groupe de ressources.

Si vous déplacez une machine virtuelle avec une carte d’interface réseau, vous devez déplacer toutes les ressources dépendantes. Déplacer le réseau virtuel de la carte d’interface réseau, toutes les autres cartes d’interface réseau dépendantes du réseau virtuel ainsi que les passerelles VPN.

## <a name="state-of-dependent-resources"></a>État des ressources dépendantes

Si le groupe de ressources source ou cible contient un réseau virtuel, les états de toutes les ressources dépendantes pour le réseau virtuel sont vérifiés au cours du déplacement. Si l’une de ces ressources est en état d’échec, le déplacement est bloqué. Par exemple, si une machine virtuelle qui utilise le réseau virtuel a échoué, le déplacement est bloqué. Le déplacement est bloqué même lorsque la machine virtuelle n’est pas l’une des ressources déplacées et ne se trouve pas dans l’un des groupes de ressources à déplacer. Pour éviter ce problème, déplacez vos ressources vers un groupe de ressources qui n’a pas de réseau virtuel.

## <a name="peered-virtual-network"></a>Réseau virtuel appairé

Pour déplacer un réseau virtuel homologué, vous devez d’abord désactiver l’homologation du réseau virtuel. Une fois l’homologation désactivée, vous pouvez déplacer le réseau virtuel. Après le déplacement, réactivez l’homologation du réseau virtuel.

## <a name="subnet-links"></a>Liens de sous-réseau

Vous ne pouvez pas déplacer un réseau virtuel vers un autre abonnement s’il contient un sous-réseau avec des liens de navigation dans les ressources. Par exemple, si une ressource de Cache Azure pour Redis est déployée dans un sous-réseau, ce sous-réseau possède un lien de navigation dans les ressources.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../resource-group-move-resources.md).
