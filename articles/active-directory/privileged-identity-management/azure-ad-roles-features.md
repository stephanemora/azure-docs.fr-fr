---
title: Gérer les rôles Azure AD dans Privileged Identity Management (PIM) | Microsoft Docs
description: Guide pratique pour gérer les rôles Azure AD afin de les attribuer dans Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225469"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Fonctionnalités de gestion des rôles Azure AD dans Privileged Identity Management

L’expérience de gestion des rôles Azure AD dans Privileged Identity Management a été mise à jour dans un souci de cohérence avec l’expérience de gestion des rôles de ressources Azure. Auparavant, Privileged Identity Management pour les rôles de ressources Azure comportait quelques fonctionnalités clés qui n’étaient pas disponibles pour les rôles Azure AD.

Dans le cadre de la mise à jour en cours de déploiement, nous fusionnons les deux en une expérience de gestion unique offrant les mêmes fonctionnalités pour les rôles Azure AD que pour les rôles de ressources Azure. Cet article vous informe des fonctionnalités mises à jour et des conditions requises.


## <a name="time-bound-assignments"></a>Attributions limitées dans le temps

Dans Privileged Identity Management pour les rôles Azure AD, les attributions de rôles comportaient deux états possibles : *éligible* et *permanente*. Il est maintenant possible de définir une heure de début et de fin pour chaque type d’attribution. Cet ajout donne quatre états possibles pour une attribution :

- Éligible de façon permanente
- Active de façon permanente
- Éligible, avec les dates de début/fin spécifiées pour l’attribution
- Active, avec les dates de début/fin spécifiées pour l’attribution

Dans la plupart des cas, même si vous ne souhaitez pas que les utilisateurs aient une attribution éligible et activent les rôles à chaque fois, vous pouvez protéger votre organisation Azure AD en définissant un délai d’expiration des affectations. Si, par exemple, vous avez des utilisateurs temporaires éligibles, définissez une date d’expiration afin de les supprimer automatiquement de l’attribution de rôle lorsque leur travail est terminé.

## <a name="new-role-settings"></a>Nouveaux paramètres de rôle

Nous ajoutons également de nouveaux paramètres pour les rôles Azure AD. Auparavant, les paramètres d’activation (par exemple, les exigences d’authentification multifacteur et de ticket d’incident/de demande) ne pouvaient être configurés que rôle par rôle. Autrement dit, ils étaient appliqués à tous les utilisateurs éligibles pour un rôle spécifié. Il est maintenant possible de configurer si un utilisateur individuel doit effectuer une authentification multifacteur pour pouvoir activer un rôle. En outre, vous pouvez exercer un contrôle avancé de vos e-mails Privileged Identity Management relatifs à des rôles spécifiques.

## <a name="extend-and-renew-assignments"></a>Étendre et renouveler les attributions

La première question qui se pose avec les attributions limitées dans le temps est la suivante : que se passe-t-il si un rôle expire ? Dans cette nouvelle version, nous proposons deux options pour ce scénario :

- Étendre : quand une attribution de rôle arrive à expiration, l’utilisateur peut utiliser Privileged Identity Management pour demander une extension de cette attribution de rôle.
- Renouveler : quand une attribution de rôle est expirée, l’utilisateur peut utiliser Privileged Identity Management pour demander un renouvellement de cette attribution de rôle.

Ces deux actions utilisateur exigent l’approbation d’un Administrateur général ou d’un Administrateur de rôle privilégié. Les administrateurs n’ont plus besoin d’être spécialisés dans la gestion de ces expirations. Il leur suffit d’attendre les demandes d’extension ou de renouvellement et de les approuver si elles sont valides.

## <a name="api-changes"></a>Modifications d'API

Quand la version mise à jour aura été déployée dans l’organisation Azure AD des clients, l’API Graph actuelle cessera de fonctionner. Vous devrez passer à [l’API Graph pour les rôles de ressources Azure](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Pour gérer les rôles Azure AD avec cette API, remplacez `/azureResources` par `/aadroles` dans la signature et utilisez l’ID répertoire de `resourceId`.

Nous avons fait tout notre possible pour contacter tous les clients qui utilisent l’ancienne API et les informer de cette modification à l’avance. Si votre organisation Azure AD est passée à la nouvelle version et que vous dépendez toujours de l’ancienne API, contactez l’équipe à l’adresse pim_preview@microsoft.com.

## <a name="powershell-change"></a>Modification de PowerShell

Pour les clients qui l’utilisent pour les rôles Azure AD, le module PowerShell Privileged Identity Management cessera de fonctionner avec la mise à jour. À la place des cmdlets précédentes, vous devrez utiliser les cmdlets Privileged Identity Management dans le module PowerShell Azure AD Preview. Installez le module Azure AD PowerShell à partir de la [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Vous pouvez maintenant [consulter la documentation et les exemples d'opérations PIM dans ce module PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer un rôle personnalisé Azure AD](azure-ad-custom-roles-assign.md)
- [Supprimer ou mettre à jour une attribution de rôle personnalisée Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurer une attribution de rôle Azure AD personnalisée](azure-ad-custom-roles-configure.md)
- [Définitions de rôles dans Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
