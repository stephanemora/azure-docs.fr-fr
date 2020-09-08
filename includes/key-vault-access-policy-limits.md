---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380414"
---
Le coffre de clés prend en charge jusqu’à 1024 entrées de stratégie d’accès, chaque entrée accordant un ensemble distinct d’autorisations à un principal de sécurité particulier. En raison de cette limitation, nous vous recommandons d’attribuer des stratégies d’accès, autant que possible, à des groupes d’utilisateurs plutôt qu’à des utilisateurs individuels. L’utilisation de groupes facilite grandement la gestion des autorisations pour plusieurs personnes au sein de votre organisation. Pour plus d’informations, consultez [Gérer l’accès aux applications et aux ressources à l’aide de groupes Azure Active Directory](/azure/active-directory/fundamentals/active-directory-manage-groups).

Pour plus d’informations sur le contrôle d’accès Key Vault, consultez [Sécurité d’Azure Key Vault : Gestion de l’identité et de l’accès](/azure/key-vault/general/overview-security#identity-and-access-management). 
