---
title: Utiliser des identités managées dans Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Les identités managées vous permettent d’autoriser l’accès à Azure Communication Services à partir d’applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, et d’autres ressources.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e4b71cc889615fd4784f11c9edd77b44ef421d9e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110732"
---
# <a name="use-managed-identities"></a>Utiliser des identités managées
Prise en main d’Azure Communication Services à l’aide d’identités managées Les kits SDK Communication Services, Identity et SMS prennent en charge l’authentification Azure AD (Azure Active Directory) avec des [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Ce guide de démarrage rapide vous montre comment autoriser l’accès aux kits SDK Identity et SMS à partir d’un environnement Azure qui prend en charge les identités managées. Il explique également comment tester votre code dans un environnement de développement.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le contrôle d’accès en fonction du rôle dans Azure](../../../articles/role-based-access-control/index.yml)
- [En savoir plus sur la bibliothèque d’identité Azure pour .NET](/dotnet/api/overview/azure/identity-readme)
- [Créer des jetons d’accès utilisateur](../quickstarts/access-tokens.md)
- [Envoyer un message SMS](../quickstarts/telephony-sms/send.md)
- [En savoir plus sur les SMS](../concepts/telephony-sms/concepts.md)
