---
author: baanders
description: Fichier include pour DefaultAzureCredential dans les exemples Azure Digital Twins - Remarque
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 462098536ae4598c7875a1b8291e477f6b4a8d7d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108778367"
---
>[!NOTE]
> Cet exemple utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (qui fait partie de la bibliothèque de `Azure.Identity`) pour authentifier les utilisateurs avec l’instance Azure Digital Twins lorsque vous l’exécutez sur votre ordinateur local. Avec ce type d’authentification, l’exemple recherche les informations d’identification Azure dans votre environnement local, comme une connexion à partir d’une instance [Azure CLI](/cli/azure/install-azure-cli) locale ou dans Visual Studio/Visual Studio Code.
>
> Pour plus d’informations sur l’utilisation de `DefaultAzureCredential` et d’autres options d’authentification, consultez [Guide pratique : Écrire le code d’authentification d’une application](../articles/digital-twins/how-to-authenticate-client.md).