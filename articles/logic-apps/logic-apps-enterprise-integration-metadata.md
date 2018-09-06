---
title: Gestion des métadonnées d’artefact de compte d’intégration - Azure Logic Apps | Microsoft Docs
description: Ajout ou suppression de métadonnées d’artefact à partir de comptes d’intégration dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128801"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Gestion des métadonnées d’artefact à partir de comptes d’intégration dans Azure Logic Apps avec Enterprise Integration Pack

Vous pouvez définir des métadonnées personnalisées pour les artefacts dans les comptes d’intégration et récupérer ces métadonnées pendant l’exécution pour votre application logique. Par exemple, vous pouvez spécifier des métadonnées pour les artefacts tels que des partenaires, des contrats, des schémas et mappages ; tous stockent les métadonnées à l’aide de paires clé-valeur. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Ajoute de métadonnées à des artefacts dans des comptes d’intégration

1. Dans le portail Azure, créez un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md).

2. Ajoutez un artefact à votre compte d’intégration, par exemple, un [partenaire](logic-apps-enterprise-integration-partners.md), un [contrat](logic-apps-enterprise-integration-agreements.md) ou un [schéma](logic-apps-enterprise-integration-schemas.md).

3. Sélectionnez l’artefact, choisissez **Modifier** et entrez les informations des métadonnées.

   ![Saisie des métadonnées](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Récupération des métadonnées à partir d’artefacts pour les applications logiques

1. Dans le portail Azure, créez une [application logique](quickstart-create-first-logic-app-workflow.md).

2. Créez un [lien de votre application logique vers votre compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. Dans le Concepteur d’application logique, ajoutez un déclencheur comme **Request** ou **HTTP** à votre application logique.

4. Sous le déclencheur, choisissez **Nouvelle étape** > **Ajouter une action**. Recherchez « compte d’intégration », puis recherchez et sélectionnez cette action : **Compte d’intégration - Recherche d’artefact de compte d’intégration**.

   ![Sélectionner Recherche d’artefact de compte d’intégration](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Sélectionnez le **type d’artefact** et indiquez son **nom**. Par exemple : 

   ![Sélectionner le type d’artefact et spécifiez son nom](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exemple : récupération des métadonnées du partenaire

Supposons que ce partenaire possède ces métadonnées avec des détails `routingUrl` :

![Rechercher des métadonnées de « routingURL » de partenaire](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Dans votre application logique, ajoutez votre déclencheur, une action **Compte d’intégration - Recherche d’artefact de compte d’intégration** pour votre partenaire et une action **HTTP**, par exemple :

   ![Ajouter un déclencheur, une recherche d’artefact et une action HTTP à votre application logique](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Pour récupérer l’URI, dans la barre d’outils du concepteur d’application logique, choisissez **mode Code** pour votre application logique. La définition de votre application logique doit ressembler à cet exemple :

   ![Recherche](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les contrats](logic-apps-enterprise-integration-agreements.md)
