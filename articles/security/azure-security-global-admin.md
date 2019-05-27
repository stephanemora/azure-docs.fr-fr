---
title: Activer MFA pour tous les administrateurs Azure
description: Conseils sur l’activation de l’administrateur général
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: fbc3bba3d95526277da3487cb8670c1f1a88a925
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951759"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Mettre en œuvre l’authentification multifacteur (MFA) pour les administrateurs d’abonnement

Lorsque vous créez vos administrateurs, notamment votre compte d’administrateur général, il est essentiel d’utiliser des méthodes d’authentification fortes.

Vous pouvez effectuer les tâches d’administration quotidiennes en attribuant des rôles d’administrateur spécifiques, comme administrateur d’Exchange ou administrateur de mot de passe, aux comptes utilisateur du personnel informatique en fonction des besoins.
En outre, l’activation [d’Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) pour vos administrateurs ajoute une deuxième couche de sécurité aux connexions et aux transactions des utilisateurs. Azure MFA permet également au département informatique de réduire le risque d’accès aux données de l’organisation par un compte compromis.

Exemple : Vous appliquer Azure MFA pour vos utilisateurs et configurez pour utiliser un appel téléphonique ou un message texte en tant que la vérification. Si les informations d’identification sont compromises, l’attaquant ne pourrez pas accéder à n’importe quelle ressource dans la mesure où il n’aura accès au téléphone de l’utilisateur. Les organisations qui n’ajoutent pas de couche supplémentaire de protection d’identité sont plus sensibles au vol d’informations d’identification, susceptible de compromettre des données.

Les organisations souhaitant conserver le contrôle complet de l’authentification en local peuvent recourir au [serveur Microsoft Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), ou « MFA local ». Grâce à cette méthode, vous pourrez toujours appliquer l’authentification multi-facteur, tout en conservant le serveur MFA en local.

Pour vérifier qui dans votre organisation dispose de privilèges d’administrateur, vous pouvez utiliser la commande PowerShell Microsoft Azure AD V2 suivante :

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Activation de MFA

Avant de continuer, vérifiez comment [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) fonctionne.

Tant que vos utilisateurs disposent de licences comprenant Azure Multi-Factor Authentication, vous n’avez rien à faire pour activer Azure Multi-Factor Authentication (MFA). Vous pouvez commencer par demander une vérification en deux étapes pour les différents utilisateurs. Les licences suivantes prennent en charge Azure MFA :

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Activer la vérification en deux étapes pour les utilisateurs

Utilisez l’une des procédures répertoriées dans [How to require two-step verification for a user or group](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) (Exiger la vérification en deux étapes pour un utilisateur ou groupe) pour commencer à utiliser Azure Multi-Factor Authentication. Vous pouvez choisir d’appliquer la vérification en deux étapes pour toutes les connexions, ou de créer des règles d’accès conditionnel, ce qui permet d’exiger la vérification en deux étapes lorsque vous le souhaitez.

