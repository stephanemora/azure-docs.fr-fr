---
title: Désactiver PTA avec « Ne pas configurer » d’Azure AD Connect | Microsoft Docs
description: Cet article explique comment désactiver PTA avec la fonctionnalité Azure AD Connect « Ne pas configurer ».
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acabc2cf177ec81ecc293398f9b43f42e71c2862
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234905"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Désactiver PTA lors de l’utilisation d’Azure AD Connect

Si vous utilisez l’authentification directe avec Azure AD Connect et l’avez définie sur **« Ne pas configurer »** , vous pouvez la désactiver. 

>[!NOTE]
>Si vous avez déjà activé PHS, la désactivation de PTA entraînera le basculement du locataire sur PHS.

Pour désactiver PTA, utilisez les cmdlets suivantes. 

## <a name="prerequisites"></a>Prérequis
Les prérequis suivants sont obligatoires :
- Toute machine Windows sur lequel l’agent PTA est installé. 
- L'agent doit correspondre à la version 1.5.1742.0 ou ultérieure. 
- Un compte d’administrateur général Azure pour exécuter les cmdlets PowerShell et désactiver PTA.

>[!NOTE]
> Un agent antérieur peut ne pas disposer des cmdlets requises pour mener à bien cette opération. Vous pouvez obtenir un nouvel agent depuis le portail Azure, l'installer sur une machine Windows et fournir les informations d’identification d’administrateur. (L’installation de l’agent n’affecte pas l’état PTA dans le cloud)

> [!IMPORTANT]
> Si vous utilisez le cloud Azure Government, vous devez transmettre le paramètre ENVIRONMENTNAME avec la valeur suivante. 
>
>| Nom de l’environnement | Cloud |
>| - | - |
>| AzureUSGovernment | Gouvernement des États-Unis|


## <a name="to-disable-pta"></a>Pour désactiver PTA
À partir d’une session PowerShell, utilisez la commande suivante pour désactiver PTA :
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` ou `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` ou `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Si vous n'avez accès à aucun agent

Si vous ne disposez pas de machine agent, vous pouvez utiliser la commande suivante pour installer un agent.

1. Téléchargez l'agent d’authentification le plus récent depuis portal.azure.com.
2. Installez la fonctionnalité : `.\AADConnectAuthAgentSetup.exe` ou `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Étapes suivantes

- [Connexion de l’utilisateur avec l’authentification directe Azure Active Directory](how-to-connect-pta.md)
