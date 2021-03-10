---
author: baanders
description: Fichier include pour DefaultAzureCredential dans les exemples Azure Digital Twins - Remarque
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473794"
---
>[!NOTE]
> Cet exemple utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (qui fait partie de la bibliothèque de `Azure.Identity`) pour authentifier les utilisateurs avec l’instance Azure Digital Twins lorsque vous l’exécutez sur votre ordinateur local. Avec ce type d’authentification, l’exemple recherche les informations d’identification Azure dans votre environnement local, comme une connexion à partir d’une instance [Azure CLI](/cli/azure/install-azure-cli) locale ou dans Visual Studio/Visual Studio Code.
>
> Pour plus d’informations sur l’utilisation de `DefaultAzureCredential` et d’autres options d’authentification, consultez [*Guide pratique : Écrire le code d’authentification d’une application*](../articles/digital-twins/how-to-authenticate-client.md).