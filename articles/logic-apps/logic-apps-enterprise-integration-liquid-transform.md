---
title: Convertir des données JSON à l’aide de transformations Liquid
description: Créer des transformations ou des mappages pour des transformations JSON avancées à l’aide de Logic Apps et du modèle Liquid
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879171"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Effectuez des transformations JSON avancées avec des modèles Liquid dans Azure Logic Apps

Vous pouvez effectuer des transformations JSON de base dans vos applications logiques par le biais d’actions d’opérations de données natives telles que **Compose** ou **Parse JSON**. Pour effectuer des transformations JSON avancées, vous pouvez créer des modèles ou des mappages avec [Liquid](https://shopify.github.io/liquid/), qui est un langage de modèle open source destiné aux applications web flexibles. Un modèle Liquid définit comment transformer une sortie JSON et prend en charge des transformations JSON plus complexes (itérations, flux de contrôle, variables, etc.).

Avant de pouvoir effectuer une transformation Liquid dans votre application logique, vous devez définir le mappage JSON à JSON avec un modèle Liquid et le stocker dans votre compte d’intégration. Cet article vous montre comment créer et utiliser ce modèle ou ce mappage Liquid.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) de base

* Des connaissances de base sur le [langage de gabarit Liquid](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Créer un mappage ou un modèle Liquid pour votre compte d’intégration

1. Pour cet exemple, créez l’exemple de modèle Liquid décrit à cette étape. Dans votre modèle Liquid, vous pouvez définir des [filtres Liquid](https://shopify.github.io/liquid/basics/introduction/#filters), qui utilisent les conventions de nommage de [DotLiquid](https://github.com/dotliquid/dotliquid) et C#.

   > [!NOTE]
   > Assurez-vous que les noms de filtre utilisent la *mise en majuscules des phrases* dans votre modèle. Sinon, les filtres ne fonctionneront pas. En outre, des [limites de taille de fichier](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) s'appliquent aux mappages.

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. Dans le [portail Azure](https://portal.azure.com), dans la zone de recherche Azure, entrez `integration accounts`, puis sélectionnez **Comptes d’intégration**.

   ![Rechercher « Comptes d’intégration »](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Recherchez et sélectionnez votre compte d’intégration.

   ![Sélectionner le compte d’intégration](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Dans le volet **Vue d’ensemble**, sous **Composants**, sélectionnez **Mappages**.

    ![Sélectionner la vignette « Mappages »](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. Dans le volet **Mappages**, sélectionnez **Ajouter** et fournissez les informations suivantes pour votre mappage :

   | Propriété | Valeur | Description | 
   |----------|-------|-------------|
   | **Nom** | `JsonToJsonTemplate` | Nom de votre mappage (« JsontoJsonTemplate » dans cet exemple) | 
   | **Type de mappage** | **liquid** | Type de votre mappage. Pour une transformation JSON à JSON, vous devez sélectionner **liquid**. | 
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | Fichier de modèle ou de mappage Liquid existant à utiliser pour la transformation (« SimpleJsonToJsonTemplate.liquid » dans cet exemple). Pour trouver ce fichier, vous pouvez utiliser le sélecteur de fichiers. Pour connaître les limites de taille qui s'appliquent aux mappages, consultez [Limites et configuration](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Ajouter un modèle Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Ajouter l’action Liquid pour la transformation JSON

1. Dans le portail Azure, procédez comme suit pour [créer une application logique vierge](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Dans le Concepteur d’application logique, ajoutez le [déclencheur Requête](../connectors/connectors-native-reqres.md#add-request) à votre application logique.

1. Sous le déclencheur, choisissez **Nouvelle étape**. Dans la zone de recherche, entrez `liquid` comme filtre, puis sélectionnez cette action : **Transformer de JSON en JSON - Liquid**

   ![Rechercher et sélectionner l’action Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Ouvrez la liste **Mappage**, puis sélectionnez votre modèle Liquid (« JsonToJsonTemplate » dans cet exemple).

   ![Sélectionner le mappage](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Si la liste de mappages est vide, votre application logique n’est probablement pas liée à votre compte d’intégration. 
   Pour lier votre application logique au compte d’intégration associé au modèle ou au mappage Liquid, effectuez les étapes suivantes :

   1. Dans le menu de votre application logique, sélectionnez **Paramètres de flux de travail**.

   1. Dans la liste **Sélectionner un compte d’intégration**, sélectionnez votre compte d’intégration et sélectionnez **Enregistrer**.

      ![Lier une application logique à un compte d’intégration](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Ajoutez maintenant la propriété **Contenu** à cette action. Ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez la propriété **Contenu**.

   ![Ajouter la propriété « Contenu » à l’action](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Pour définir la valeur de la propriété **Contenu**, cliquez à l’intérieur de la zone **Contenu** afin que la liste de contenu dynamique s’affiche. Sélectionnez le jeton **Corps**, qui représente la sortie du contenu du corps à partir du déclencheur.

   ![Sélectionner le jeton « Corps » pour la valeur de propriété « Contenu »](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Une fois que vous avez terminé, l’action ressemble à cet exemple :

   ![Action « Transformer de JSON en JSON » terminée](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Tester votre application logique

Publiez l’entrée JSON sur votre application logique à partir de [Postman](https://www.getpostman.com/postman) ou d’un outil similaire. La sortie JSON transformée à partir de votre application logique ressemble à ceci :
  
![Exemple de sortie](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Autres exemples d’actions Liquid
Liquid n’est pas limité aux transformations JSON. Voici d’autres actions de transformation disponibles qui utilisent Liquid.

* Transformer du JSON en texte
  
  Voici le modèle Liquid utilisé pour cet exemple :
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Voici quelques exemples d’entrées et de sorties :
  
   ![Exemple de sortie JSON en texte](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformer du XML en JSON
  
  Voici le modèle Liquid utilisé pour cet exemple :
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Voici quelques exemples d’entrées et de sorties :

   ![Exemple de sortie XML en JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformer du XML en texte
  
  Voici le modèle Liquid utilisé pour cet exemple :

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Voici quelques exemples d’entrées et de sorties :

   ![Exemple de sortie XML en texte](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")  
* [En savoir plus sur les mappages](../logic-apps/logic-apps-enterprise-integration-maps.md "En savoir plus sur les mappages d’intégration d’entreprise")  

