---
title: CreateUiDefitinion.json pour une expérience de création d’applications managées par Azure | Microsoft Docs
description: Décrit comment créer des définitions d’interface utilisateur pour des applications gérées Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 50bbaf740a67d3830df2d0447b9522153cb8c93c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619082"
---
# <a name="createuidefitinionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefitinion.json pour une expérience de création d’applications managées par Azure
Ce document présente les principaux concepts du fichier **createUiDefinition.json** utilisé par le portail Microsoft Azure afin de définir l’interface utilisateur lors de la création d’une application managée.

Le modèle est le suivant :

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

Une fonction CreateUiDefinition contient toujours trois propriétés : 

* handler
* version
* parameters

Le gestionnaire doit toujours être `Microsoft.Azure.CreateUIDef` et la version prise en charge la plus récente est `0.1.2-preview`.

Le schéma de la propriété des paramètres dépend de la version et du gestionnaire spécifiés. Pour les applications gérées, les propriétés prises en charge sont `basics`, `steps`, et `outputs`. Les propriétés basics et steps contiennent des [éléments](create-uidefinition-elements.md), comme des zones de texte et des listes déroulantes, à afficher dans le portail Azure. La propriété outputs est utilisée pour mettre en correspondance les valeurs de sortie des éléments spécifiés avec les paramètres du modèle de déploiement Azure Resource Manager.

L’inclusion de `$schema` est recommandée, mais facultative. Si la valeur de `version` est spécifiée, celle-ci doit correspondre à la version figurant dans l’`$schema` URI.

Vous pouvez utiliser un éditeur JSON pour créer votre définition d’interface utilisateur puis la tester dans le [Bac à sable de définition d’interface utilisateur](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) pour l’afficher. Pour plus d’informations sur le bac à sable, consultez [Tester votre interface de portail pour les Applications managées Azure](test-createuidefinition.md).

## <a name="basics"></a>Concepts de base
Les principes de base sont les premières étapes générées lorsque le portail Microsoft Azure analyse le fichier. Outre le fait d’afficher des éléments spécifiés dans `basics`, le portail injecte des éléments permettant aux utilisateurs de choisir l’abonnement, le groupe de ressources et l’emplacement du déploiement. Lorsque cela est possible, les éléments demandant des paramètres concernant le déploiement, comme le nom d’un cluster ou des informations d’identification administrateur, doivent figurer dans cette étape.

Si le comportement d’un élément dépend de l’abonnement de l’utilisateur, du groupe de ressources ou de l’emplacement, cet élément ne peut pas être utilisé dans les principes de base. Par exemple, **Microsoft.Compute.SizeSelector** dépend de l’abonnement et de l’emplacement de l’utilisateur pour déterminer la liste des tailles disponibles. Par conséquent, **Microsoft.Compute.SizeSelector** ne peut être utilisé que dans steps. En règle générale, seuls les éléments de l’espace de noms **Microsoft.Common** peuvent être utilisés dans basics. Cependant, certains éléments dans d’autres espaces de noms (comme **Microsoft.Compute.Credentials**) qui ne dépendent pas du contexte de l’utilisateur, sont toujours autorisés.

## <a name="steps"></a>Étapes
La propriété steps peut contenir zéro ou plusieurs des étapes supplémentaires à afficher après les principes de base, chacun contenant un ou plusieurs éléments. Vous pouvez ajouter des étapes par rôle ou niveau de l’application déployée. Par exemple, ajoutez une étape pour les entrées destinées aux nœuds principaux et une étape pour les nœuds Worker à un cluster.

## <a name="outputs"></a>Outputs
Le portail Azure utilise la propriété `outputs` pour mettre en correspondance des éléments issus de `basics` et `steps` avec les paramètres du modèle de déploiement Azure Resource Manager. Les clés de ce dictionnaire sont les noms des paramètres du modèle et les valeurs sont les propriétés des objets de sortie issues des éléments référencés.

Pour définir le nom de ressource d’application managée, vous devez inclure une valeur nommée `applicationResourceName` dans la propriété des sorties. Si vous ne définissez pas cette valeur, l’application affecte un GUID au nom. Vous pouvez inclure une zone de texte dans l’interface utilisateur afin de demander un nom à l’utilisateur.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Fonctions
CreateUiDefinition propose des [fonctions](create-uidefinition-functions.md) permettant de travailler avec les entrées et les sorties des éléments, ainsi que des fonctionnalités telles que des logiques conditionnelles. Ces fonctions ont une syntaxe et des fonctionnalités semblables aux fonctions de modèle d’Azure Resource Manager.

## <a name="next-steps"></a>Étapes suivantes
Le fichier createUiDefinition.json proprement dit a un schéma simple. Sa profondeur réelle provient de tous les éléments et fonctions pris en charge. Ces éléments sont décrits plus en détail dans :

- [Éléments](create-uidefinition-elements.md)
- [Fonctions](create-uidefinition-functions.md)

Un schéma JSON actuel pour createUiDefinition est disponible ici : https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Pour obtenir un exemple de fichier d’interface utilisateur, voir [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
