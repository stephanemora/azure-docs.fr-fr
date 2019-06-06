---
title: Résoudre les problèmes de gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: En savoir plus sur certains éléments que vous devez vérifier pour vous aider à résoudre les problèmes de gestion des habilitations Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473828"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Résoudre les problèmes de gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article décrit certains éléments que vous devez vérifier pour vous aider à résoudre les problèmes de gestion des habilitations Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Liste de vérification pour l’administration de gestion des droits

* Si vous obtenez un message accès refusé lors de la configuration de gestion des habilitations, et vous êtes un administrateur Global, assurez-vous que votre répertoire comporte un [licence Azure AD Premium P2 (ou EMS E5)](entitlement-management-overview.md#license-requirements).  
* Si vous obtenez un accès refusé le message lors de la création ou la consultation des packages d’accès, et que vous êtes membre d’un groupe de créateur de catalogue, vous devez créer un catalogue avant de créer votre premier package d’accès.

## <a name="checklist-for-adding-a-resource"></a>Liste de contrôle pour l’ajout d’une ressource

* Pour une application soit une ressource dans un package d’accès, il doit avoir au moins un rôle de ressource qui peut être affecté. Les rôles sont définis par l’application elle-même et sont gérés dans Azure AD. Notez que le portail Azure peut également afficher les principaux de service pour les services qui ne peut pas être sélectionnés en tant qu’applications.  En particulier, **Exchange Online** et **SharePoint Online** sont des services, pas les applications qui ont des rôles de ressources dans le répertoire, afin qu’ils ne peuvent pas être incluses dans un package d’accès.  Au lieu de cela, utilisez Gestion des licences par groupe pour établir une licence appropriée pour un utilisateur qui a besoin d’accéder à ces services.

* Pour un groupe à une ressource dans un package d’accès, il doit pouvoir être modifiable dans Azure AD.  Groupes issus d’un annuaire local Active Directory ne peut pas être assignés en tant que ressources, car leur propriétaire ou les attributs de membre ne peut pas être modifiées dans Azure AD.  

* Les bibliothèques de documents SharePoint Online et des documents individuels ne peuvent pas être ajoutés en tant que ressources.  Au lieu de cela, créez un groupe de sécurité Azure AD, inclure ce groupe et un rôle de site dans le package de l’accès et dans SharePoint Online, utilisez ce groupe pour contrôler l’accès à la bibliothèque de documents ou d’un document.

* Si des utilisateurs qui ont déjà été affectés à une ressource que vous souhaitez gérer avec un package d’accès, n’oubliez pas que les utilisateurs sont affectés au package d’accès avec une stratégie appropriée. Par exemple, vous souhaiterez peut-être inclure un groupe dans un package d’accès qui contient déjà des utilisateurs dans le groupe. Si ces utilisateurs dans le lien exiger de groupe d’un accès ininterrompu, ils doivent avoir une stratégie appropriée pour les packages d’accès afin qu’ils ne perdent leur accès au groupe. Vous pouvez affecter le package de l’accès en vous demandant aux utilisateurs de demander le package d’accès contenant cette ressource, ou en leur attribuant directement au package d’accès. Pour plus d’informations, consultez [modifier et gérer un package d’accès existant](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Liste de vérification pour fournir l’accès des utilisateurs externes

* S’il existe un B2B [liste verte](../b2b/allow-deny-list.md), les utilisateurs dont les répertoires ne sont pas autorisés ne sera pas en mesure de demander l’accès.

* Assurez-vous qu’il n’y a aucune [stratégies d’accès conditionnel](../conditional-access/require-managed-devices.md) qui empêche les utilisateurs externes à partir de la demande d’accès ou de pouvoir utiliser les applications dans les packages de l’accès.

## <a name="checklist-for-request-issues"></a>Liste de vérification pour les problèmes de demande

* Lorsqu’un utilisateur souhaite demander l’accès à un package d’accès, n’oubliez pas qu’ils utilisent le **lien vers le portail mon accès** pour le package de l’accès. Pour plus d’informations, consultez [lien vers le portail copie mon accès](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Lorsqu’un utilisateur se connecte au portail mon accès pour demander un package d’accès, n’oubliez pas qu’ils s’authentifient à l’aide de leur compte professionnel. Le compte d’organisation peut être soit un compte dans le répertoire de ressources, ou dans un répertoire qui est inclus dans l’une des stratégies du package d’accès. Si le compte d’utilisateur n’est pas un compte professionnel ou le répertoire n’est pas inclus dans la stratégie, l’utilisateur ne verrez pas le package de l’accès. Pour plus d’informations, consultez [demander l’accès à un package d’accès](entitlement-management-request-access.md).

* Si un utilisateur est bloqué à partir de la connexion à un répertoire de ressources, ils ne sera pas en mesure de demander l’accès dans le portail mon accès. Avant que l’utilisateur peut demander l’accès, vous devez supprimer le bloc de connexion du profil de l’utilisateur. Pour supprimer le bloc de connexion, dans le portail Azure, cliquez sur **Azure Active Directory**, cliquez sur **utilisateurs**, cliquez sur l’utilisateur, puis cliquez sur **profil**. Modifier le **paramètres** section et modifier **bloquer la connexion** à **non**. Pour plus d’informations, consultez [ajouter ou mettre à jour les informations de profil d’un utilisateur à l’aide d’Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Vous pouvez également vérifier si l’utilisateur a été bloqué par un [stratégie de Protection d’identité](../identity-protection/howto-unblock-user.md).

* Dans le portail mon accès, si un utilisateur est un demandeur et un approbateur, ils verra pas leur demande d’un package d’accès sur le **approbations** page. Ce comportement est intentionnel : un utilisateur ne peut pas approuver leur demande. Vérifiez que le package d’accès qu’ils demandent a des approbateurs supplémentaires configurés sur la stratégie. Pour plus d’informations, consultez [modifier une stratégie existante](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Si un utilisateur externe, qui ne possède pas déjà inscrit dans votre répertoire, reçoit un package d’accès, y compris un site SharePoint Online, leur package accès affichera comme pas entièrement remis jusqu'à ce que leur compte est configuré dans SharePoint Online.

## <a name="next-steps"></a>Étapes suivantes

- [Afficher les rapports de la façon dont les utilisateurs ont eu accès dans la gestion des habilitations](entitlement-management-reports.md)
