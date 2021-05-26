---
author: baanders
description: Fichier include pour DefaultAzureCredential dans les exemples Azure Digital Twins - Remarque
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 1275e7e230fedc4fae77592bf1232e62ca757b8b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078924"
---
>[!NOTE]
> Cet exemple utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) (qui fait partie de la bibliothèque de `Azure.Identity`) pour authentifier les utilisateurs avec l’instance Azure Digital Twins lorsque vous l’exécutez sur votre ordinateur local. Avec ce type d’authentification, l’exemple recherche les informations d’identification Azure dans votre environnement local, comme une connexion à partir d’une instance [Azure CLI](/cli/azure/install-azure-cli) locale ou dans Visual Studio/Visual Studio Code.
>
> Pour plus d’informations sur l’utilisation de `DefaultAzureCredential` et d’autres options d’authentification, consultez [Guide pratique : Écrire le code d’authentification d’une application](../articles/digital-twins/how-to-authenticate-client.md).