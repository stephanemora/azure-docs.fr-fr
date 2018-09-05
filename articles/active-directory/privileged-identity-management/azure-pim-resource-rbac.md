---
title: Afficher les détenteurs de rôles de ressources Azure dans PIM | Microsoft Docs
description: Affichez les détenteurs de rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: aee172bc6fc77aaac8d2d52037a481fdb976d308
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188966"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>Afficher les détenteurs de rôles de ressources Azure dans PIM

Avec Azure Active Directory Privileged Identity Management (PIM), vous pouvez gérer, contrôler et surveiller l’accès aux ressources Azure au sein de votre organisation. Cela inclut les abonnements, les groupes de ressources et même les machines virtuelles. Toutes les ressources du portail Azure qui utilisent la fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) d’Azure peuvent tirer profit de toutes les capacités de gestion de la sécurité et des cycles de vie proposées par Azure AD PIM. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM pour les ressources Azure vient en aide aux administrateurs de ressources

- Vérifier quels utilisateurs et groupes se sont vus assigner des rôles pour les ressources Azure que vous administrez
- Activer l’accès sur demande, « à la volée », pour gérer des ressources telles que les abonnements, les groupes de ressources, etc.
- Faire expirer automatiquement les accès assignés aux ressources d’utilisateurs/groupes grâce aux nouveaux paramètres d’affectation temporaire
- Affecter un accès temporaire aux ressources pour des tâches rapides ou des planifications d’appel
- Application de l’authentification multifacteur via Azure Multi-Factor Authentication pour l’accès aux ressources sur n’importe quel rôle intégré ou personnalisé 
- Obtenir des rapports sur l’activité des ressources liées aux accès pendant une session active d’un utilisateur
- Obtenir des alertes lorsque de nouveaux utilisateurs ou groupes se voient assigner un accès à des ressources, et lorsqu’ils activent des attributions éligibles

## <a name="view-activation-and-azure-resource-activity"></a>Voir les activations et les activités des ressources Azure

Si vous avez besoin de connaître les actions d’un utilisateur spécifique effectuées sur les différentes ressources, vous pouvez consulter l’activité de la ressource Azure sur une période d’activation donnée (pour les utilisateurs éligibles). Commencez par sélectionner un utilisateur à partir de la vue des membres ou à partir de la liste des membres dans un rôle spécifique. Le résultat affiche une vue graphique des actions de l’utilisateur sur les ressources Azure classées par date ainsi que les activations de rôle récentes sur la même période de temps.

![](media/azure-pim-resource-rbac/user-details.png)

La sélection d’une activation de rôle affichera les détails de l’activation de rôle ainsi que l’activité de la ressource Azure correspondante survenue lorsque l’utilisateur était actif.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>Revoir les accès à un abonnement

Pour revoir les attributions de rôle dans votre abonnement, sélectionnez l’onglet Membres dans le volet de navigation gauche, ou bien sélectionner des rôles et choisissez un rôle spécifique pour passer en revue les membres. 

Sélectionnez Revoir à partir de la barre d’action pour afficher les révisions d’accès existantes et sélectionnez Ajouter pour en créer une nouvelle.

![](media/azure-pim-resource-rbac/owner.png)

[En savoir plus sur les révisions d’accès](pim-how-to-perform-security-review.md)

>[!NOTE]
Les révisions sont uniquement prises en charge pour les types de ressource d’abonnement à un instant donné.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
- [Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM](pim-resource-roles-approval-workflow.md)
- [Rôles intégrés dans Azure](../../role-based-access-control/built-in-roles.md)
