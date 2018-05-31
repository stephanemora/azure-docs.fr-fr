---
title: Présentation des messages Azure Blockchain Workbench
description: Présentation de l’utilisation des messages dans Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 4a2e85cc619d17745be9d8f72af5f99049ce7c6b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302090"
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Présentation des messages Azure Blockchain Workbench

En plus de fournir une API REST, Azure Blockchain Workbench propose une intégration basée sur la messagerie. Workbench publie des événements orienté registre via Azure Event Grid, ce qui permet aux consommateurs en aval d’ingérer des données ou d’entreprendre des actions basées sur ces événements. Pour les clients qui ont besoin d’une messagerie fiable, Azure Blockchain Workbench transmet également les messages à un point de terminaison Azure Service Bus.

Les développeurs ont également indiqué qu’ils souhaitaient avoir la possibilité de disposer de systèmes externes pour communiquer et initier des transactions pour créer des utilisateurs, des contrats et mettre à jour des contrats sur un registre. Bien que cette fonctionnalité ne soit actuellement pas disponible en préversion publique, un exemple proposant cette option se trouve à l’emplacement [http://aka.ms/blockchain-workbench-integration-sample](http://aka.ms/blockchain-workbench-integration-sample).


## <a name="event-notifications"></a>Notifications d’événement

Des notifications d’événements peuvent être utilisées pour informer les utilisateurs et les systèmes en aval des événements qui se produisent dans Workbench et sur le réseau blockchain auquel il est connecté. Les notifications d’événements peuvent être utilisées directement dans le code ou avec des outils comme Logic Apps et Flow pour déclencher des flux de données vers des systèmes en aval.

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
2.  Lorsque vous ouvrez l’application logique Azure dans le portail, vous êtes invité à sélectionner un déclencheur. Tapez **Service Bus** dans la zone de recherche et sélectionnez le déclencheur approprié pour le type d’interaction que vous souhaitez avoir avec Service Bus. Par exemple, **Service Bus -- Quand une rubrique abonnement reçoit un message (saisie semi-automatique)**.
3. Lorsque le Concepteur de flux de travail s’affiche, spécifiez les informations de connexion à Service Bus.
4. Sélectionnez votre abonnement et spécifiez la rubrique de **workbench-external**.
5. Développez la logique de votre application qui utilise le message à partir de ce déclencheur.

## <a name="notification-message-reference"></a>Référence sur les messages de notification

En fonction de OperationName, les messages de notification ont l’un des types de message suivants.

### <a name="accountcreated"></a>AccountCreated

Indique qu’un nouveau compte a reçu une demande d’ajout à la chaîne spécifiée.

| NOM    | Description  |
|----------|--------------|
| UserId  | ID de l’utilisateur qui a été créé. |
| ChainIdentifier | Adresse de l’utilisateur qui a été créé sur le réseau blockchain. Dans Ethereum, il s’agit de l’adresse « on-chain » de l’utilisateur. |

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
| ChainID | Identificateur unique de la chaîne associée à la demande.|
  BlockId | Identificateur unique pour un bloc sur le registre.|
  ContractId | Identificateur unique du contrat.|
  ContractAddress |       Adresse du contrat sur le registre.|
  TransactionHash  |     Code de hachage de la transaction sur le registre.|
  OriginatingAddress |   Adresse du créateur de la transaction.|
  ActionName       |     Nom de l’action.|
  IsUpdate        |      Identifie s’il s’agit d’une mise à jour.|
  parameters       |     Liste d’objets qui identifient le nom, la valeur et le type de données des paramètres transmis à une action.|
  TopLevelInputParams |  Dans les scénarios où un contrat est lié à un ou plusieurs contrats, il s’agit des paramètres du contrat de niveau supérieur. |

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
| ContractActionId         | Identificateur unique de cette action de contrat.                                                                                                                                |
| ChainIdentifier          | Identificateur unique de la chaîne.                                                                                                                                           |
| ConnectionId             | Identificateur unique de la connexion.                                                                                                                                      |
| UserChainIdentifier      | Adresse de l’utilisateur qui a été créé sur le réseau blockchain. Dans Ethereum, il s’agit de l’adresse « on-chain » de l’utilisateur.                                                     |
| ContractLedgerIdentifier | Adresse du contrat sur le registre.                                                                                                                                        |
| WorkflowFunctionName     | Nom de la fonction du flux de travail.                                                                                                                                                |
| WorkflowName             | Nom du flux de travail.                                                                                                                                                         |
| WorkflowBlobStorageURL   | URL du contrat dans le stockage d’objets blob.                                                                                                                                      |
| ContractActionParameters | Paramètres de l’action du contrat.                                                                                                                                           |
| TransactionHash          | Code de hachage de la transaction sur le registre.                                                                                                                                    |
| État de l’approvisionnement      | État de l’approvisionnement de l’action.</br>0 - Créée</br>1 - En cours de traitement</br>2 - Terminée</br> « Terminée » indique une confirmation de l’ajout par le registre.                                               |
|                          |                                                                                                                                                                               |

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
| Adresse | Adresse de l’utilisateur qui a été financé. |
| Balance | Solde de l’utilisateur.         |
| ChainID | Identificateur unique de la chaîne.     |


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
| ChainId        | Identificateur unique de la chaîne à laquelle le bloc a été ajouté.             |
| BlockId        | Identificateur unique du bloc dans Azure Blockchain Workbench. |
| BlockHash      | Code de hachage du bloc.                                                 |
| BlockTimeStamp | Timestamp du bloc.                                            |

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
| ChainId         | Identificateur unique de la chaîne à laquelle le bloc a été ajouté.             |
| BlockId         | Identificateur unique du bloc dans Azure Blockchain Workbench. |
| TransactionHash | Code de hachage de la transaction.                                           |
| À partir            | Adresse du créateur de la transaction.                      |
| À              | Adresse du destinataire de la transaction.              |
| Valeur           | Valeur incluse dans la transaction.                                 |
| IsAppBuilderTx  | Identifie s’il s’agit d’une transaction Blockchain Workbench.                         |

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
| ContractId      | Il s’agit de l’identificateur unique du contrat dans Azure Blockchain Workbench. |
| ChainIdentifier | Il s’agit de l’identificateur du contrat sur la chaîne.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modèles d’intégration de contrat intelligent](blockchain-workbench-integration-patterns.md)