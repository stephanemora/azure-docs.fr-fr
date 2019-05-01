---
title: Afficher les rapports et les journaux dans la gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: Découvrez comment afficher le rapport d’affectations d’utilisateur et les journaux d’audit dans la gestion des habilitations Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541539"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Afficher les rapports et les journaux dans la gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Afficher les ressources d’un utilisateur a accès à

1. Cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **rapport des affectations utilisateur**.

1. Cliquez sur **sélectionnez utilisateurs** pour ouvrir le volet Sélectionner les utilisateurs.

1. Recherchez l’utilisateur dans la liste que vous souhaitez afficher les ressources qu’ils ont accès.

1. Cliquez sur l’utilisateur, puis **sélectionnez**.

    Une liste de ressources que l’utilisateur a accès à s’affiche. Il inclut le package de l’accès, la stratégie et dates.

    ![Rapport sur les affectations de l'utilisateur](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Déterminer l’état de la demande d’un utilisateur

Pour obtenir plus d’informations sur comment un utilisateur demandé et reçu l’accès à un package de l’accès, vous pouvez utiliser le journal d’audit Azure AD. En particulier, vous pouvez utiliser des enregistrements de journal dans le `EntitlementManagement` et `UserManagement` catégories pour obtenir des détails supplémentaires sur les étapes de traitement pour chaque demande.  

1. Cliquez sur **Azure Active Directory** puis cliquez sur **journaux d’Audit**.

1. En haut, remplacez le **catégorie** soit `EntitlementManagement` ou `UserManagement`, en fonction de l’enregistrement d’audit que vous recherchez.  

1. Cliquez sur **Appliquer**.

1. Pour télécharger les journaux, cliquez sur **télécharger**.

En cas d’Azure AD reçoit une nouvelle demande, il écrit un enregistrement d’audit, dans lequel le **catégorie** est `EntitlementManagement` et **activité** est généralement `User requests access package assignment`.  Dans le cas d’une affectation directe créée dans le portail Azure, le **activité** champ de l’enregistrement d’audit est `Administrator directly assigns user to access package`, et l’utilisateur qui effectue l’attribution est identifiée par le **ActorUserPrincipalName**.

Azure AD écrira les enregistrements d’audit supplémentaires lorsque la demande est en cours, y compris :

| Catégorie | Activité | État de la demande |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Demande ne nécessite pas d’approbation |
| `UserManagement` | `Create request approval` | Demande nécessite une approbation |
| `UserManagement` | `Add approver to request approval` | Demande nécessite une approbation |
| `EntitlementManagement` | `Approve access package assignment request` | Demande approuvée |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Demande approuvée, ou ne nécessite pas d’approbation |

Lorsqu’un utilisateur est affecté l’accès, Azure AD écrit un enregistrement d’audit pour le `EntitlementManagement` catégorie avec **activité** `Fulfill access package assignment`.  L’utilisateur qui a reçu l’accès est identifié par **ActorUserPrincipalName** champ.

Si l’accès n’a pas été affecté, Azure AD écrit un enregistrement d’audit pour le `EntitlementManagement` catégorie avec **activité** soit `Deny access package assignment request`, si la demande a été refusée par un approbateur, ou `Access package assignment request timed out (no approver action taken)`, si la demande a expiré avant un approbateur peut approuver.

Lors de l’attribution de package de l’accès de l’utilisateur arrive à expiration, est annulée par l’utilisateur, ou supprimé par un administrateur, puis Azure AD écrit un enregistrement d’audit pour le `EntitlementManagement` catégorie avec **activité** de `Remove access package assignment`.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de gestion des droits Azure AD](entitlement-management-troubleshoot.md)
- [Scénarios courants](entitlement-management-scenarios.md)
