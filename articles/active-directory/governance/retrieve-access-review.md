---
title: Récupérer les résultats d’une révision d’accès Azure AD | Microsoft Docs
description: Procédure de récupération des résultats des révisions d’accès Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: ac90025ff617323e79fef21b286f8f612f55ce84
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173494"
---
# <a name="retrieve-access-review-results"></a>Récupérer les résultats d’une révision d’accès

Les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour [créer une révision d’accès](create-access-review.md) des membres du groupe ou des utilisateurs affectés à une application.  Un utilisateur disposant du rôle **Administrateur général**, **Administrateur des comptes d’utilisateur** , **Administrateur de la sécurité** ou **Lecteur Sécurité** peut également lire les résultats d’une révision d’accès.  Pour assigner des utilisateurs à l’un de ces rôles, un Administrateur de rôle privilégié peut utiliser Azure AD Privileged Identity Management (PIM) afin de définir un utilisateur comme éligible à l’activation du rôle, ou un Administrateur général peut [assigner un utilisateur au rôle](../fundamentals/active-directory-users-assign-role-azure-portal.md) de façon permanente.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Localisation d’une révision d’accès

Si vous connaissez le nom du programme qui contient la révision d’accès, accédez à la page des révisions d’accès, sélectionnez **Programmes**, puis choisissez le programme contenant le contrôle de révision d’accès.  Ensuite, sélectionnez **Contrôles** et choisissez le contrôle de révision d’accès. S’il existe de nombreux contrôles dans le programme, vous pouvez appliquer un filtre pour n’afficher que les contrôles d’un type spécifique et les trier selon leur état. Vous pouvez également effectuer une recherche en saisissant le nom du contrôle de révision d’accès ou le nom complet de son auteur. 

Si vous ne connaissez pas le nom du programme qui contient la révision d’accès, accédez à la page des révisions d’accès, puis sélectionnez **Contrôles**.  Vous pouvez filtrer les contrôles d’un type spécifique, les trier selon leur état et effectuer une recherche sur le nom du contrôle de révision d’accès ou sur le nom d’affichage du propriétaire qui l’a créé. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Récupération des résultats d’une révision d’accès ponctuelle

Si la révision d’accès ne doit se produire qu’une seule fois, vous pouvez visualiser la progression d’une révision d’accès active et les résultats d’une révision terminée à partir de la section **Résultats**.  Si vous souhaitez visualiser uniquement l’accès en cours de révision d’un utilisateur spécifique, vous pouvez taper le nom d’affichage ou le nom d’utilisateur principal de cet utilisateur.  Pour récupérer tous les résultats d’une révision d’accès terminée, cliquez sur le bouton **Télécharger**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Récupération des résultats de plusieurs instances d’une révision d’accès récurrente

Pour visualiser la progression d’une révision d’accès active récurrente, cliquez sur la section **Résultats**.  Vous pouvez taper le nom d’affichage ou le nom d’utilisateur principal d’un utilisateur dont l’accès est en cours de révision.

Pour visualiser les résultats d’une instance terminée d’une révision d’accès récurrente, sélectionnez **Historique des révisions**, puis choisissez l’instance concernée dans la liste des instances de révision d’accès terminées, en fonction des dates de début et de fin de l’instance.   Vous pouvez obtenir les résultats de cette instance à partir de la section **Résultats**.  Si vous souhaitez visualiser uniquement l’accès en cours de révision d’un utilisateur spécifique, vous pouvez taper le nom d’affichage ou le nom d’utilisateur principal de cet utilisateur.  Pour récupérer tous les résultats d’une instance terminée d’une révision d’accès récurrente, cliquez sur le bouton **Télécharger**.


## <a name="removing-users-from-an-access-review"></a>Suppression d’utilisateurs d’une révision d’accès

Par défaut, un utilisateur supprimé conserve cet état dans Azure AD pendant 30 jours, au cours desquels il peut être restauré par un administrateur s’il y a lieu.  Au bout de 30 jours, cet utilisateur est définitivement supprimé.  En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](../fundamentals/active-directory-users-restore.md) avant que cette période arrive à son terme.  Une fois qu’un utilisateur a été définitivement supprimé, les données le concernant sont par la suite supprimées des révisions d’accès actives.  Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.

## <a name="next-steps"></a>Étapes suivantes

- [Manage user access with Azure AD access reviews](manage-user-access-with-access-reviews.md) (Gérer l’accès des utilisateurs avec les révisions d’accès Azure AD)
- [Manage guest access with Azure AD access reviews](manage-guest-access-with-access-reviews.md) (Gérer l’accès des invités avec les révisions d’accès Azure AD)
- [Manage programs and controls for Azure AD access reviews](manage-programs-controls.md) (Gérer les programmes et les contrôles pour la révision d’accès Azure AD)
- [Create an access review for members of a group or access to an application](create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-how-to-start-security-review.md) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)


