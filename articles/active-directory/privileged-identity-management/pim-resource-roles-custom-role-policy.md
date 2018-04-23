---
title: Privileged Identity Management pour les ressources Azure - Utiliser des rôles personnalisés pour cibler les paramètres PIM | Microsoft Docs
description: Décrit l’utilisation de rôles personnalisés dans PIM pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Utiliser des rôles personnalisés pour cibler les paramètres PIM

L’application de paramètres PIM stricts à certains membres d’un rôle, tout en offrant une plus grande autonomie aux autres membres, peut s’avérer nécessaire. Imaginez un scénario dans lequel votre société loue les services de différents partenaires contractuels, afin de faciliter le développement d’une application à exécuter dans un abonnement Azure. 

En tant qu’administrateur de la ressource, vous souhaitez que les employés de votre société aient un accès éligible sans nécessiter d’approbation, mais que tous les partenaires contractuels demandent l’autorisation lorsqu’ils veulent l’activation. Les étapes ci-dessous détaillent la procédure d’activation des paramètres PIM ciblés pour les rôles de ressources Azure.

## <a name="create-the-custom-role"></a>Créer le rôle personnalisé

[Utilisez ce guide pour créer le rôle personnalisé d’une ressource](../../role-based-access-control/custom-roles.md).

Indiquez un nom descriptif afin de vous rappeler facilement du rôle intégré que vous souhaitez dupliquer.

>[!NOTE]
>Assurez-vous que le rôle personnalisé est un doublon du rôle prévu et que son étendue correspond au rôle intégré.

## <a name="apply-pim-settings"></a>Appliquer les paramètres PIM

Une fois que le rôle est créé dans votre locataire, accédez à PIM et sélectionnez la ressource à laquelle le rôle est étendu.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Configurez les paramètres du rôle PIM](pim-resource-roles-configure-role-settings.md) qui doivent s’appliquer à ces membres.

Enfin, [Attribuez des rôles](pim-resource-roles-assign-roles.md) au groupe de membres distinct que vous souhaitez cibler avec ces paramètres.

## <a name="next-steps"></a>Étapes suivantes

[Passer en revue les propriétaires d’abonnement](pim-resource-roles-perform-access-review.md)
