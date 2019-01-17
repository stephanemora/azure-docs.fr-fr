---
title: Activité de fonction Azure dans Azure Data Factory | Microsoft Docs
description: Découvrir comment utiliser l’activité de fonction Azure pour exécuter une fonction Azure dans un pipeline Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: ee99733440d74424f98a2ed16de83c88bae53ff1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321787"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Activité de fonction Azure dans Azure Data Factory

L’activité de fonction Azure vous permet d’exécuter [Azure Functions](../azure-functions/functions-overview.md) dans un pipeline Data Factory. Pour exécuter une fonction Azure, vous devez créer une connexion de service lié et une activité qui spécifie la fonction Azure que vous envisagez d’exécuter.

Pour une présentation de huit minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Service lié de fonction Azure

Le type de retour de la fonction Azure doit être un `JObject` valide (n’oubliez pas que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) est *pas* un `JObject`). Tout type de retour autre que `JObject` échoue et lève l’erreur d’utilisateur générique *Erreur lors de l’appel du point de terminaison*.

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

## <a name="more-info"></a>En savoir plus

L’activité de fonction Azure prend en charge le **routage**. Par exemple, si votre application utilise le routage `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>`, l’élément `functionName` est défini sur `functionName/{value}`, que vous pouvez paramétrer pour fournir le texte `functionName` souhaité lors de l’exécution.

L’activité de fonction Azure prend également en charge les **requêtes**. Une requête doit faire partie de l’élément `functionName`, par exemple `HttpTriggerCSharp2?name=hello` , où le `function name` est `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les activités dans Data Factory dans [Pipelines et activités dans Azure Data Factory](concepts-pipelines-activities.md).
