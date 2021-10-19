---
title: 'Guide pratique : Utiliser l’automatisation des tests avec Relais Azure Fluid'
description: Comment utiliser les bibliothèques d’automatisation des tests pour créer des tests automatisés pour les applications Fluid
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/testing/testing/
ms.openlocfilehash: 0087be9dccc1b040720ea53f5e6aae6d64f6fa17
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661895"
---
# <a name="how-to-use-test-automation-with-azure-fluid-relay"></a>Guide pratique : Utiliser l’automatisation des tests avec Relais Azure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Les tests et l’automatisation sont essentiels pour maintenir la qualité et la longévité de votre code. En interne, Fluid utilise une plage de tests unitaires et d’intégration alimentés par [Mocha](https://mochajs.org/), [Jest](https://jestjs.io/), [Puppeteer](https://github.com/puppeteer/puppeteer) et [Webpack](https://webpack.js.org/).

Vous pouvez exécuter des tests à l’aide de l’outil **@fluidframework/azure-local-service** local ou à l’aide d’un locataire de test dans le service Relais Azure Fluid. **AzureClient** peut être configuré pour se connecter à un service distant et à un service local, ce qui vous permet d’utiliser un seul type de client entre les tests sur des instances de service actives et locales. La seule différence entre les deux est la configuration utilisée pour créer le client.

## <a name="automation-against-azure-fluid-relay"></a>Automatisation sur Relais Azure Fluid

Votre automatisation peut se connecter à un locataire de test pour Relais Azure Fluid de la même façon qu’un locataire de production et n’a besoin que de la configuration de connexion appropriée. Consultez [Guide pratique : Se connecter à un service Relais Azure Fluid](connect-fluid-azure-service.md) pour plus de détails.

## <a name="creating-an-adaptable-test-client"></a>Création d’un client de test adaptable

Pour créer un client de test adaptable, vous devez configurer AzureClient différemment en fonction de la cible du service. La fonction ci-dessous utilise une variable d’environnement pour déterminer cela. Vous pouvez définir la variable d’environnement dans un script de test pour contrôler quel est le service ciblé.

```typescript
function createAzureClient(): AzureClient {
    const useAzure = process.env.FLUID_CLIENT === "azure";
    const tenantKey = useAzure ? process.env.FLUID_TENANTKEY as string : "";
    const user = { id: "userId", name: "Test User" };

    const connectionConfig = useAzure ? {
        tenantId: "myTenantId",
        tokenProvider: new InsecureTokenProvider(tenantKey, user),
        orderer: "https://myOrdererUrl",
        storage: "https://myStorageUrl",
    } : {
        tenantId: LOCAL_MODE_TENANT_ID,
        tokenProvider: new InsecureTokenProvider("", user),
        orderer: "http://localhost:7070",
        storage: "http://localhost:7070",
    };

    const clientProps = {
        connection: config,
    };

    return new AzureClient(clientProps);
}
```

Vos tests peuvent appeler cette fonction pour créer un objet AzureClient sans se soucier du service sous-jacent. Le test [Mocha](https://mochajs.org/) ci-dessous crée le client de service avant d’exécuter des tests, puis l’utilise pour exécuter chaque test. Il existe un seul test qui utilise le client de service pour créer un conteneur qui réussit tant qu’aucune erreur n’est générée.

```typescript
describe("ClientTest", () => {
    const client = createAzureClient();
    let documentId: string;

    it("can create Azure container successfully", async () => {
        const schema: ContainerSchema = {
            initialObjects: {
                customMap: SharedMap
            },
        };
        documentId = await container.attach();
        const { container, services } = await azureClient.createContainer(schema);
    });
});

```

## <a name="running-tests"></a>Exécution des tests

Vous pouvez ajouter les scripts npm suivants dans le fichier package.json de votre projet pour exécuter les tests :

```json
"scripts": {
    "start:local": "npx @fluidframework/azure-local-service@latest > local-service.log 2>&1",
    "test:mocha": "mocha",
    "test:azure": "cross-env process.env.FLUID_CLIENT='\"azure\"' && npm run test:mocha",
    "test:local": "start-server-and-test start:local 7070 test:mocha"
}
```

Pour installer les dépendances requises par les scripts ci-dessus, vous pouvez utiliser la commande suivante :

```bash
npm install cross-env start-server-and-test mocha
```

Le script `test:local` utilise la bibliothèque [start-server-and-test](https://www.npmjs.com/package/start-server-and-test) pour démarrer le serveur local, patienter jusqu’à ce que le port 7070 (le port par défaut utilisé par le serveur local) réponde, exécuter le script de test, puis arrêter le serveur local.
