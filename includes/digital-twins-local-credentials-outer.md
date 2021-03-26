---
author: baanders
description: Fichier include pour la configuration de l’authentification locale pour DefaultAzureCredential dans les exemples Azure Digital Twins - Avec introduction
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473727"
---
### <a name="set-up-local-azure-credentials"></a>Configurer les informations d’identification Azure locales

Cet exemple utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (qui fait partie de la bibliothèque `Azure.Identity`) pour authentifier les utilisateurs auprès de l’instance Azure Digital Twins quand vous l’exécutez sur votre ordinateur local. Pour plus d’informations sur les différentes façons dont une application cliente peut s’authentifier auprès d’Azure Digital Twins, consultez [*Guide pratique : Écrire le code d’authentification d’une application*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]