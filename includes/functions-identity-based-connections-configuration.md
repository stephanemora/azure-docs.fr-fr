---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/11/2021
ms.author: mahender
ms.openlocfilehash: 8cef6aa5daa8bbbdda6971724343e65ea7e47aef
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992729"
---
Quand elles sont hébergées dans le service Azure Functions, les connexions basées sur une identité utilisent une [identité managée](../articles/app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). L’identité attribuée par le système est utilisée par défaut, bien qu’une identité attribuée par l’utilisateur puisse être spécifiée avec les propriétés `credential` et `clientID`. Lors d’une exécution dans d’autres contextes, tels que le développement local, votre identité de développeur est utilisée à la place, même si cela peut être personnalisé. Consultez [Développement local avec connexions basées sur une identité](../articles/azure-functions/functions-reference.md#local-development-with-identity-based-connections).

#### <a name="grant-permission-to-the-identity"></a>Accorder l’autorisation à l’identité

Quelle que soit l’identité utilisée, elle doit avoir les autorisations nécessaires pour effectuer les actions prévues. Vous devrez [attribuer un rôle dans Azure RBAC](../articles/role-based-access-control/role-assignments-steps.md) en utilisant des rôles intégrés ou personnalisés qui fournissent ces autorisations.

> [!IMPORTANT]
> Parmi les autorisations exposées par le service cible, certaines ne sont peut-être pas nécessaires pour tous les contextes. Dans la mesure du possible, adhérez au **principe du privilège minimum**, en accordant à l’identité uniquement les privilèges nécessaires. Par exemple, si l’application a juste besoin de pouvoir lire à partir d’une source de données, utilisez un rôle qui a uniquement l’autorisation de lecture. Il serait inapproprié d’attribuer un rôle qui autorise aussi l’écriture dans ce service, car ce serait une autorisation excessive pour une opération de lecture. De même, vous voudrez vous assurer que l’attribution de rôle est limitée aux seules ressources qui doivent être lues.
