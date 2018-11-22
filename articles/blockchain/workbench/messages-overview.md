---
title: Présentation de l’intégration des messages Azure Blockchain Workbench
description: Présentation de l’utilisation des messages dans Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614359"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Intégration des messages Azure Blockchain Workbench

En plus de fournir une API REST, Azure Blockchain Workbench propose une intégration basée sur la messagerie. Workbench publie des événements orienté registre via Azure Event Grid, ce qui permet aux consommateurs en aval d’ingérer des données ou d’entreprendre des actions basées sur ces événements. Pour les clients qui ont besoin d’une messagerie fiable, Azure Blockchain Workbench transmet également les messages à un point de terminaison Azure Service Bus.

## <a name="input-apis"></a>API d’entrée

Si vous souhaitez initier des transactions à partir de systèmes externes pour créer des utilisateurs, créer des contrats et mettre à jour des contrats, vous pouvez utiliser les API d’entrée de messagerie pour effectuer des transactions sur un registre. Consultez les [exemples d’intégration de messagerie](https://aka.ms/blockchain-workbench-integration-sample) pour obtenir un exemple qui illustre les API d’entrée.

Les API d’entrée actuellement disponibles sont les suivantes.

### <a name="create-user"></a>Créer un utilisateur

Crée un nouvel utilisateur.

La requête requiert les champs suivants :

| **Nom**             | **Description**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID fourni par le client                                |
| firstName            | Prénom de l’utilisateur                              |
| lastName             | Nom de l’utilisateur                               |
| emailAddress         | Adresse e-mail de l’utilisateur                           |
| externalId           | ID d’objet Azure AD de l’utilisateur                      |
| connectionId         | Identificateur unique de la connexion blockchain |
| messageSchemaVersion | Version du schéma de messagerie                            |
| messageName          | **CreateUserRequest**                               |

Exemple :

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench retourne une réponse avec les champs suivants :

| **Nom**              | **Description**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID fourni par le client |
| userId                | ID de l’utilisateur qui a été créé. |
| userChainIdentifier   | Adresse de l’utilisateur qui a été créé sur le réseau blockchain. Dans Ethereum, il s’agit de l’adresse **on-chain** de l’utilisateur. |
| connectionId          | Identificateur unique de la connexion blockchain|
| messageSchemaVersion  | Version du schéma de messagerie |
| messageName           | **CreateUserUpdate** |
| status                | État de la demande de création d’utilisateur.  En cas de réussite, la valeur est **Succès**. En cas d’échec, la valeur est **Échec**.     |
| additionalInformation | Informations supplémentaires fournies en fonction de l’état |

Exemple de réponse **create user** réussie de Blockchain Workbench :

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Si la demande a échoué, les détails sur l’échec sont inclus dans les informations supplémentaires.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Créer un contrat

Crée un nouveau contrat.

La requête requiert les champs suivants :

| **Nom**             | **Description**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID fourni par le client |
| userChainIdentifier  | Adresse de l’utilisateur qui a été créé sur le réseau blockchain. Dans Ethereum, il s’agit de l’adresse **on-chain** de l’utilisateur. |
| applicationName      | Nom de l’application |
| workflowName         | Nom du flux de travail |
| parameters           | Entrée de paramètres pour la création de contrat |
| connectionId         | Identificateur unique de la connexion blockchain |
| messageSchemaVersion | Version du schéma de messagerie |
| messageName          | **CreateContractRequest** |

Exemple :

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench retourne une réponse avec les champs suivants :

| **Nom**                 | **Description**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID fourni par le client                                                             |
| contractId               | Identificateur unique du contrat dans Azure Blockchain Workbench |
| contractLedgerIdentifier | Adresse du contrat sur le registre                                            |
| connectionId             | Identificateur unique de la connexion blockchain                               |
| messageSchemaVersion     | Version du schéma de messagerie                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | État de la demande de création de contrat.  Valeurs possibles : **Envoyé**, **Validé**, **Échec**.  |
| additionalInformation    | Informations supplémentaires fournies en fonction de l’état                              |

Exemple d’une réponse **create contract** envoyée à partir de Blockchain Workbench :

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Exemple d’une réponse **create contract** validée à partir de Blockchain Workbench :

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Si la demande a échoué, les détails sur l’échec sont inclus dans les informations supplémentaires.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Créer une action de contrat

Crée une nouvelle action de contrat.

La requête requiert les champs suivants :

| **Nom**                 | **Description**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID fourni par le client |
| userChainIdentifier      | Adresse de l’utilisateur qui a été créé sur le réseau blockchain. Dans Ethereum, il s’agit de l’adresse **on-chain** de l’utilisateur. |
| contractLedgerIdentifier | Adresse du contrat sur le registre |
| workflowFunctionName     | Nom de la fonction du flux de travail |
| parameters               | Entrée de paramètres pour la création de contrat |
| connectionId             | Identificateur unique de la connexion blockchain |
| messageSchemaVersion     | Version du schéma de messagerie |
| messageName              | **CreateContractActionRequest** |

Exemple :

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench retourne une réponse avec les champs suivants :

| **Nom**              | **Description**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID fourni par le client|
| contractId            | Identificateur unique du contrat dans Azure Blockchain Workbench |
| connectionId          | Identificateur unique de la connexion blockchain |
| messageSchemaVersion  | Version du schéma de messagerie |
| messageName           | **CreateContractActionUpdate** |
| status                | État de la demande d’action de contrat. Valeurs possibles : **Envoyé**, **Validé**, **Échec**.                         |
| additionalInformation | Informations supplémentaires fournies en fonction de l’état |

Exemple d’une réponse **create contract action** envoyée à partir de Blockchain Workbench :

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exemple d’une réponse **create contract action** validée à partir de Blockchain Workbench :

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Si la demande a échoué, les détails sur l’échec sont inclus dans les informations supplémentaires.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Messages et codes d’erreur d’API d’entrée

**Code d’erreur 4000 : Erreur de demande incorrecte**
- connectionId non valide
- Échec de la désérialisation de CreateUserRequest
- Échec de la désérialisation de CreateContractRequest
- Échec de la désérialisation de CreateContractActionRequest
- L’application {identifiée par le nom de l’application} n’existe pas
- L’application {identifiée par le nom de l’application} n’a pas de flux de travail
- UserChainIdentifier n’existe pas
- Le contrat {identifié par l’identificateur du registre} n’existe pas
- Le contrat {identifié par l’identificateur du registre} n’a pas de fonction {nom de fonction de flux de travail}
- UserChainIdentifier n’existe pas

**Code d’erreur 4090 : Erreur de conflit**
- L’utilisateur existe déjà
- Le contrat existe déjà
- L’action de contrat existe déjà

**Code d’erreur 5000 : Erreur interne du serveur**
- Messages d’exception

## <a name="event-notifications"></a>Notifications d’événement

Vous pouvez utiliser des notifications d’événements pour informer les utilisateurs et les systèmes en aval des événements qui se produisent dans Blockchain Workbench et sur le réseau blockchain auquel il est connecté. Les notifications d’événements peuvent être utilisées directement dans le code ou avec des outils comme Logic Apps et Flow pour déclencher des flux de données vers des systèmes en aval.

Consultez la section [Référence sur les messages de notification](#notification-message-reference) pour plus d’informations sur les différents messages qui peuvent être reçus.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consommation d’événements Event Grid avec Azure Functions

Si un utilisateur souhaite utiliser Event Grid pour être informé des événements qui se produisent dans Blockchain Workbench, vous pouvez utiliser des événements d’Event Grid à l’aide d’Azure Functions.

1. Créez une **application Azure Function** dans le portail Azure
2. Créez une fonction.
3. Recherchez le modèle pour Event Grid. Le code du modèle de base pour lire le message est affiché. Modifiez le code en fonction de vos besoins.
4. Enregistrez la fonction. 
5. Sélectionnez Event Grid dans le groupe de ressources de Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consommation d’événements Event Grid avec Logic Apps

1.  Créez une **application logique Azure** dans le portail Azure.
2.  Lorsque vous ouvrez l’application logique Azure dans le portail, vous êtes invité à sélectionner un déclencheur. Sélectionnez **Azure Event Grid--When a resource event occurs** (Azure Event Grid--Quand un événement de ressource se produit).
3. Lorsque le Concepteur de flux de travail s’affiche, vous êtes invité à vous connecter.
4. Sélectionnez l’abonnement. Ressource comme **Microsoft.EventGrid.Topics**. Sélectionnez le **nom de la ressource** à partir du nom de la ressource du groupe de ressources Azure Blockchain Workbench.
5. Sélectionnez l’événement Event Grid dans le groupe de ressources de Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Utilisation des rubriques Service Bus pour les notifications

Des rubriques Service Bus peuvent être utilisées pour informer les utilisateurs des événements qui se produisent dans Blockchain Workbench. 

1.  Accédez à Service Bus dans le groupe de ressources de Workbench.
2.  Sélectionnez **Rubriques**.
3.  Sélectionnez **workbench-external**.
4.  Créez un abonnement à cette rubrique. Récupérez une clé pour celle-ci.
5.  Créez un programme qui s’abonne aux événements de cet abonnement.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Consommation des messages Service Bus avec Logic Apps

1. Créez une **application logique Azure** dans le portail Azure.
2. Lorsque vous ouvrez l’application logique Azure dans le portail, vous êtes invité à sélectionner un déclencheur. Tapez **Service Bus** dans la zone de recherche et sélectionnez le déclencheur approprié pour le type d’interaction que vous souhaitez avoir avec Service Bus. Par exemple, **Service Bus -- Quand une rubrique abonnement reçoit un message (saisie semi-automatique)**.
3. Lorsque le Concepteur de flux de travail s’affiche, spécifiez les informations de connexion à Service Bus.
4. Sélectionnez votre abonnement et spécifiez la rubrique de **workbench-external**.
5. Développez la logique de votre application qui utilise le message à partir de ce déclencheur.

## <a name="notification-message-reference"></a>Référence sur les messages de notification

En fonction de **OperationName**, les messages de notification ont l’un des types de messages suivants.

### <a name="accountcreated"></a>AccountCreated

Indique qu’un nouveau compte a reçu une demande d’ajout à la chaîne spécifiée.

| NOM    | Description  |
|----------|--------------|
| UserId  | ID de l’utilisateur qui a été créé. |
| ChainIdentifier | Adresse de l’utilisateur qui a été créé sur le réseau blockchain. Dans Ethereum, il s’agit de l’adresse **on-chain** de l’utilisateur. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Indique qu’une demande a été faite pour insérer ou mettre à jour un contrat sur un registre distribué.

| NOM | Description |
|-----|--------------|
| ChainID | Identificateur unique de la chaîne associée à la demande |
| BlockId | Identificateur unique pour un bloc sur le registre |
| ContractId | Identificateur unique du contrat |
| ContractAddress |       Adresse du contrat sur le registre |
| TransactionHash  |     Code de hachage de la transaction sur le registre |
| OriginatingAddress |   Adresse du créateur de la transaction |
| ActionName       |     Nom de l’action |
| IsUpdate        |      Identifie s’il s’agit d’une mise à jour |
| parameters       |     Liste d’objets qui identifient le nom, la valeur et le type de données des paramètres transmis à une action |
| TopLevelInputParams |  Dans les scénarios où un contrat est lié à un ou plusieurs contrats, il s’agit des paramètres du contrat de niveau supérieur. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Indique qu’une demande a été faite pour exécuter une action sur un contrat spécifique dans un registre distribué.

| NOM                     | Description                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Identificateur unique de cette action de contrat |
| ChainIdentifier          | Identificateur unique de la chaîne |
| ConnectionId             | Identificateur unique de la connexion |
| UserChainIdentifier      | Adresse de l’utilisateur qui a été créé sur le réseau blockchain. Dans Ethereum, il s’agit de l’adresse **on-chain** de l’utilisateur. |
| ContractLedgerIdentifier | Adresse du contrat sur le registre |
| WorkflowFunctionName     | Nom de la fonction du flux de travail |
| WorkflowName             | Nom du flux de travail |
| WorkflowBlobStorageURL   | URL du contrat dans le stockage Blob |
| ContractActionParameters | Paramètres de l’action du contrat |
| TransactionHash          | Code de hachage de la transaction sur le registre |
| État de l’approvisionnement      | État de l’approvisionnement de l’action.</br>0 - Créée</br>1 - En cours de traitement</br>2 - Terminée</br> « Terminée » indique une confirmation de l’ajout par le registre |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Indique qu’une demande a été faite pour mettre à jour le solde de l’utilisateur sur un registre distribué spécifique.

> [!NOTE]
> Ce message est généré uniquement pour les registres qui requièrent le budget des comptes.
> 

| NOM    | Description                              |
|---------|------------------------------------------|
| Adresse | Adresse de l’utilisateur qui a été financé |
| Balance | Solde de l’utilisateur         |
| ChainID | Identificateur unique de la chaîne     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Message qui indique qu’une demande a été faite pour ajouter un bloc sur un registre distribué.

| NOM           | Description                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Identificateur unique de la chaîne à laquelle le bloc a été ajouté             |
| BlockId        | Identificateur unique du bloc dans Azure Blockchain Workbench |
| BlockHash      | Code de hachage du bloc                                                 |
| BlockTimeStamp | Timestamp du bloc                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Message qui fournit des détails sur une demande d’ajout d’une transaction sur un registre distribué.

| NOM            | Description                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Identificateur unique de la chaîne à laquelle le bloc a été ajouté             |
| BlockId         | Identificateur unique du bloc dans Azure Blockchain Workbench |
| TransactionHash | Code de hachage de la transaction                                           |
| À partir            | Adresse du créateur de la transaction                      |
| À              | Adresse du destinataire de la transaction              |
| Valeur           | Valeur incluse dans la transaction                                 |
| IsAppBuilderTx  | Identifie s’il s’agit d’une transaction Blockchain Workbench                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Fournit des détails sur l’affectation d’un identificateur de chaîne pour un contrat. Par exemple, dans un blockchain Ethereum, l’adresse d’un contrat sur le registre.

| NOM            | Description                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Identificateur unique du contrat dans Azure Blockchain Workbench |
| ChainIdentifier | Identificateur du contrat sur la chaîne                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Classes utilisées par les types de messages

### <a name="messagemodelbase"></a>MessageModelBase

Modèle de base pour tous les messages.

| NOM          | Description                          |
|---------------|--------------------------------------|
| OperationName | Nom de l’opération           |
| RequestId     | Identificateur unique pour la demande |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Contient le nom, la valeur et le type d’un paramètre.

| NOM  | Description                 |
|-------|-----------------------------|
| NOM  | Nom du paramètre  |
| Valeur | Valeur du paramètre |
| type  | Type du paramètre  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Contient l’ID, le nom, la valeur et le type d’une propriété.

| NOM  | Description                |
|-------|----------------------------|
| ID    | ID de la propriété    |
| NOM  | Nom de la propriété  |
| Valeur | Valeur de la propriété |
| type  | Type de la propriété  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modèles d’intégration de contrat intelligent](integration-patterns.md)