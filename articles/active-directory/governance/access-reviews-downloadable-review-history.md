---
title: Créer et gérer un rapport sur l’historique des révisions d’accès téléchargeable (préversion) – Azure Active Directory
description: Grâce aux révisions d’accès d’Azure Active Directory, vous pouvez télécharger un historique des révisions d’accès dans votre organisation.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/25/2021
ms.author: ajburnle
ms.openlocfilehash: 6f0d9b92b9aa3bb48533d5270c0fcd31aa8df82c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025798"
---
# <a name="create-and-manage-downloadable-access-review-history-report-preview-in-azure-ad-access-reviews"></a>Créer et gérer un rapport sur l’historique des révisions d’accès téléchargeable (préversion) dans les révisions d’accès d’Azure AD

Les révisions d’accès d’Azure Active Directory (Azure AD) vous permettent de créer un historique des révisions téléchargeable pour aider votre organisation à acquérir une vision plus claire. Le rapport extrait les décisions prises par les réviseurs lors de la création de rapports. Ces rapports peuvent être construits pour inclure des révisions d’accès spécifiques, pour une période spécifique, et peuvent être filtrés pour inclure différents types et résultats de révisions.
 
## <a name="who-can-access-and-request-review-history"></a>Qui peut demander et consulter l’historique des révisions

La demande et la consultation de l’historique des révisions sont à la portée de tous les utilisateurs autorisés à consulter les révisions d’accès. Pour voir quels rôles peuvent consulter et créer des révisions d’accès, consultez [Quels types de ressources peuvent être révisés ?](deploy-access-reviews.md#what-resource-types-can-be-reviewed). L’administrateur général et le lecteur général peuvent consulter toutes les révisions d’accès. Tous les autres utilisateurs ne sont autorisés à voir que les rapports sur les révisions d’accès qu’ils ont générés.

## <a name="how-to-create-a-review-history-report"></a>Comment créer un rapport sur l’historique des révisions

**Rôle prérequis :** tous les utilisateurs autorisés à consulter les révisions d’accès

1. Dans le portail Azure, sélectionnez **Azure Active Directory**, puis **Identity Governance**.

1. Dans le menu de gauche, sous **Révisions d’accès**, sélectionnez **Historique des révisions**.
 
1. Sélectionnez **Nouveau rapport**. 

1. Spécifiez les dates de début et de fin de révision.

1. Sélectionnez les types et résultats de révisions que vous souhaitez inclure dans le rapport. 

    ![Révisions d’accès - Rapport sur l’historique des révisions d’accès - Créer](./media/access-reviews-downloadable-review-history/create-review-history.png)

1. Sélectionnez ensuite **Créer** pour créer un rapport sur l’historique des révisions d’accès.

## <a name="how-to-download-review-history-reports"></a>Comment télécharger des rapports sur l’historique des révisions

Une fois qu’un rapport sur l’historique des révisions est créé, vous pouvez le télécharger. Tous les rapports créés sont disponibles pour téléchargement au format CSV pendant 30 jours.

1. Sous **Identity Governance**, sélectionnez **Historique des révisions**. Tous les rapports sur l’historique des révisions que vous avez créés seront disponibles. 
1. Sélectionnez le rapport que vous souhaitez télécharger. 

## <a name="what-is-included-in-a-review-history-report"></a>Qu’est-ce qui est inclus dans un rapport sur l’historique des révisions ?

Les rapports fournissent des détails par utilisateur, présentant les informations suivantes :

| Nom de l'élément | Description |
| --- | --- |
| AccessReviewId |  ID d’objet de la révision |
| ReviewType | Les types de révisions sont groupe, application, rôle Azure AD, rôle Azure et package d’accès|
|ResourceDisplayName | Nom d’affichage de la ressource en cours de révision |
| ResourceId | ID de la ressource en cours de révision |
| ReviewName |  Nom de la révision |
| CreatedDateTime | Date et heure de création de la révision |
| ReviewStartDate | Date de début de la révision
| ReviewEndDate | Date de fin de la révision |
| ReviewStatus | État de la révision. Pour tous les états de révision, consultez le tableau sur les états de révision d’accès [ici](create-access-review.md) |
| OwnerId | ID de propriétaire du réviseur |
| OwnerName | Nom de propriétaire du réviseur |
| OwnerUPN | Nom d’utilisateur principal propriétaire du réviseur |
| PrincipalId | ID du principal en cours de révision |
| Nom principal | Nom du principal en cours de révision |
| PrincipalUPN | Nom principal de l’utilisateur en cours de révision |
| PrincipalType | Type du principal. Les options sont utilisateur, groupe et principal du service |
| ReviewDate | Date de la révision |
| ReviewResult | Les résultats de la révision sont Refuser, Approuver et Non révisé |
|Justification | Justification du résultat de la révision fournie par le réviseur |
| ReviewerId | ID du réviseur |
| ReviewerName | Nom du réviseur |
| ReviewerUPN | Nom d’utilisateur principal du réviseur |
| ReviewerEmailAddress | Adresse e-mail du réviseur |
| AppliedByName | Nom de l’utilisateur qui a appliqué le résultat de la révision |
| AppliedByUPN | Nom d’utilisateur principal de l’utilisateur qui a appliqué le résultat de la révision|
| AppliedByEmailAddress | Adresse e-mail de l’utilisateur qui a appliqué le résultat de la révision |
| AppliedDate | Date à laquelle le résultat de la révision a été appliqué |
| AccessRecommendation | Les recommandations du système sont Approuver, Refuser et Aucune information |
|SubmissionResult | L’état de l’envoi du résultat de la révision est appliqué ou non appliqué |

## <a name="next-steps"></a>Étapes suivantes
- [Réviser l’accès à des groupes ou à des applications](perform-access-review.md)
- [Réviser son accès à des groupes ou à des applications](review-your-access.md)
- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)
