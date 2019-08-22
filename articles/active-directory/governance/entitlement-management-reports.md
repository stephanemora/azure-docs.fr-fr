---
title: Afficher les rapports et les journaux dans la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez comment afficher le rapport d’affectations d’utilisateur et les journaux d’audit dans la gestion des droits d’utilisation Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2420fc25795ec74939649fb8a17ead7c8cfdd1df
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032442"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Afficher les rapports et les journaux dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le rapport sur les attributions d’utilisateurs et le journal d’audit Azure Active Directory fournissent des détails supplémentaires sur les utilisateurs de votre annuaire. En tant qu’administrateur, vous pouvez afficher les ressources auxquelles un utilisateur a accès et afficher les journaux des requêtes à des fins d’audit, ou pour déterminer l’état de la requête d’un utilisateur. Cet article explique comment utiliser le rapport des attributions des utilisateurs et les journaux d’audit Azure AD.

Regardez la vidéo suivante pour savoir comment utiliser la gestion des droits pour gérer l’accès des utilisateurs dans Azure Active Directory :

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-resources-a-user-has-access-to"></a>Afficher les ressources auxquelles un utilisateur a accès

1. Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Rapport des affectations utilisateur**.

1. Cliquez sur **Sélectionner des utilisateurs** pour ouvrir le volet Sélectionner des utilisateurs.

1. Passez au travers de la liste pour trouver l’utilisateur dont vous souhaitez afficher les ressources auxquelles il a accès.

1. Sélectionnez sur l’utilisateur, puis cliquez sur **Sélectionner**.

    Une liste de ressources auxquelles l’utilisateur a accès s’affiche. Elle comprend le package d’accès, la stratégie et les dates.

    ![Rapport des affectations utilisateur](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Déterminer l’état de la demande d’un utilisateur

Pour obtenir plus d’informations sur la façon dont un utilisateur a demandé et reçu l’accès à un package d’accès, vous pouvez utiliser le journal d’audit Azure AD. En particulier, vous pouvez utiliser des enregistrements de journal dans les catégories `EntitlementManagement` et `UserManagement` pour obtenir des détails supplémentaires sur les étapes de traitement pour chaque demande.  

1. Cliquez sur **Azure Active Directory**, puis sur **Journaux d’audit**.

1. En haut, remplacez la **Catégorie** par `EntitlementManagement` ou `UserManagement`, en fonction de l’enregistrement d’audit que vous recherchez.  

1. Cliquez sur **Appliquer**.

1. Cliquez sur **Télécharger** pour télécharger les journaux.

Lorsqu’Azure AD reçoit une nouvelle demande, il écrit un enregistrement d’audit, dont la **catégorie** est `EntitlementManagement` et l’**activité** est généralement `User requests access package assignment`.  Dans le cas d’une affectation directe créée dans le portail Azure, le champ **activité** de l’enregistrement d’audit est `Administrator directly assigns user to access package`, et l’utilisateur effectuant l’affectation est identifiée par **ActorUserPrincipalName**.

Azure AD écrira des enregistrements d’audit supplémentaires lorsque la demande est en cours, y compris :

| Category | Activité | État de la demande |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | La demande ne nécessite pas d’approbation |
| `UserManagement` | `Create request approval` | La demande nécessite une approbation |
| `UserManagement` | `Add approver to request approval` | La demande nécessite une approbation |
| `EntitlementManagement` | `Approve access package assignment request` | Demande approuvée |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Demande approuvée, ou ne nécessite pas d’approbation |

Lorsqu’un utilisateur se voit affecté l’accès, Azure AD écrit un enregistrement d’audit pour la catégorie `EntitlementManagement` avec l’**activité** `Fulfill access package assignment`.  L’utilisateur qui a reçu l’accès est identifié par le champ **ActorUserPrincipalName**.

Si l’accès n’a pas été affecté, Azure AD écrit un enregistrement d’audit pour la catégorie `EntitlementManagement` avec l’**activité** `Deny access package assignment request`, si la demande a été refusée par un approbateur, ou `Access package assignment request timed out (no approver action taken)`, si la demande a expiré avant l’approbation.

Lorsque l’attribution du package d’accès d’un utilisateur expire, est annulé par l’utilisateur, ou supprimé par un administrateur, Azure AD écrit un enregistrement d’audit pour la catégorie `EntitlementManagement` avec l’**activité** de `Remove access package assignment`.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de gestion des droits d’utilisation Azure AD](entitlement-management-troubleshoot.md)
- [Scénarios courants](entitlement-management-scenarios.md)
