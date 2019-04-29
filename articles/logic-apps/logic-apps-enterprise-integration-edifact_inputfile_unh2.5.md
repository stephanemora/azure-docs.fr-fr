---
title: Gérer des messages EDIFACT avec des segments UNH 2.5 - Azure Logic Apps | Microsoft Docs
description: Résoudre des documents EDIFACT avec des segments UNH2.5 dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681654"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Gérer des documents EDIFACT avec des segments UNH2.5 dans Azure Logic Apps

Lorsque UNH2.5 est présent dans le document EDIFACT, il est utilisé pour la recherche de schéma. 

Exemple : Le champ UNH est **EAN008** dans le message EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Étapes à suivre pour gérer le message 
1. Mettre à jour le schéma
2. Vérifier les paramètres de l’accord  

## <a name="update-the-schema"></a>Mettre à jour le schéma
Pour traiter le message, vous devez déployer un schéma avec le nom de nœud racine UNH2.5.  Pour l’exemple donné, le nom racine du schéma est **EFACT_D03B_ORDERS_EAN008**  

Pour chaque D03B_ORDERS avec un autre segment UNH2.5, vous devez déployer un schéma individuel.  

## <a name="add-schema-to-the-edifact-agreement"></a>Ajouter un schéma à l’accord EDIFACT
### <a name="edifact-decode"></a>Décodage EDIFACT
Pour décoder le message entrant, configurez le schéma EDIFACT dans les paramètres de réception de l’accord EDIFACT.
1. Ajouter le schéma au compte d’intégration    
2. Configurez le schéma EDIFACT dans les paramètres de réception de l’accord EDIFACT. 
3. Sélectionnez un accord EDIFACT et cliquez sur **Modifier au format JSON**.  Ajoutez la valeur UNH2.5 dans l’accord de réception **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png).

### <a name="edifact-encode"></a>Encodage EDIFACT
Pour encoder le message entrant, configurez le schéma EDIFACT dans les paramètres d’envoi de l’accord EDIFACT.
1. Ajouter le schéma au compte d’intégration    
2. Configurez le schéma EDIFACT dans les paramètres d’envoi de l’accord EDIFACT. 
3. Sélectionnez un accord EDIFACT et cliquez sur **Modifier au format JSON**.  Ajoutez la valeur UNH2.5 dans l’accord d’envoi **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les contrats de compte d’intégration](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  