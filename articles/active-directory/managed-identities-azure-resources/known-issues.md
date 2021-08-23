---
title: Problèmes connus en lien avec des identités managées - Azure Active Directory
description: Problèmes connus en lien avec des identités managées pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: ''
ms.openlocfilehash: bc43eef81a7c7bc5d66f1144bf8fc2c2e148bc49
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122563966"
---
# <a name="known-issues-with-managed-identities"></a>Problèmes connus avec les identités managées

Cet article aborde quelques-uns des problèmes liés aux identités managées et comment les résoudre. Les questions courantes sur les identités managées sont documentées dans notre article [Forum aux questions](managed-identities-faq.md).
## <a name="vm-fails-to-start-after-being-moved"></a>La machine virtuelle ne démarre pas après avoir été déplacée 

Si vous déplacez une machine virtuelle dans un état d’exécution à partir d’un groupe de ressources ou d’un abonnement, elle continue à s’exécuter pendant le déplacement. Toutefois, si la machine virtuelle est arrêtée et redémarrée après le déplacement, elle ne démarre plus. Ce problème se produit parce que la machine virtuelle ne met pas à jour la référence à l’identité des identités managées pour les ressources Azure, mais continue de pointer dessus dans l’ancien groupe de ressources.

**Solution de contournement** 
 
Déclenchez une mise à jour sur la machine virtuelle afin qu’elle obtienne les valeurs correctes pour les identités managées pour les ressources Azure. Vous pouvez modifier les propriétés d’une machine virtuelle pour mettre à jour la référence à l’identité des identités managées pour les ressources Azure. Par exemple, vous pouvez définir une nouvelle valeur de balise sur la machine virtuelle avec la commande suivante :

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Cette commande définit une nouvelle balise « fixVM » avec une valeur de 1 sur la machine virtuelle. 
 
En définissant cette propriété, la machine virtuelle se met à jour avec l’URI de ressource appropriée des identités managées pour les ressources Azure. Vous devriez ensuite être en mesure de démarrer la machine virtuelle. 
 
Une fois la machine virtuelle démarrée, la balise peut être supprimée en utilisant la commande suivante :

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transfert d’un abonnement entre des répertoires Azure AD

Les identités managées ne sont pas mises à jour lorsqu’un abonnement est déplacé/transféré vers un autre répertoire. Par conséquent, toutes les identités managées existantes affectées par le système ou par l’utilisateur seront rompues. 

Solution de contournement pour les identités managées dans un abonnement déplacé vers un autre répertoire :

 - Pour les identités managées affectées par le système : désactivez et réactivez-les. 
 - Pour les identités managées affectées par l’utilisateur : supprimez, recréez et joignez-les à nouveau aux ressources nécessaires (par exemple, des machines virtuelles)

Pour plus d’informations, consultez [Transférer un abonnement Azure vers une autre instance Azure AD Directory](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez consulter notre article répertoriant les [services qui prennent en charge les identités managées](services-support-managed-identities.md) et le [forum aux questions](managed-identities-faq.md)
