---
title: API de récupération des offres | Place de marché Azure
description: API récupère une liste récapitulative des offres d’un espace de noms du serveur de publication.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: a1f15e269481b9706f81fd02f19effc9ad37df32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280488"
---
<a name="retrieve-offers"></a>Récupération des offres
===============

Récupère une liste récapitulative des offres sous un espace de noms du serveur de publication.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Paramètres URI
--------------

| **Nom**         |  **Description**                         |  **Type de données** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificateur du serveur de publication, par exemple `contoso` |   String    |
|  api-version     | Dernière version de l’API                    |    Date        |
|  |  |


<a name="header"></a>En-tête
------

|  **Nom**        |         **Valeur**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autorisation   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Exemple de corps
------------

### <a name="response"></a>response

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Propriétés du corps de réponse

|  **Nom**       |       **Description**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifie le type d’offre                                                                                           |
|  publisherId    | L’identificateur qui identifie de façon unique ce serveur de publication                                                                      |
|  status         | État de l’offre. Pour la liste des valeurs possibles, consultez [État de l’offre](#offer-status) ci-dessous.                         |
|  id             | GUID qui identifie de façon unique l’offre dans l’espace de noms du serveur de publication.                                                    |
|  version        | Version actuelle de l’offre. La propriété de version ne peut pas être modifiée par le client. Elle est incrémentée après chaque publication. |
|  Définition     | Contient une vue résumée de la définition réelle de la charge de travail. Pour obtenir une définition détaillée, utilisez l’API[Récupérer une offre spécifique](./cloud-partner-portal-api-retrieve-specific-offer.md). |
|  changedTime    | Heure UTC de la dernière modification de l’offre                                                                              |
|  |  |


### <a name="response-status-codes"></a>Codes d’état de réponse

| **Code**  |  **Description**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` : la requête a été correctement traitée et toutes les offres de l’éditeur ont été retournées au client.  |
|  400      | `Bad/Malformed request` : le corps de la réponse d’erreur peut contenir plus d’informations.                                    |
|  403      | `Forbidden` : le client n’a pas accès à l’espace de noms spécifié.                                          |
|  404      | `Not found` : l’entité spécifiée n’existe pas.                                                                 |
|  |  |


### <a name="offer-status"></a>État de l’offre

|  **Nom**                    | **Description**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | L’offre n’a jamais été publiée.                  |
|  NotStarted                  | L’offre est nouvelle, mais n’a pas démarré.                 |
|  WaitingForPublisherReview   | L’offre est en attente d’approbation du serveur de publication.         |
|  Exécution en cours                     | La soumission de l’offre est en cours de traitement.             |
|  Opération réussie                   | La soumission de l’offre a été traitée.       |
|  Opération annulée                    | La soumission de l’offre a été annulée.                   |
|  Échec                      | La soumission de l’offre a échoué.                         |
|  |  |
