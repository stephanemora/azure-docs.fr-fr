---
author: baanders
description: Fichier include pour la configuration de l’authentification locale pour DefaultAzureCredential dans les exemples Azure Digital Twins - Avec introduction
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 2d1a1636ca1ce7ea3bacaa872178d100ec2536ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437682"
---
### <a name="set-up-local-azure-credentials"></a>Configurer les informations d’identification Azure locales

Cet exemple utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) (qui fait partie de la bibliothèque `Azure.Identity`) pour authentifier les utilisateurs auprès de l’instance Azure Digital Twins quand vous l’exécutez sur votre ordinateur local. Pour plus d’informations sur les différentes façons dont une application cliente peut s’authentifier auprès d’Azure Digital Twins, consultez [Écrire le code d’authentification de l’application](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]