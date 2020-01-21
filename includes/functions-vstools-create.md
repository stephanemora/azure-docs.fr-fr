---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a27d44a7684b4ad3d39d7fba50ca52e08e932971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769764"
---
Le modèle de projet Azure Functions dans Visual Studio crée un projet qui peut être publié dans une application de fonction dans Azure. Vous pouvez utiliser une application de fonction pour regrouper des fonctions en une unité logique afin de faciliter la gestion, le déploiement, la mise à l’échelle et le partage des ressources.

1. Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.

1. Dans la boîte de dialogue **Créer un projet**, recherchez `functions`, choisissez le modèle **Azure Functions**, puis sélectionnez **Suivant**.

1. Entrez un nom pour votre projet, puis sélectionnez **Créer**. Le nom d’application de la fonction doit être valide en tant qu’espace de noms C#, afin de ne pas utiliser des traits d’union, des traits de soulignement ou d’autres caractères non alphanumériques.

1. Dans **Créer une application Azure Functions**, utilisez les options suivantes :

    + **Azure Functions v2 (.NET Core)**
    + **Déclencheur HTTP**
    + **Compte de stockage** : **Émulateur de stockage**
    + **Niveau d’autorisation** : **Anonyme** 

    | Option      | Valeur suggérée  | Description                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Runtime Functions** | **Azure Functions 2.x <br />(.NET Core)** | Ce paramètre crée un projet de fonction qui utilise le runtime d’Azure Functions version 2.x qui prend en charge .NET Core. La version 1.x d’Azure Functions prend en charge .NET Framework. Pour plus d’informations, consultez [Cibler la version du runtime Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Modèle de fonction** | **Déclencheur HTTP** | Ce paramètre crée une fonction déclenchée par une demande HTTP. |
    | **Compte de stockage**  | **Émulateur de stockage** | Un déclencheur HTTP n’utilise pas la connexion de compte de stockage Azure. Tous les autres types de déclencheurs nécessitent une chaîne de connexion de compte de stockage valide. Étant donné que Functions requiert un compte de stockage, celui-ci est attribué ou créé lorsque vous publiez votre projet sur Azure. |
    | **Niveau d’autorisation** | **Anonyme** | La fonction créée peut être déclenchée par n’importe quel client sans fournir une clé. Ce paramètre d’autorisation facilite le test de votre nouvelle fonction. Pour plus d’informations sur les clés et autorisations, consultez [Clés d’autorisation](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) dans [HTTP et liaisons de webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Veillez à définir le **Niveau d’autorisation** sur `Anonymous`. Si vous choisissez le niveau par défaut de `Function`, vous êtes invité à présenter la [clé de fonction](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) dans les demandes d’accès à votre point de terminaison de fonction.
    
4. Sélectionnez **Créer** pour créer le projet de fonction et la fonction déclenchée par HTTP.
