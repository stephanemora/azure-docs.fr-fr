---
author: baanders
description: Fichier include pour la configuration de l’authentification locale pour DefaultAzureCredential dans les exemples Azure Digital Twins - Avec introduction
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bd8676aaf4a8c084abb7cbc735d3564eeb7cf102
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108739356"
---
### <a name="set-up-local-azure-credentials"></a>Configurer les informations d’identification Azure locales

Cet exemple utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (qui fait partie de la bibliothèque `Azure.Identity`) pour authentifier les utilisateurs auprès de l’instance Azure Digital Twins quand vous l’exécutez sur votre ordinateur local. Pour plus d’informations sur les différentes façons dont une application cliente peut s’authentifier auprès d’Azure Digital Twins, consultez [Guide pratique : Écrire le code d’authentification d’une application](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]