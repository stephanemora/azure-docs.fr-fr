---
title: Activité de fonction Azure dans Azure Data Factory | Microsoft Docs
description: Découvrir comment utiliser l’activité de fonction Azure pour exécuter une fonction Azure dans un pipeline Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 82786b8f01ce409179f4ddd37127679f9357cd0e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727069"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Activité de fonction Azure dans Azure Data Factory

L’activité de fonction Azure vous permet d’exécuter [Azure Functions](../azure-functions/functions-overview.md) dans un pipeline Data Factory. Pour exécuter une fonction Azure, vous devez créer une connexion de service lié et une activité qui spécifie la fonction Azure que vous envisagez d’exécuter.

Pour une présentation de huit minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Service lié de fonction Azure

Le type de retour de la fonction Azure doit être un `JObject` valide (n’oubliez pas que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) est *pas* un `JObject`). Aucun type de retour autre que `JObject` échoue et génère l’erreur utilisateur *contenu de la réponse n’est pas un JObject valide*.

| **Propriété** | **Description** | **Obligatoire** |
| --- | --- | --- |
| Type   | La propriété type doit être définie sur : **AzureFunction** | Oui |
| URL de l’application de fonction | URL de l’application de fonction Azure. Son format est `https://<accountname>.azurewebsites.net`. Cette URL correspond à la valeur indiquée dans la section **URL** quand vous affichez votre application de fonction dans le portail Azure.  | Oui |
| clé de fonction | Clé d’accès de la fonction Azure. Cliquez sur la section **Gérer** de la fonction correspondante, puis copiez la **clé de fonction** ou la **clé d’hôte**. Découvrez-en plus ici : [Déclencheurs et liaisons HTTP Azure Functions](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | Oui |
|   |   |   |

## <a name="azure-function-activity"></a>Activité de fonction Azure

| **Propriété**  | **Description** | **Valeurs autorisées** | **Obligatoire** |
| --- | --- | --- | --- |
| Nom  | Nom de l’activité dans le pipeline  | Chaîne | Oui |
| Type  | Le type d’activité est « AzureFunctionActivity ». | Chaîne | Oui |
| service lié | Service lié de fonction Azure de l’application de fonction Azure correspondante  | référence de service lié | Oui |
| nom de fonction  | Nom de la fonction dans l’application de fonction Azure que cette activité appelle. | Chaîne | Oui |
| method  | Méthode API REST de l’appel de fonction. | Types de chaîne pris en charge : « GET », « POST », « PUT »   | Oui |
| en-tête  | En-têtes envoyés à la demande. Par exemple, pour définir la langue et le type sur une demande : "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Chaîne (ou expression avec resultType de chaîne) | Non  |
| body  | Corps envoyé avec la demande à la méthode d’api de la fonction  | Chaîne (ou expression avec resultType de chaîne) ou objet.   | Obligatoire pour les méthodes PUT/POST |
|   |   |   | |

Reportez-vous au schéma de la charge utile de demande dans la section  [Schéma de charge utile de demande](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Routage et requêtes

L’activité de fonction Azure prend en charge le **routage**. Par exemple, si votre fonction Azure a le point de terminaison `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, puis le `functionName` à utiliser dans l’activité de fonction Azure est `<functionName>/<value>`. Vous pouvez paramétrer cette fonction pour fournir le texte souhaité `functionName` lors de l’exécution.

L’activité de fonction Azure prend également en charge les **requêtes**. Une requête doit être inclus dans le cadre de la `functionName`. Par exemple, lorsque le nom de fonction est `HttpTriggerCSharp` et la requête que vous souhaitez inclure est `name=hello`, puis vous pouvez construire la `functionName` dans l’activité de fonction Azure en tant que `HttpTriggerCSharp?name=hello`. Cette fonction peut être paramétrée pour la valeur peut être déterminée au moment de l’exécution.

## <a name="timeout-and-long-running-functions"></a>Délai d’expiration et les fonctions de longue durée

Azure Functions expire après 230 secondes, quel que soit le `functionTimeout` paramètre que vous avez configuré dans les paramètres. Pour plus d’informations, consultez [cet article](../azure-functions/functions-versions.md#timeout). Pour contourner ce comportement, suivent un modèle async ou utilisez les fonctions durables. L’avantage de fonctions durables est qu’elles offrent leur propre mécanisme de suivi de l’état, donc vous n’aurez pas à implémenter votre propre.

En savoir plus sur les fonctions durables dans [cet article](../azure-functions/durable/durable-functions-overview.md). Vous pouvez configurer une activité de fonction Azure pour appeler la fonction Durable, qui renvoie une réponse avec un autre URI, tel que [cet exemple](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Étant donné que `statusQueryGetUri` retourne 202 d’état HTTP tandis que la fonction est en cours d’exécution, vous pouvez interroger l’état de la fonction à l’aide d’une activité Web. Il suffit de définir une activité Web avec le `url` champ la valeur `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Lorsque la fonction Durable est terminée, la sortie de la fonction sera la sortie de l’activité Web.


## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les activités dans Data Factory dans [Pipelines et activités dans Azure Data Factory](concepts-pipelines-activities.md).
