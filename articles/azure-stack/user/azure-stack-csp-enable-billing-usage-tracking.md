---
title: Activer un fournisseur de services cloud pour gérer votre abonnement Azure Stack | Microsoft Docs
description: Active le fournisseur de services pour accéder à un abonnement dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357841"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Activer un fournisseur de services cloud pour gérer votre abonnement Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Si vous utilisez Azure Stack avec un fournisseur de services cloud, vous pouvez choisir de gérer votre propre abonnement pour accéder aux ressources dans Azure et dans Azure Stack. Vous pouvez également laisser le fournisseur gérer votre abonnement à votre place. Cet article vous montre comment effectuer les opérations suivantes :

 * Autoriser votre fournisseur de services à accéder à votre abonnement.
 * Vérifier que le fournisseur de services peut gérer votre service.

> [!Note]
>  Si vous ignorez les étapes suivantes et le fournisseur de services cloud ne gère pas déjà votre compte, il ne peut pas gérer votre abonnement Azure Stack à votre place.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gérer votre abonnement avec un fournisseur de services cloud

Ajoutez le fournisseur de services cloud comme **utilisateur** dans votre abonnement.

1. Ajoutez votre fournisseur de services cloud en tant qu’utilisateur invité avec le rôle d’utilisateur dans votre répertoire d’abonné.  Pour savoir comment ajouter un utilisateur, voir [Ajouter de nouveaux utilisateurs dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Le fournisseur de services cloud crée l’abonnement Azure Stack local pour vous.
3. Vous êtes prêt à utiliser Azure Stack.
4. Votre fournisseur de services cloud doit créer une ressource dans votre abonnement pour vérifier s’il peut également gérer vos ressources. Par exemple, il peut [Créer une machine virtuelle Windows avec le portail Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Activer le fournisseur de services cloud pour gérer votre abonnement avec les droits du contrôle d’accès en fonction du rôle

Ajoutez le fournisseur de services cloud comme **propriétaire** dans votre abonnement.

1. Ajoutez votre fournisseur de services cloud comme utilisateur invité dans votre répertoire de locataire.  Pour savoir comment ajouter un utilisateur, voir [Ajouter de nouveaux utilisateurs dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Ajoutez le rôle Propriétaire à l’utilisateur invité du fournisseur de services cloud. Pour savoir comment ajouter l’utilisateur du fournisseur de services cloud à votre abonnement, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
3. Le fournisseur de services cloud crée l’abonnement Azure Stack local pour vous.
4. Vous êtes prêt à utiliser Azure Stack.
5. Votre fournisseur de services cloud doit créer une ressource dans votre abonnement pour vérifier s’il peut gérer vos ressources.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack, consultez [Usage and billing in Azure Stack](../azure-stack-billing-and-chargeback.md) (Utilisation et facturation dans Azure Stack).
