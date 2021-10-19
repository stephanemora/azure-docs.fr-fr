---
title: 'Guide pratique : Écrire un TokenProvider avec une fonction Azure'
description: Comment écrire un fournisseur de jetons personnalisé en tant que fonction Azure et le déployer.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: d6987b4e4592167fcb41a7f6654ff46140a79724
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661861"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>Guide pratique : Écrire un TokenProvider avec une fonction Azure

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Dans l’[infrastructure Fluid](https://fluidframework.com/), les fournisseurs de jetons sont chargés de créer et signer des jetons que `@fluidframework/azure-client` utilise pour adresser des demandes au service Relais Azure Fluid. L’infrastructure Fluid inclut un fournisseur de jetons simple non sécurisé à des fins de développement, judicieusement nommé **InsecureTokenProvider**. Chaque service Fluid doit implémenter un fournisseur de jetons personnalisé en fonction des considérations relatives à l’authentification et à la sécurité du service particulier.

## <a name="implementing-your-own-tokenprovider-class"></a>Implémentation de votre propre classe TokenProvider

Chaque locataire du service Relais Azure Fluid que vous créez se voit attribuer un **ID de locataire** et sa propre **clé secrète de locataire** unique. La clé secrète est un **secret partagé**. Votre application/service la connaît, et le service Relais Azure Fluid la connaît également. 

Les fournisseurs de jetons doivent connaître la clé secrète pour signer des demandes, mais cette clé ne peut pas être incluse dans le code client. Les fournisseurs de jetons contactent le serveur Fluid au moment de l’exécution afin d’obtenir la clé secrète en toute sécurité sans l’exposer au client. Cela s’effectue par le biais de deux appels d’API distincts : `fetchOrdererToken` et `fetchStorageToken`. Ceux-ci sont chargés de récupérer respectivement les URL de l’ordonnanceur et du stockage à partir de l’hôte. Les deux fonctions retournent des `TokenResponse` objets représentant la valeur du jeton.

## <a name="tokenprovider-class-example"></a>Exemple de classe TokenProvider

Pour créer un fournisseur de jetons sécurisés, une option consiste à créer une fonction Azure serverless et à l’exposer en tant que fournisseur de jetons. Cela vous permet de stocker la *clé secrète du locataire* sur un serveur sécurisé. Votre application appelle ensuite la fonction Azure pour générer des jetons.

Cet exemple implémente ce modèle dans une classe nommée **AzureFunctionTokenProvider**. Il accepte l’URL de votre fonction Azure, `userId` et `userName`. Cette implémentation spécifique vous est également fournie en tant qu’exportation à partir du package `@fluidframework/azure-client`.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/azure-client";

export class AzureFunctionTokenProvider implements ITokenProvider {
  constructor(
    private readonly azFunctionUrl: string,
    private readonly userId: string,
    private readonly userName: string,
  );

  public async fetchOrdererToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }
}
```

Pour garantir la sécurité de la clé secrète du locataire, celle-ci est stockée dans un emplacement principal sécurisé et n’est accessible qu’à partir de la fonction Azure. L’une des méthodes permettant de récupérer un jeton signé consiste à adresser une demande `GET` à votre fonction Azure, en fournissant les valeurs `tenantID`, `documentId` et `userID`/`userName`. La fonction Azure assure le mappage entre l’ID de locataire et la clé secrète du locataire pour générer et signer le jeton de manière appropriée pour que le service Relais Azure Fluid l’accepte.

```typescript
private async getToken(tenantId: string, documentId: string): Promise<string> {
    const params = {
        tenantId,
        documentId,
        userId: this.userId,
        userName: this.userName,
    };
    const token = this.getTokenFromServer(params);
    return token;
}
```

L’exemple ci-dessous utilise la bibliothèque [`axios`](https://www.npmjs.com/package/axios) pour effectuer des requêtes HTTP. Vous pouvez utiliser d’autres bibliothèques ou approches pour effectuer une requête HTTP.

```typescript
private async getTokenFromServer(input: any): Promise<string> {
    return axios.get(this.azFunctionUrl, {
        params: input,
    }).then((response) => {
        return response.data as string;
    }).catch((err) => {
        return err as string;
    });
}
```

Cet exemple montre comment créer votre propre **fonction Azure HTTPTrigger** qui récupère le jeton en passant votre clé de locataire.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

//Replace "myTenantKey" with your key here.
const key = "myTenantKey";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // tenantId, documentId, userId and userName are required parameters
    const tenantId = (req.query.tenantId || (req.body && req.body.tenantId)) as string;
    const documentId = (req.query.documentId || (req.body && req.body.documentId)) as string;
    const userId = (req.query.userId || (req.body && req.body.userId)) as string;
    const userName = (req.query.userName || (req.body && req.body.userName)) as string;
    const scopes = (req.query.scopes || (req.body && req.body.scopes)) as ScopeType[];

    if (!tenantId) {
        context.res = {
            status: 400,
            body: "No tenantId provided in query params",
        };
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
    }

    let user = { name: userName, id: userId };

    // Will generate the token and returned by an ITokenProvider implementation to use with the AzureClient.
    const token = generateToken(
        tenantId,
        documentId,
        key,
        scopes ?? [ScopeType.DocRead, ScopeType.DocWrite, ScopeType.SummaryWrite],
        user
    );

    context.res = {
        status: 200,
        body: token
    };
};

export default httpTrigger;
```

La fonction `generateToken` génère un jeton pour l’utilisateur donné qui s’est connecté à l’aide de la clé secrète du locataire. Cela permet de retourner le jeton au client sans jamais lui exposer le secret proprement dit. Au lieu de cela, le jeton est généré en utilisant le secret pour fournir un accès délimité au document donné. Ce jeton peut être retourné par une implémentation de `ITokenProvider` à utiliser avec l’`AzureClient`.

## <a name="see-also"></a>Voir aussi

- [Ajouter des données personnalisées à un jeton d’authentification](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
