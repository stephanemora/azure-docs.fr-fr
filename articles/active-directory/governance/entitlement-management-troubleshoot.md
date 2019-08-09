---
title: Résoudre les problèmes de gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez certains éléments à vérifier pour vous aider à résoudre les problèmes de gestion des droits d'utilisation Azure Active Directory (préversion).
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473828"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Résoudre les problèmes de gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article décrit certains éléments que vous devriez vérifier pour vous aider à résoudre les problèmes de gestion des droits d’utilisation Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Check-list pour l’administration de gestion des droits d’utilisation

* Si vous obtenez un message d’accès refusé lors de la configuration de la gestion des droits d’utilisation, et que vous êtes administrateur général, assurez-vous que votre annuaire comporte une [licence Azure AD Premium P2 (ou EMS E5)](entitlement-management-overview.md#license-requirements).  
* Si vous obtenez un message d’accès refusé lors de la création ou de la consultation de packages d’accès, et que vous êtes membre d’un groupe de créateurs de catalogue, vous devez créer un catalogue avant de créer votre premier package d’accès.

## <a name="checklist-for-adding-a-resource"></a>Check-list pour l’ajout d’une ressource

* Pour qu’une application soit une ressource dans un package d’accès, elle doit disposer d’au moins un rôle de ressource attribuable. Les rôles sont définis par l’application elle-même, et ils sont managés dans Azure AD. Notez que le portail Azure peut également afficher les principaux de service des services qui ne peuvent pas être sélectionnés en tant qu’applications.  Plus particulièrement, **Exchange Online** et **SharePoint Online** sont des services, pas des applications disposant de rôles de ressources dans l’annuaire, ils ne peuvent donc pas être dans un package d’accès.  Utilisez plutôt la gestion des licences par groupe pour établir une licence appropriée, destinée à un utilisateur qui a besoin d’accéder à ces services.

* Pour qu’un groupe soit une ressource dans un package d’accès, il doit pouvoir être modifiable dans Azure AD.  Les groupes issus d’une instance Active Directory locale ne peuvent pas être attribués en tant que ressources, car leurs attributs de propriétaire ou de membre ne sont pas modifiables dans Azure AD.  

* Les documents individuels et les bibliothèques SharePoint Online ne peuvent pas être ajoutés en tant que ressources.  Créez plutôt un groupe de sécurité Azure AD, incluez ce groupe et un rôle de site dans le package d’accès, puis dans SharePoint Online, utilisez ce groupe pour contrôler l’accès au document ou à la bibliothèque de documents.

* Si des utilisateurs ont déjà été affectés à une ressource que vous souhaitez gérer avec un package d’accès, assurez-vous qu’ils sont affectés au package d’accès avec une stratégie appropriée. Par exemple, vous envisagez d’inclure un groupe dans un package d’accès qui contient déjà des utilisateurs dans le groupe. Si ces utilisateurs dans le groupe nécessitent un accès permanent, ils doivent avoir une stratégie appropriée pour les packages d’accès, afin de ne pas perdre leur accès au groupe. Vous pouvez affecter le package d’accès soit en indiquant aux utilisateurs qu’ils demandent le package d’accès contenant cette ressource, soit en les affectant directement au package d’accès. Pour plus d’informations, consultez [Modifier et gérer un package d’accès existant](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Check-list pour la fourniture de l’accès à des utilisateurs externes

* S’il existe une [liste verte](../b2b/allow-deny-list.md) B2B, les utilisateurs dont les annuaires ne sont pas autorisés sont dans l’incapacité de demander l’accès.

* Assurez-vous qu’il n’y a aucune [stratégie d’accès conditionnel](../conditional-access/require-managed-devices.md) empêchant les utilisateurs externes de demander l’accès ou de pouvoir utiliser les applications dans les packages d’accès.

## <a name="checklist-for-request-issues"></a>Check-list pour les problèmes de demande

* Lorsqu’un utilisateur souhaite demander l’accès à un package d’accès, assurez-vous qu’il utilise le **lien du portail Mon accès** pour le package d’accès. Pour plus d’informations, consultez [Copier le lien du portail Mon Accès](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Lorsqu’un utilisateur se connecte au portail Mon accès pour demander un package d’accès, assurez-vous qu’il s’authentifie à l’aide de son compte professionnel ou scolaire. Le compte professionnel ou scolaire peut être un compte présent dans l’annuaire de ressources, ou dans un annuaire qui est inclus dans l’une des stratégies du package d’accès. Si le compte de l’utilisateur n’est pas un compte professionnel ou scolaire, ou si l’annuaire n’est pas inclus dans la stratégie, l’utilisateur ne voit pas le package d’accès. Pour plus d’informations, consultez [Demander l’accès à un package d’accès](entitlement-management-request-access.md).

* Si un utilisateur est empêché de se connecter à l’annuaire de ressources, il est dans l’incapacité de demander l’accès dans le portail Mon accès. Avant que l’utilisateur puisse demander l’accès, vous devez supprimer le bloc de connexion du profil de l’utilisateur. Pour supprimer le bloc de connexion, dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Utilisateurs** ; cliquez ensuite sur l’utilisateur concerné, puis sur **Profil**. Modifiez la section **Paramètres** et remplacez **Bloquer la connexion** par **Non**. Pour plus d’informations, consultez [Ajouter ou mettre à jour les informations du profil utilisateur avec Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Vous pouvez également vérifier si l’utilisateur a été bloqué à cause d’une [Stratégie dIdentity Protection](../identity-protection/howto-unblock-user.md).

* Dans le portail Mon accès, si un utilisateur est en même temps demandeur et approbateur, il ne peut pas voir sa demande pour un package d’accès dans la page **Approbations**. Ce comportement est intentionnel : un utilisateur ne peut pas approuver sa propre demande. Vérifiez que le package d’accès demandé compte des approbateurs supplémentaires configurés sur la stratégie. Pour plus d’informations, consultez [Modifier une stratégie existante](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Si un utilisateur externe, qui ne s’est pas connecté avant à votre annuaire, reçoit un package d’accès, y compris un site SharePoint Online, son package d’accès s’affiche comme n’étant pas entièrement remis tant que son compte ne sera pas provisionné dans SharePoint Online.

## <a name="next-steps"></a>Étapes suivantes

- [Afficher les rapports sur la façon dont les utilisateurs ont eu accès à la gestion des droits d’utilisation](entitlement-management-reports.md)
