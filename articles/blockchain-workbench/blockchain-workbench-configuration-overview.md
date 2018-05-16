---
title: Référence de configuration Azure Blockchain Workbench
description: Vue d’ensemble de la configuration d’application Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 5569a7608a61b4e79a03264e0ccf62682782264b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Référence de configuration Azure Blockchain Workbench

 Les applications Azure Blockchain Workbench désignent des workflows multiniveaux définis par des métadonnées de configuration et un code de contrat intelligent. Les métadonnées de configuration définissent les workflows généraux et le modèle d’interaction de l’application blockchain. Les contrats intelligents définissent la logique métier de l’application blockchain. Workbench utilise la configuration et le code de contrat intelligent pour générer des expériences utilisateur d’application blockchain.

Les métadonnées de configuration spécifient les informations suivantes pour chaque application blockchain : 

* Nom et description de l’application blockchain
* Rôles uniques pour les utilisateurs qui peuvent agir ou participer au sein de l’application blockchain
* Un ou plusieurs workflows. Chaque workflow agit comme une machine à états pour contrôler le flux de la logique métier. Les workflows peuvent être indépendants ou interagir entre eux.

Chaque workflow défini spécifie les éléments suivants :

* Nom et description du workflow
* États du workflow.  Chaque état est une étape dans le flux de contrôle de la logique métier. 
* Actions de transition à l’état suivant
* Rôles d’utilisateur autorisés à initier chaque action
* Contrats intelligents qui représentent la logique métier dans les fichiers de code

## <a name="application"></a>Application

Une application blockchain contient des métadonnées de configuration, des workflows et des rôles utilisateur qui peuvent agir ou participer au sein de l’application.

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| ApplicationName | Nom d’application unique. Le contrat intelligent correspondant doit utiliser la même propriété **ApplicationName** pour la classe de contrat applicable.  | OUI |
| DisplayName | Nom d’affichage convivial de l’application. | OUI |
| Description | Description de l’application. | Non  |
| ApplicationRoles | Collection d’[ApplicationRoles](#application-roles). Rôles utilisateur autorisés à agir ou à participer au sein de l’application.  | OUI |
| Flux de travail | Collection de [Workflows](#workflows). Chaque workflow agit comme une machine à états pour contrôler le flux de la logique métier. | OUI |

Pour obtenir un exemple, consultez l’[exemple de fichier de configuration](#configuration-file-example).

## <a name="workflows"></a>Flux de travail

La logique métier d’une application peut être modélisée comme une machine à états dans laquelle la réalisation d’une action entraîne le transfert du flux de logique métier d’un état à un autre. Un workflow est une collection de ces états et actions. Chaque workflow se compose d’un ou plusieurs contrats intelligents, qui représentent la logique métier dans les fichiers de code. Un contrat exécutable est une instance de workflow.

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| NOM | Nom unique du workflow. Le contrat intelligent correspondant doit utiliser la même propriété **Name** pour la classe de contrat applicable. | OUI |
| DisplayName | Nom d’affichage convivial du workflow. | OUI |
| Description | Description du workflow. | Non  |
| Initiateurs | Collection d’[ApplicationRoles](#application-roles). Rôles attribués aux utilisateurs qui sont autorisés à créer des contrats dans le workflow. | OUI |
| StartState | Nom de l’état initial du workflow. | OUI |
| properties | Collection d’[identificateurs](#identifiers). Représente des données qui peuvent être lues hors de la chaîne ou visualisées dans un outil d’expérience utilisateur. | OUI |
| Constructeur | Définit les paramètres d’entrée pour la création d’une instance du workflow. | OUI |
| Functions | Collection de [fonctions](#functions) qui peut être exécutée dans le workflow. | OUI |
| États | Collection d’[états](#states) de workflow. | OUI |

Pour obtenir un exemple, consultez l’[exemple de fichier de configuration](#configuration-file-example).

## <a name="type"></a>type

Types de données pris en charge.

| type | Description |
|-------|-------------|
| address  | Type d’adresse blockchain, par exemple *contracts* ou *users* |
| bool     | Type de données booléennes |
| contract | Adresse du contrat type |
| int      | Type de données Integer |
| money    | Type de données Money |
| state    | État du workflow |
| chaîne   | Type de données String |
| user     | Adresse de l’utilisateur type |
| time     | Type de données Time |
|`[ Application Role Name ]`| N’importe quel nom spécifié dans le rôle d’application. Limite les utilisateurs à ce type de rôle. |

## <a name="constructor"></a>Constructeur

Définit les paramètres d’entrée pour une instance de workflow.

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| parameters | Collection d’[identificateurs](#identifiers) requise pour initialiser un contrat intelligent. | OUI |

### <a name="constructor-example"></a>Exemple de constructeur

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Functions

Définit les fonctions qui peuvent être exécutées dans le workflow.

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| NOM | Nom unique de la fonction. Le contrat intelligent correspondant doit utiliser la même propriété **Name** pour la fonction applicable. | OUI |
| DisplayName | Nom d’affichage convivial de la fonction. | OUI |
| Description | Description de la fonction | Non  |
| parameters | Collection d’[identificateurs](#identifiers) correspondant aux paramètres de la fonction. | OUI |

### <a name="functions-example"></a>Exemples de fonctions

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>États

Collection d’états uniques au sein d’un workflow. Chaque état capture une étape dans le workflow de la logique métier. 

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| NOM | Nom unique de l’état. Le contrat intelligent correspondant doit utiliser la même propriété **Name** pour l’état applicable. | OUI |
| DisplayName | Nom d’affichage convivial de l’état. | OUI |
| Description | Description de l’état. | Non  |
| PercentComplete | Valeur entière qui apparaît dans l’interface utilisateur Blockchain Workbench pour afficher la progression dans le flux de contrôle de logique métier. | OUI |
| Style | Indicateur visuel qui indique si l’état représente un état de réussite ou d’échec. Il existe deux valeurs valides : `Success` ou `Failure`. | OUI |
| Transitions | Collection de [transitions](#transitions) disponibles de l’état actuel vers l’ensemble d’états suivant. | Non  |

### <a name="states-example"></a>Exemple d’états

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transitions

Actions disponibles à l’état suivant. Un ou plusieurs rôles d’utilisateur peuvent exécuter une action à chaque état, où une action peut passer d’un état à un autre dans le workflow. 

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| AllowedRoles | Liste des rôles d’application autorisés à initier la transition. Tous les utilisateurs du rôle spécifié peuvent effectuer l’action. | Non  |
| AllowedInstanceRoles | Liste des rôles d’utilisateur participant ou spécifiés dans le contrat intelligent, autorisés à initier la transition. Les rôles d’instance sont définis dans **Propriétés** au sein de Workflows. Ces utilisateurs représentent un utilisateur participant ou spécifié dans le contrat intelligent par opposition à tous les utilisateurs d’un type de rôle. | Non  |
| DisplayName | Nom d’affichage convivial de la transition. | OUI |
| Description | Description de la transition. | Non  |
| Fonction | Nom de la fonction permettant d’initier la transition. | OUI |
| NextStates | Collection d’états potentiels après une transition réussie. | OUI |

### <a name="transitions-example"></a>Exemple de transitions

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Rôles d’application

Les rôles d’application définissent un ensemble de rôles qui peuvent être assignés aux utilisateurs souhaitant agir ou participer au sein de l’application. Les rôles d’application peuvent servir à limiter les actions et la participation dans l’application blockchain et les workflows correspondants. 

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| NOM | Nom unique du rôle d’application. Le contrat intelligent correspondant doit utiliser la même propriété **Name** pour le rôle applicable. Les noms de type de base sont réservés. Vous ne pouvez pas nommer un rôle d’application de la même manière que [Type](#type)| OUI |
| Description | Description du rôle d’application. | Non  |

### <a name="application-roles-example"></a>Exemple de rôles d’application

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identificateurs

Les identificateurs représentent une collection d’informations utilisées pour décrire les propriétés de workflow, le constructeur et les paramètres de fonction. 

| Champ | Description | Obligatoire |
|-------|-------------|:--------:|
| NOM | Nom unique de la propriété ou du paramètre. Le contrat intelligent correspondant doit utiliser la même propriété **Name** pour la propriété ou le paramètre applicable. | OUI |
| DisplayName | Nom d’affichage convivial pour la propriété ou le paramètre. | OUI |
| Description | Description de la propriété ou du paramètre. | Non  |

### <a name="identifiers-example"></a>Exemple d’identificateurs

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Exemple de fichier de configuration

L’exemple suivant définit une application demande-réponse de base dans laquelle un demandeur envoie une demande et un répondeur envoie une réponse à la demande.

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Étapes suivantes

[Déployer Azure Blockchain Workbench](blockchain-workbench-deploy.md)

