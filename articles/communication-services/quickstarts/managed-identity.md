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
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307436"
---
# <a name="use-managed-identities"></a>Utiliser des identités managées
Prise en main d’Azure Communication Services à l’aide d’identités managées Les kits SDK Communication Services, Identity et SMS prennent en charge l’authentification Azure AD (Azure Active Directory) avec des [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Ce guide de démarrage rapide vous montre comment autoriser l’accès aux kits SDK Identity et SMS à partir d’un environnement Azure qui prend en charge les identités managées. Il explique également comment tester votre code dans un environnement de développement.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
- Une ressource Azure Communication Services active ou consultez [Créer une ressource Communication Services](./create-communication-resource.md) si vous n’en avez pas.
- Pour envoyer un SMS, vous aurez besoin d’un [numéro de téléphone](./telephony-sms/get-phone-number.md).
- Une identité managée par la configuration pour un environnement de développement. Voir [Autoriser l’accès avec une identité managée](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le contrôle d’accès en fonction du rôle dans Azure](../../../articles/role-based-access-control/index.yml)
- [En savoir plus sur la bibliothèque d’identité Azure pour .NET](/dotnet/api/overview/azure/identity-readme)
- [Créer des jetons d’accès utilisateur](../quickstarts/access-tokens.md)
- [Envoyer un message SMS](../quickstarts/telephony-sms/send.md)
- [En savoir plus sur les SMS](../concepts/telephony-sms/concepts.md)
