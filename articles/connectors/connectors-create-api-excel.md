---
title: Se connecter à Excel Online – Azure Logic Apps | Microsoft Docs
description: Gérer les données avec les API REST de Excel Online et Azure Logic Apps
ms.service: logic-apps
services: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 917b9abd4a32b7951313c5555f4111dff990078c
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230949"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gérer les données Excel Online avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur Excel Online, vous pouvez créer des tâches et des workflows automatisés basés sur vos données dans Excel Online Entreprise ou OneDrive. Ce connecteur fournit des actions qui vous aident à travailler avec vos données et à gérer des feuilles de calcul, par exemple : 

* Créer des tables et des feuilles de calcul.
* Obtenir et gérer des feuilles de calcul, des tables et des lignes.
* Ajouter des lignes uniques et des colonnes clés.

Vous pouvez ensuite utiliser les sorties de ces actions avec des actions pour d’autres services. Par exemple, si vous utilisez une action qui crée des feuilles de calcul chaque semaine, vous pouvez utiliser une autre action qui envoie un e-mail de confirmation à l’aide du connecteur Outlook d’Office 365.

Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Les connecteurs [Excel Online pour les entreprises](/connectors/excelonlinebusiness/) et [Excel en ligne pour OneDrive](/connectors/excelonline/) fonctionnent avec Azure Logic Apps et diffère du [connecteur Excel pour PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Un [compte Office 365](https://www.office.com/) pour votre compte Microsoft professionnel ou personnel 

  Vos données Excel peuvent exister dans un fichier de valeurs séparées par des virgules (CSV) dans un dossier de stockage, OneDrive par exemple. 
  Vous pouvez également utiliser le même fichier CSV avec le [connecteur de fichier plat](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à vos données Excel Online. Ce connecteur fournit uniquement des actions, par conséquent, pour démarrer votre application logique, sélectionnez un déclencheur distinct, par exemple, le déclencheur **Périodicité**.

## <a name="add-excel-action"></a>Ajouter une action Excel

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques, si elle n’est pas encore ouverte.

1. Sous le déclencheur, choisissez **Nouvelle étape**.

1. Dans la zone de recherche, entrez « excel » comme filtre. Sous la liste des actions, sélectionnez l’action souhaitée.

1. Si vous êtes invité à vous connecter à votre compte Office 365, choisissez **Se connecter**. 

   Vos informations d’identification autorisent votre application logique à créer une connexion à Excel Online et à accéder à votre données.

1. Continuez de fournir les informations nécessaires pour l’action sélectionnée et générez le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, telles que les actions et les limites, comme décrit dans les fichiers Swagger des connecteurs, consultez la page de référence du connecteur :

* [Excel Online pour les entreprises](/connectors/excelonlinebusiness/) 
* [Excel Online pour OneDrive](/connectors/excelonline/) 

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
