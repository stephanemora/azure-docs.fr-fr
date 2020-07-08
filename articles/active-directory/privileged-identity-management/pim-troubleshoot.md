---
title: Détecter un problème avec Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Découvrez comment détecter un problème sur des erreurs système avec des rôles dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d2ba5806e23aa87e5d7787be300512444f95b25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742145"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Détecter un problème avec Privileged Identity Management

Rencontrez-vous un problème avec Privileged Identity Management (PIM) dans Azure Active Directory (Azure AD) ? Les informations ci-après peuvent vous aider à le résoudre.

## <a name="access-to-azure-resources-denied"></a>Accéder aux ressources Azure refusées

### <a name="problem"></a>Problème

En tant que propriétaire actif ou administrateur de l’accès utilisateur pour une ressource Azure, vous pouvez voir votre ressource dans Privileged Identity Management mais vous ne pouvez pas effectuer d’action comme effectuer une attribution éligible ou afficher une liste d’attributions de rôles à partir de la page d’aperçu des ressources. L’une de ces actions génère une erreur d’autorisation.

### <a name="cause"></a>Cause :

Ce problème peut se produire lorsque le rôle administrateur de l’accès utilisateur pour le principal du service PIM a été accidentellement supprimé de l’abonnement. Pour que le service Privileged Identity Management puisse accéder aux ressources Azure, le principal du service MS-PIM doit toujours avoir le [rôle administrateur d’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) sur l’abonnement Azure.

### <a name="resolution"></a>Résolution

Attribuez le rôle d’administrateur de l’accès utilisateur au nom de principal du service Privileged Identity Management (MS – PIM) au niveau de l’abonnement. Cette attribution doit autoriser le service Privileged Identity Management à accéder aux ressources Azure. Le rôle peut être attribué au niveau d’un groupe d’administration ou au niveau de l’abonnement, en fonction de vos besoins. Pour plus d’informations sur les principaux de service, consultez [Attribuer une application à un rôle](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Étapes suivantes

- [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md)
- [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Déployer Privileged Identity Management](pim-deployment-plan.md)
