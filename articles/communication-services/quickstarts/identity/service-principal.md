---
title: Utiliser Azure Active Directory dans Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Azure Active Directory vous permet d’autoriser l’accès à Azure Communication Services à partir d’applications s’exécutant dans des machines virtuelles Azure, des applications de fonction et d’autres ressources.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 3559dc9f7adc10f11e13e18468bbbd964bf5db04
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253504"
---
# <a name="quickstart-authenticate-using-azure-active-directory"></a>Démarrage rapide : S’authentifier à l’aide d’Azure Active Directory

Commencez à utiliser Azure Communication Services avec Azure Active Directory. Les SDK Communication Services Identity et SMS prennent en charge l’authentification Azure AD (Azure Active Directory).

Ce guide de démarrage rapide vous montre comment autoriser l’accès aux SDK Identity et SMS à partir d’un environnement Azure qui prend en charge Active Directory. Elle explique également comment tester votre code dans un environnement de développement en créant un principal de service pour votre travail.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
- Une ressource Azure Communication Services active ou consultez [Créer une ressource Communication Services](../create-communication-resource.md) si vous n’en avez pas.
- Pour envoyer un SMS, vous aurez besoin d’un [numéro de téléphone](../telephony-sms/get-phone-number.md).
- Un principal de service configuré pour un environnement de développement. Voir [Autoriser l’accès avec une identité managée](./service-principal-from-cli.md).

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/active-directory/service-principal-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/active-directory/service-principal-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/active-directory/service-principal-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/active-directory/service-principal-python.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le contrôle d’accès en fonction du rôle dans Azure](../../../../articles/role-based-access-control/index.yml)
- [En savoir plus sur la bibliothèque d’identité Azure pour .NET](/dotnet/api/overview/azure/identity-readme)
- [Créer des jetons d’accès utilisateur](../../quickstarts/access-tokens.md)
- [Envoyer un message SMS](../../quickstarts/telephony-sms/send.md)
- [En savoir plus sur les SMS](../../concepts/telephony-sms/concepts.md)
- [Créer rapidement une identité à des fins de test](./quick-create-identity.md)

