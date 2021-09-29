---
title: 'Démarrage rapide : Créer rapidement des identités Azure Communication Services à des fins de test'
titleSuffix: An Azure Communication Services quickstart
description: Apprenez à utiliser l’outil Identités et jetons d’accès dans le portail Azure pour l’utiliser avec des exemples et à des fins de dépannage.
author: manoskow
manager: chpalm
services: azure-communication-services
ms.author: manoskow
ms.date: 07/19/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 63a08ba4d117160177124d1951720f6d9a4fedcc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671922"
---
# <a name="quickstart-quickly-create-azure-communication-services-access-tokens-for-testing"></a>Démarrage rapide : Créer rapidement des jetons d’accès Azure Communication Services à des fins de test

Dans l’extension Communication Services du [portail Azure](https://portal.azure.com), vous pouvez générer une identité et un jeton d’accès Communication Services. Vous pouvez ainsi éviter de créer un service d’authentification, ce qui vous permet de tester plus facilement les exemples d’applications et des scénarios de développement simples. Cette fonctionnalité est destinée à la validation et aux tests à petite échelle et ne doit pas être utilisée pour des scénarios de production. Pour le code de production, reportez-vous au [démarrage rapide relatif à la création de jetons d’accès](../access-tokens.md).

L’outil présente le comportement de ```Identity SDK``` dans une expérience utilisateur simple. Les jetons et les identités créés à l’aide de cet outil suivent les mêmes comportements et règles que s’ils avaient été créés à l’aide de ```Identity SDK```.  Par exemple, les jetons d’accès expirent après 24 heures.

## <a name="prerequisites"></a>Prérequis

- Une [ressource Azure Communication Services](../create-communication-resource.md)

## <a name="create-the-access-tokens"></a>Créer les jetons d’accès

Dans le [portail Azure](https://portal.azure.com), accédez au panneau **Identités et jetons d’accès utilisateur** de votre ressource Communication Services. 

Choisissez l’étendue des jetons d’accès. Vous pouvez sélectionner aucun, un ou plusieurs. Cliquez sur **Générer**.

![Sélectionnez les étendues de l’identité et des jetons d’accès.](../media/quick-create-identity-choose-scopes.png)

Vous verrez qu’une identité et un jeton d’accès utilisateur correspondant sont générés. Vous pouvez copier ces chaînes et les utiliser dans les [exemples d’applications](../../samples/overview.md) et d’autres scénarios de test.

![L’identité et les jetons d’accès sont générés et affichent la date d’expiration.](../media/quick-create-identity-generated.png)

## <a name="next-steps"></a>Étapes suivantes


Vous voudrez peut-être aussi :

 - [Découvrir l’authentification](../../concepts/authentication.md)
 - [En savoir plus sur l’architecture client et serveur](../../concepts/client-and-server-architecture.md)
