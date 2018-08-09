---
title: Utiliser des rôles personnalisés pour cibler les paramètres Privileged Identity Management des ressources Azure | Microsoft Docs
description: Décrit l’utilisation de rôles personnalisés pour les ressources Azure avec PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 03091b50eb4fb368ae0277344317b261991c4bd1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622685"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Utiliser des rôles personnalisés pour cibler les paramètres Privileged Identity Management

Vous devrez peut-être appliquer des paramètres PIM (Privileged Identity Management) stricts à certains membres d’un rôle, tout en offrant une plus grande autonomie aux autres membres. Envisagez un scénario dans lequel votre société embauche plusieurs associés sous contrat pour aider au développement d’une application qui s’exécute dans un abonnement Azure.

En tant qu’administrateur de ressources, vous souhaitez que les employés puissent bénéficier d’un accès sans exiger d’approbation. Toutefois, tous les partenaires doivent être approuvés quand ils demandent à accéder aux ressources de l’organisation.

Suivez les étapes décrites dans la section suivante pour définir les paramètres PIM ciblés pour les rôles de ressources Azure.

## <a name="create-the-custom-role"></a>Créer le rôle personnalisé

Pour créer un rôle personnalisé pour une ressource, suivez les étapes décrites dans [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../role-based-access-control-custom-roles.md).

Quand vous créez un rôle personnalisé, indiquez un nom descriptif afin de vous rappeler facilement du rôle intégré que vous souhaitez dupliquer.

> [!NOTE]
> Vérifiez que le rôle personnalisé est un doublon du rôle intégré à dupliquer, et que son étendue correspond à celle du rôle intégré.

## <a name="apply-pim-settings"></a>Appliquer les paramètres PIM

Une fois le rôle créé dans votre locataire, dans le portail Azure, accédez au volet **Privileged Identity Management - Ressources Azure**. Sélectionnez la ressource à laquelle le rôle s’applique.

![Volet « Privileged Identity Management - Ressources Azure »](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Configurez les paramètres du rôle PIM](pim-resource-roles-configure-role-settings.md) qui doivent s’appliquer à ces membres du rôle.

Pour finir, [attribuez des rôles](pim-resource-roles-assign-roles.md) au groupe de membres distinct que vous souhaitez cibler avec ces paramètres.

## <a name="next-steps"></a>Étapes suivantes

[Passer en revue les propriétaires d’abonnements et l’accès](pim-resource-roles-perform-access-review.md)
