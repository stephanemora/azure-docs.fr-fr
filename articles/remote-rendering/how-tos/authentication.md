---
title: Authentification
description: Explique le fonctionnement de l’authentification
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195302"
---
# <a name="configure-authentication"></a>configurer l’authentification ;

Azure Remote Rendering utilise un mécanisme d’authentification identique à [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Les clients doivent définir *AccountKey*, *AuthenticationToken* ou *AccessToken* pour parvenir à appeler les API REST. *AccountKey* peut être obtenu sous l’onglet « Clés » correspondant au compte Remote Rendering dans le portail Azure. *AuthenticationToken* est un jeton Azure AD, qui peut être obtenu à l’aide de la [bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* est un jeton MR, qui peut être obtenu à partir du service d’émission de jeton de sécurité (STS) Azure Mixed Reality.

## <a name="authentication-for-deployed-applications"></a>Authentification des applications déployées

 L’utilisation de clés de compte est recommandée pour l’intégration rapide, mais uniquement durant le développement/prototypage. Nous vous déconseillons vivement de ne pas livrer votre application en production avec une clé de compte incorporée, mais d’adopter plutôt les approches d’authentification Azure AD basées sur le service ou l’utilisateur.

 L’authentification Azure AD est décrite dans la section [Authentification utilisateur Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) du service [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/).

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Pour vous aider à contrôler le niveau d’accès accordé aux applications, services ou utilisateurs Azure AD de votre service, les rôles suivants ont été créés. Vous pouvez les affecter en fonction des besoins sur vos comptes Azure Remote Rendering :

* **Administrateur Remote Rendering** : Fournit à l’utilisateur des fonctionnalités de conversion, de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering.
* **Client Remote Rendering** : Fournit à l’utilisateur des fonctionnalités de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte](create-an-account.md)
* [Utiliser les API Azure Frontend pour l’authentification](frontend-apis.md)
* [Exemples de scripts PowerShell](../samples/powershell-example-scripts.md)
