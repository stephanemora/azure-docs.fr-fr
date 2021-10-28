---
title: Problèmes de gestion d’Azure Virtual Desktop - Azure
description: Problèmes de gestion courants dans Azure Virtual Desktop et comment les résoudre.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 7632bbdda8515476cc88fa0735fcd41b9eb5444a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245812"
---
# <a name="management-issues"></a>Problèmes de gestion

Cet article décrit les erreurs de gestion courantes et donne des suggestions pour les résoudre.

## <a name="common-management-errors"></a>Erreurs de gestion communes

Le tableau suivant répertorie les messages d’erreur qui s’affichent en raison de problèmes liés à la gestion, ainsi que des suggestions sur la façon de les résoudre.

|Message d’erreur|Solution suggérée|
|---|---|
|La création de la clé d’inscription a échoué. |Impossible de créer le jeton d’inscription. Réessayez de le créer avec un délai d’expiration plus court (entre 1 heure et 1 mois). |
|La suppression de la clé d’inscription a échoué.|Impossible de supprimer le jeton d’inscription. Réessayez de le supprimer. Si cela ne fonctionne toujours pas, utilisez PowerShell pour vérifier si le jeton est toujours présent. Si c’est le cas, supprimez-le avec PowerShell.|
|Le changement du mode maintenance de l’hôte de session a échoué |Impossible de modifier le mode maintenance sur la machine virtuelle. Vérifiez l’état de la machine virtuelle. Si la machine virtuelle n’est pas disponible, vous ne pouvez pas modifier le mode maintenance.|
|La déconnexion de sessions utilisateur a échoué |Impossible de déconnecter l’utilisateur de la machine virtuelle. Vérifiez l’état de la machine virtuelle. Si la machine virtuelle n’est pas disponible, vous ne pouvez pas déconnecter la session utilisateur. Si la machine virtuelle est disponible, vérifiez l’état de la session utilisateur pour voir si elle est déconnectée. |
|La fermeture de session de tous les utilisateurs de l’hôte de session a échoué |Impossible de déconnecter les utilisateurs de la machine virtuelle. Vérifiez l’état de la machine virtuelle. S’il n’est pas disponible, il n’est pas possible de déconnecter les utilisateurs. Vérifiez l’état de la session utilisateur pour voir s’ils sont déconnectés. Vous pouvez forcer la déconnexion avec PowerShell. |
|La dissociation de l’utilisateur du groupe d’applications a échoué|Impossible d’annuler la publication d’un groupe d’applications pour un utilisateur. Vérifiez si l’utilisateur est disponible sur Azure AD. Vérifiez si l’utilisateur fait partie d’un groupe d’utilisateurs sur lequel le groupe d’applications est publié. |
|Une erreur s’est produite lors de la récupération des emplacements disponibles |Vérifiez l’emplacement de la machine virtuelle utilisée dans l’Assistant Création d’un pool d’hôtes. Si l’image n’est pas disponible dans cet emplacement, ajoutez une image à cet emplacement ou choisissez un autre emplacement de machine virtuelle. |

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Erreur : Impossible d’ajouter des affectations d’utilisateurs à un groupe d’applications

Après l’affectation d’un utilisateur à un groupe d’applications, le portail Azure affiche un avertissement indiquant « Fin de session » ou « Problèmes d’authentification - Extension Microsoft_Azure_WVD ». La page d’affectation n’est alors pas chargée, et ensuite, les pages cessent de se charger dans le portail Azure (par exemple, Azure Monitor, Log Analytics, Service Health, etc.).

Ce problème survient généralement en raison d’un problème avec la stratégie d’accès conditionnel. Le portail Azure tente d’obtenir un jeton pour Microsoft Graph, qui dépend de SharePoint Online. Le client dispose d’une stratégie d’accès conditionnel appelée « Microsoft Office 365 Data Storage Terms of Use », qui oblige les utilisateurs à accepter les conditions d’utilisation pour accéder au stockage des données. Toutefois, les utilisateurs ne se sont pas encore connectés, donc le portail Azure ne peut pas récupérer le jeton.

Pour résoudre ce problème, avant de se connecter au portail Azure, l’administrateur doit d’abord se connecter à SharePoint et accepter les conditions d’utilisation. Ensuite, il devrait être en mesure de se connecter au portail Azure normalement.

## <a name="next-steps"></a>Étapes suivantes

Pour examiner les scénarios d’erreur courants que la fonctionnalité de diagnostic peut identifier pour vous, consulter [Identifier et diagnostiquer les problèmes](./troubleshoot-set-up-overview.md).
