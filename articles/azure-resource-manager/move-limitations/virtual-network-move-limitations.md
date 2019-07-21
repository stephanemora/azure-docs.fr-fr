---
title: Déplacer des ressources Réseaux virtuels Azure vers un nouveau groupe de ressources ou abonnement | Microsoft Docs
description: Utilisez Azure Resource Manager pour déplacer des réseaux virtuels vers un nouveau groupe de ressources ou abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723349"
---
# <a name="move-guidance-for-virtual-networks"></a>Conseils pour le déplacement de réseaux virtuels

Cet article décrit comment déplacer des réseaux virtuels pour des scénarios spécifiques.

## <a name="dependent-resources"></a>Ressources dépendantes

Lors de la migration d’un réseau virtuel, vous devez également migrer ses ressources dépendantes. Pour les passerelles VPN, vous devez déplacer les adresses IP, les passerelles de réseau virtuel et toutes les ressources de connexion associées. Les passerelles de réseau locales peuvent se trouver dans un autre groupe de ressources.

Si vous déplacez une machine virtuelle avec une carte d’interface réseau, vous devez déplacer toutes les ressources dépendantes. Déplacer le réseau virtuel de la carte d’interface réseau, toutes les autres cartes d’interface réseau dépendantes du réseau virtuel ainsi que les passerelles VPN.

## <a name="peered-virtual-network"></a>Réseau virtuel appairé

Pour déplacer un réseau virtuel homologué, vous devez d’abord désactiver l’homologation du réseau virtuel. Une fois l’homologation désactivée, vous pouvez déplacer le réseau virtuel. Après le déplacement, réactivez l’homologation du réseau virtuel.

## <a name="subnet-links"></a>Liens de sous-réseau

Vous ne pouvez pas déplacer un réseau virtuel vers un autre abonnement s’il contient un sous-réseau avec des liens de navigation dans les ressources. Par exemple, si une ressource de Cache Azure pour Redis est déployée dans un sous-réseau, ce sous-réseau possède un lien de navigation dans les ressources.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../resource-group-move-resources.md).
