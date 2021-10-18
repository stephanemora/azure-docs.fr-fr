---
title: 'Guide pratique : Utiliser AzureClient pour les tests locaux'
description: Comment utiliser AzureClient pour tester une application sans service
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: f73c5e62471f6c038d7338244ec4756b533fe2d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661959"
---
# <a name="how-to-use-azureclient-for-local-testing"></a>Guide pratique : Utiliser AzureClient pour les tests locaux

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Cet article décrit les étapes de configuration d’**AzureClient** en mode local et comment l’utiliser pour tester votre application Fluid localement.

## <a name="configure-and-create-an-azureclient"></a>Configurer et créer une instance AzureClient

**AzureClient** peut être configuré pour s’exécuter sur une instance Relais Azure Fluid locale en lui passant une configuration comme celle qui suit.

```js
    import { AzureClient, AzureConnectionConfig, LOCAL_MODE_TENANT_ID } from "@fluidframework/azure-client";
    import { InsecureTokenProvider } from "@fluidframework/test-client-utils";

    const clientProps = {
        connection: {
            tenantId: LOCAL_MODE_TENANT_ID,
            tokenProvider: new InsecureTokenProvider("", { id: "123", name: "Test User" }),
            orderer: "http://localhost:7070",
            storage: "http://localhost:7070",
        },
    };

    const azureClient = new AzureClient(clientProps);
```

Cet exemple utilise **InsecureTokenProvider** pour générer et signer des jetons d’authentification que le service Relais Azure Fluid doit accepter. Toutefois, comme son nom l’indique, il n’est pas sécurisé et ne doit pas être utilisé dans des environnements de production. Pour plus d’informations sur InsecureTokenProvider, consultez [Authentification et autorisation dans votre application](https://fluidframework.com/docs/build/auth/#the-token-provider).

Pour une exécution locale, vous devez d’abord configurer les URL de l’ordonnanceur et du stockage de sorte qu’elles pointent vers le domaine et le port sur lesquels l’instance du service Relais Azure Fluid locale s’exécute (http://localhost:7070 par défaut). La dernière étape consiste à définir `tenantId` sur `LOCAL_MODE_TENANT_ID`. Tous ces paramètres configurent AzureClient pour qu’il fonctionne avec un service Relais Azure Fluid local.  

## <a name="enabling-debug-logging"></a>Enabling debug logging

Vous pouvez activer la journalisation de débogage intégrée à partir de l’infrastructure Fluid à l’aide du paramètre suivant dans une console de navigateur.

`localStorage.debug = 'fluid:*'`

Dans les scénarios plus avancés, vous pouvez passer un `logger` à AzureClient. Cela vous permet de personnaliser le comportement de journalisation. Pour plus d’informations sur l’enregistreur d’événements ou la télémétrie, consultez [Journalisation et télémétrie](https://fluidframework.com/docs/testing/telemetry/) sur fluidframework.com. 

## <a name="running-azure-fluid-relay-service-locally"></a>Exécution du service Relais Azure Fluid localement

Pour utiliser le mode local d’AzureClient, vous devez d’abord démarrer un serveur local. L’exécution de `npx @fluidframework/azure-local-service@latest` à partir de la fenêtre de votre terminal lance le serveur local Relais Azure Fluid. Une fois le serveur démarré, vous pouvez exécuter votre application sur le service local.
