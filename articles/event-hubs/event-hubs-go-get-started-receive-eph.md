---
title: Recevoir des événements à l’aide de Go - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Go qui reçoit des événements d’Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: db952b82172928e42e951563d98bb32b275e8af7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084989"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Recevoir des événements d’Event Hubs avec Go

Azure Event Hubs est un système de gestion des événements capable de traiter des millions d’événements par seconde, ce qui permet aux applications de traiter et d’analyser d’énormes quantités de données produites par des appareils connectés et autres applications. Une fois les événements collectés dans le hub d'événements, vous pouvez les recevoir et les gérer à l’aide de gestionnaires intra-processus ou par transfert vers d’autres systèmes d’analyse.

Pour en savoir plus sur Event Hubs, voir [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce didacticiel décrit comment recevoir des événements d’un hub d’événements dans une application Go. Pour savoir comment envoyer des événements, voir l’[article correspondant sur l’envoi](event-hubs-go-get-started-send.md).

Le code utilisé dans ce didacticiel est extrait de ces [exemples GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) que vous pouvez examiner pour voir l’application pleinement fonctionnelle, avec des instructions d’importation et des déclarations de variable.

D’autres exemples sont disponibles dans le [référentiel du package Event Hubs](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez remplir les conditions préalables suivantes :

* Go Installé localement. Suivez [ces instructions](https://golang.org/doc/install) si nécessaire.
* Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.
* Pour recevoir des messages, ceux-ci doit figurer dans le hub d’événements cible. Découvrez comment envoyer des messages dans le [didacticiel sur l’envoi](event-hubs-go-get-started-send.md).
* Un hub d’événements existant (voir la section suivante).
* Un compte de stockage et un conteneur existants (voir la section après la section suivante).

### <a name="create-an-event-hub"></a>Créer un hub d’événements

Ce didacticiel commence avec espace de noms Event Hubs et un hub d’événements existants. Vous pouvez créer ces entités en suivant les instructions de [cet article](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Créer un compte de stockage et un conteneur

Des états tels que des baux sur des partitions et des points de contrôle dans le flux d’événements sont partagés entre les destinataires à l’aide d’un conteneur Stockage Azure. Vous pouvez créer un compte de stockage et un conteneur avec le Kit de développement logiciel (SDK) Go ou en suivant les instructions fournies dans [Comptes de stockage Azure](../storage/common/storage-create-storage-account.md).

Des exemples de création d’artefacts de stockage avec le Kit de développement logiciel (SDK) Go sont disponibles dans le [Référentiel d’exemples GO](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) et dans l’exemple correspondant à ce didacticiel.

## <a name="receive-messages"></a>Recevoir des messages

Pour recevoir les messages, obtenez les packages Go pour Event Hubs avec `go get` ou `dep` :

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>Importer des packages dans votre fichier de code

Pour importer les packages Go, utilisez l’exemple de code suivant :

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

## <a name="create-service-principal"></a>Créer un principal du service

Créez un principal du service en suivant les instructions fournies dans [Créer un principal du service avec Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Enregistrer les informations d’identification fournies dans votre environnement avec les noms suivants : Le SDK Azure pour Go et le package Event Hubs sont tous deux préconfigurés pour rechercher ces noms de variables.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Ensuite, créez un fournisseur d’autorisation pour votre client Event Hubs, qui utilise ces informations d’identification :

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="get-metadata-struct"></a>Obtenir la structure des métadonnées

Obtenez une structure avec des métadonnées sur votre environnement Azure en utilisant le Kit de développement logiciel (SDK) Azure. Les opérations ultérieures utilisent cette structure pour trouver des points de terminaison corrects.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Créer une assistance des informations d'identification 

Créez une assistance des informations d'identification qui utilise les informations d’identification Azure Active Directory (AAD) précédentes pour créer des informations d’identification de Signature d’accès partagé (SAP) pour le stockage. Le dernier paramètre indique à ce constructeur d’utiliser les variables d’environnement utilisées précédemment :

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Créer un pointeur de contrôle et un bailleur 

Créez un **Bailleur**, chargé de louer une partition à un destinataire particulier, et un **Pointeur de contrôle**, chargé d’écrire des points de contrôle pour le flux de messages afin que d’autres destinataires puissent commencer à lire à partir du décalage correct.

Actuellement, un seul **StorageLeaserCheckpointer** est disponible, qui utilise le même conteneur de stockage pour gérer les baux et les points de contrôle. En plus des noms de compte et conteneur de stockage, le **StorageLeaserCheckpointer** a besoin des informations d’identification et de la structure de l’environnement Azure créées à l’étape précédente pour accéder correctement au conteneur.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>Construire un hôte de processeur d’événements

Vous disposez à présent des éléments nécessaires pour construire un EventProcessorHost comme suit. Le même **StorageLeaserCheckpointer** est utilisé à la fois en tant que Bailleur et Pointeur de contrôle, comme décrit précédemment :

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>Créer un gestionnaire 

Créez maintenant un gestionnaire et inscrivez-le auprès de l’hôte du processeur d’événements. Au démarrage de l’hôte, il s’applique cela et tous les autres gestionnaires spécifiés aux messages entrants :

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>Recevoir des messages

Quand tout est configuré, vous pouvez démarrer l’hôte du processeur d’événements avec `Start(context)` pour qu’il s’exécute en permanence, ou avec `StartNonBlocking(context)` pour qu’il s’exécute uniquement aussi longtemps que des messages sont disponibles.

Ce didacticiel démarre et s’exécute comme suit. Voir l’exemple GitHub pour obtenir un exemple d’utilisation de `StartNonBlocking` :

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Notes

Ce didacticiel utilise une seule instance d' **EventProcessorHost**. Pour augmenter le débit et la fiabilité, vous devez exécuter plusieurs instances d’**EventProcessorHost** sur différents systèmes. Le système Bailleur veille à ce qu’un seul récepteur soit associé à une partition spécifiée à un moment donné et en reçoive des messages.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé une application Go qui a reçu des messages d’un hub d’événements. Pour découvrir comment envoyer des événements à un hub d’événements à l’aide de Go, consultez [Envoyer des événements à un hub d’événements - Go](event-hubs-go-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
