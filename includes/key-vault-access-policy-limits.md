---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934530"
---
Le coffre de clés prend en charge jusqu’à 1024 entrées de stratégie d’accès, chaque entrée accordant un ensemble distinct d’autorisations à un principal de sécurité particulier. En raison de cette limitation, nous vous recommandons d’attribuer des stratégies d’accès, autant que possible, à des groupes d’utilisateurs plutôt qu’à des utilisateurs individuels. L’utilisation de groupes facilite grandement la gestion des autorisations pour plusieurs personnes au sein de votre organisation. Pour plus d’informations, consultez [Gérer l’accès aux applications et aux ressources à l’aide de groupes Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md).

Pour plus d’informations sur le contrôle d’accès Key Vault, consultez [Sécurité d’Azure Key Vault : Gestion de l’identité et de l’accès](../articles/key-vault/general/security-overview.md#identity-management).