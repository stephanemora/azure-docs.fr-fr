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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: douglasl
ms.openlocfilehash: ef93c62a2e2084a43eeda578c889a568d04db4f1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52854386"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Activité de fonction Azure dans Azure Data Factory

L’activité de fonction Azure vous permet d’exécuter [Azure Functions](../azure-functions/functions-overview.md) dans un pipeline Data Factory. Pour exécuter une fonction Azure, vous devez créer une connexion de service lié et une activité qui spécifie la fonction Azure que vous envisagez d’exécuter.

## <a name="azure-function-linked-service"></a>Service lié de fonction Azure

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
| body  | Corps envoyé avec la demande à la méthode d’api de la fonction  | Chaîne (ou expression avec resultType de chaîne).   | Obligatoire pour les méthodes PUT/POST |
|   |   |   | |

Reportez-vous au schéma de la charge utile de demande dans la section  [Schéma de charge utile de demande](control-flow-web-activity.md#request-payload-schema) .

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les activités dans Data Factory dans [Pipelines et activités dans Azure Data Factory](concepts-pipelines-activities.md).