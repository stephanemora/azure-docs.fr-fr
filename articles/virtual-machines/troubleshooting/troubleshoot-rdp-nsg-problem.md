---
title: Impossible de se connecter aux machines virtuelles Azure car le port RDP n’est pas activé dans le NSG | Microsoft Docs
description: Découvrir comment résoudre un échec de connexion RDP à cause de la configuration du NSG dans le portail Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: c32612c411f275220f549eea79276fa5a7232fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318933"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Impossible de se connecter à distance à une machine virtuelle car le port RDP n’est pas activé dans le NSG

Cet article explique comment résoudre un problème dans lequel il est impossible de se connecter à une machine de virtuelle Windows Azure, car le port du protocole RDP (Remote Desktop Protocol) n’est pas activé dans le groupe de sécurité réseau (NSG).


> [!NOTE] 
> Azure comporte deux modèles de déploiement pour la création et l’utilisation de ressources : [Resource Manager et classique](../../azure-resource-manager/resource-manager-deployment-model.md). Nous vous recommandons d’utiliser le modèle de déploiement Resource Manager pour les nouveaux déploiements, plutôt que le modèle de déploiement classique. 

## <a name="symptom"></a>Symptôme

Vous ne pouvez pas établir de connexion RDP à une machine virtuelle dans Azure, car le port RDP n’est pas ouvert dans le groupe de sécurité réseau.

## <a name="solution"></a>Solution 

Lorsque vous créez une nouvelle machine virtuelle, tout le trafic provenant d’Internet est bloqué par défaut. 

Pour activer le port RDP dans un NSG, procédez comme suit :
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans **Machines virtuelles**, sélectionnez la machine virtuelle qui pose problème. 
3. Sous **Paramètres**, sélectionnez **Mise en réseau**. 
4. Dans **Règles des port d’entrée**, vérifiez que le port du protocole RDP est correctement défini. Voici un exemple de configuration : 

    **Priorité** : 300 </br>
    **Port** : 3389 </br>
    **Nom** : Port_3389 </br>
    **Port** : 3389 </br>
    **Protocole** : TCP </br>
    **Source** : Quelconque </br>
    **Destinations** : Quelconque </br>
    **Action** : AUTORISER </br>

Si vous spécifiez l’adresse IP source, ce paramètre autorise uniquement le trafic provenant d’une adresse IP ou plage d’adresses IP spécifique pour se connecter à la machine virtuelle. Vérifiez que l’ordinateur que vous utilisez pour démarrer la session RDP est dans la plage.

Pour plus d’informations sur les NSG, consultez [Groupe de sécurité réseau](../../virtual-network/security-overview.md).

> [!NOTE]
> Le port RDP 3389 est exposé à Internet. Par conséquent, nous vous recommandons d’utiliser ce port uniquement pour le test. Pour les environnements de production, nous vous recommandons d’utiliser une connexion VPN ou privée.

## <a name="next-steps"></a>Étapes suivantes

Si le port RDP est déjà activé dans le NSG, consultez [Résoudre une erreur générale RDP sur une machine virtuelle Azure](./troubleshoot-rdp-general-error.md).



