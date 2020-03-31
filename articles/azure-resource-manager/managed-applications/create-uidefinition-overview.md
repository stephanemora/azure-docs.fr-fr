---
title: Fichier CreateUiDefinition.json pour le volet du portail
description: Explique comment créer des définitions d’interface utilisateur pour le portail Azure. Utilisé lors de la définition d’applications managées Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294895"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json pour une expérience de création d’applications managées Azure

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
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

Une fonction CreateUiDefinition contient toujours trois propriétés : 

* gestionnaire
* version
* parameters

Le gestionnaire doit toujours être `Microsoft.Azure.CreateUIDef` et la version prise en charge la plus récente est `0.1.2-preview`.

Le schéma de la propriété des paramètres dépend de la version et du gestionnaire spécifiés. Pour les applications gérées, les propriétés prises en charge sont `basics`, `steps`, et `outputs`. Les propriétés basics et steps contiennent des [éléments](create-uidefinition-elements.md), comme des zones de texte et des listes déroulantes, à afficher dans le portail Azure. La propriété outputs est utilisée pour mettre en correspondance les valeurs de sortie des éléments spécifiés avec les paramètres du modèle de déploiement Azure Resource Manager.

L’inclusion de `$schema` est recommandée, mais facultative. Si la valeur de `version` est spécifiée, celle-ci doit correspondre à la version figurant dans l’`$schema` URI.

Vous pouvez utiliser un éditeur JSON pour créer votre fichier createUiDefinition, puis la tester dans le [sandbox (bac à sable) createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) pour en afficher un aperçu. Pour plus d’informations sur le bac à sable, consultez [Tester votre interface de portail pour les Applications managées Azure](test-createuidefinition.md).

## <a name="basics"></a>Concepts de base

Les principes de base sont les premières étapes générées lorsque le portail Microsoft Azure analyse le fichier. Outre le fait d’afficher des éléments spécifiés dans `basics`, le portail injecte des éléments permettant aux utilisateurs de choisir l’abonnement, le groupe de ressources et l’emplacement du déploiement. Lorsque cela est possible, les éléments demandant des paramètres concernant le déploiement, comme le nom d’un cluster ou des informations d’identification administrateur, doivent figurer dans cette étape.

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

## <a name="resource-types"></a>Types de ressources

Pour filtrer les emplacements disponibles afin d’obtenir uniquement ceux qui prennent en charge les types de ressources à déployer, fournissez un tableau des types de ressources. Si vous fournissez plusieurs types de ressources, seuls les emplacements qui prennent en charge tous les types de ressources sont retournés. Cette propriété est facultative.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Fonctions

CreateUiDefinition propose des [fonctions](create-uidefinition-functions.md) permettant de travailler avec les entrées et les sorties des éléments, ainsi que des fonctionnalités telles que des logiques conditionnelles. Ces fonctions ont une syntaxe et des fonctionnalités semblables aux fonctions de modèle d’Azure Resource Manager.

## <a name="next-steps"></a>Étapes suivantes

Le fichier createUiDefinition.json proprement dit a un schéma simple. Sa profondeur réelle provient de tous les éléments et fonctions pris en charge. Ces éléments sont décrits plus en détail dans :

- [Éléments](create-uidefinition-elements.md)
- [Fonctions](create-uidefinition-functions.md)

Un schéma JSON actuel pour createUiDefinition est disponible ici : `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Pour obtenir un exemple de fichier d’interface utilisateur, voir [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
