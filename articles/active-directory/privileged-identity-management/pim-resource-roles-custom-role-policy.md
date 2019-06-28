---
title: Utiliser les rôles personnalisés pour des ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser des rôles personnalisés pour des ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e814cde49374b52266f725b4d57657a507874ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602030"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Utiliser des rôles personnalisés pour les ressources Azure dans PIM

Vous devrez peut-être appliquer des paramètres Azure AD (Azure Active Directory) PIM (Privileged Identity Management) stricts à certains membres d’un rôle, tout en offrant une plus grande autonomie aux autres membres. Envisagez un scénario dans lequel votre société embauche plusieurs associés sous contrat pour aider au développement d’une application qui s’exécute dans un abonnement Azure.

En tant qu’administrateur de ressources, vous souhaitez que les employés puissent bénéficier d’un accès sans exiger d’approbation. Toutefois, tous les partenaires doivent être approuvés quand ils demandent à accéder aux ressources de l’organisation.

Suivez les étapes décrites dans la section suivante pour définir les paramètres PIM ciblés pour les rôles de ressources Azure.

## <a name="create-the-custom-role"></a>Créer le rôle personnalisé

Pour créer un rôle personnalisé pour une ressource, suivez les étapes décrites dans [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../role-based-access-control-custom-roles.md).

Quand vous créez un rôle personnalisé, indiquez un nom descriptif afin de vous rappeler facilement du rôle intégré que vous souhaitez dupliquer.

> [!NOTE]
> Vérifiez que le rôle personnalisé est un doublon du rôle intégré à dupliquer, et que son étendue correspond à celle du rôle intégré.

## <a name="apply-pim-settings"></a>Appliquer les paramètres PIM

Une fois le rôle créé dans votre locataire, dans le portail Azure, accédez au volet **Privileged Identity Management - Ressources Azure**. Sélectionnez la ressource à laquelle le rôle s’applique.

![Volet « Privileged Identity Management - Ressources Azure »](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configurez les paramètres du rôle PIM](pim-resource-roles-configure-role-settings.md) qui doivent s’appliquer à ces membres du rôle.

Pour finir, [attribuez des rôles](pim-resource-roles-assign-roles.md) au groupe de membres distinct que vous souhaitez cibler avec ces paramètres.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles de ressources Azure dans PIM](pim-resource-roles-configure-role-settings.md)
- [Rôles personnalisés dans Azure](../../role-based-access-control/custom-roles.md)
