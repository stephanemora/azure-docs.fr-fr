---
title: Création de blueprints dynamiques au moyen de paramètres dans Azure Blueprint
description: Découvrez les paramètres statiques et dynamiques et comment les utiliser pour créer des blueprints dynamiques.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993576"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Création de blueprints dynamiques au moyen de paramètres

Un blueprint entièrement défini avec divers artefacts (tels que des groupes de ressources, des modèles Resource Manager, des stratégies ou des attributions de rôles) permet de créer rapidement et de provisionner de manière cohérente des objets dans Azure. Pour permettre une utilisation flexible de ces conteneurs et modèles de conception réutilisables, Azure Blueprint prend en charge des paramètres. Le paramètre offre, à la fois pendant la définition et l’affectation, la flexibilité nécessaire pour changer les propriétés sur les artefacts déployés par le blueprint.

Un exemple simple est l’artefact de groupe de ressources. Quand un groupe de ressources est créé, deux valeurs obligatoires doivent être fournies : le nom et l’emplacement. Lors de l’ajout d’un groupe de ressources à votre blueprint, si les paramètres n’existaient pas, il vous faudrait définir ce nom et cet emplacement pour chaque utilisation du blueprint. Chaque utilisation du blueprint créerait alors des artefacts dans le même groupe de ressources. Bien que cela ne soit pas un problème pour le groupe de ressources proprement dit, les ressources contenues dans ce groupe deviendraient dupliquées et provoqueraient des conflits.

> [!NOTE]
> Cela ne pose pas de problème que deux blueprints différents incluent un groupe de ressources portant le même nom.
> Si un groupe de ressources inclus dans un blueprint existe déjà, le blueprint continue à créer les artefacts associés dans ce groupe de ressources. Cela peut provoquer un conflit, puisque deux ressources ayant le même nom et le même type de ressource ne peuvent pas exister dans un abonnement.

C’est là que les paramètres entrent en jeu. Blueprint vous autorise à ne pas définir la valeur de ces propriétés (dans le cas du groupe de ressources les propriétés de nom et d’emplacement) pendant la définition du blueprint, mais plutôt lors de l’affectation à un abonnement. Il est ainsi possible de réutiliser un blueprint qui crée un groupe de ressources et d’autres ressources dans un seul abonnement sans générer de conflit.

## <a name="blueprint-parameters"></a>Paramètres de blueprint

À l’aide de l’API REST, vous pouvez créer des paramètres sur le blueprint proprement, en plus de chacun des artefacts pris en charge. Quand un paramètre est créé sur le blueprint, il peut être utilisé par les artefacts de ce blueprint. Le préfixe pour nommer le groupe de ressources constitue un exemple. L’artefact peut ensuite utiliser le paramètre de blueprint pour créer un paramètre « principalement dynamique », puisque le paramètre pourrait toujours être défini lors de l’affectation, mais offrira une cohérence pouvant respecter les règles de nommage de l’organisation. Pour obtenir les étapes nécessaires, consultez [Définition de paramètres statiques - paramètre de niveau blueprint](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Utilisation de paramètres secureString et secureObject

Bien qu’un _artefact_ de modèle Resource Manager prenne en charge les paramètres de type **secureString** et **secureObject**, Azure Blueprint exige que chacun d’eux soit connecté avec un coffre de clés Azure.
Cela empêche la pratique risquée consistant à stocker des secrets avec le blueprint, et encourage l’utilisation de modèles sécurisés. Azure Blueprint facilite cette pratique en détectant l’inclusion de l’un ou l’autre paramètre sécurisé dans un _artefact_ de modèle Resource Manager, et en demandant au cours de l’affectation de blueprint les propriétés Key Vault suivantes pour chaque paramètre sécurisé détecté :

- ID de ressource Key Vault
- Nom du secret Key Vault
- Version du secret Key Vault

Le coffre de clés référencé doit exister dans le même abonnement que celui auquel le blueprint est affecté, et doit également avoir l’option **Activer l’accès à Azure Resource Manager pour le déploiement de modèles** configurée dans la page **Stratégies d’accès** du coffre de clés. Pour obtenir des instructions sur la façon d’activer cette fonctionnalité, consultez [Key Vault - Activer le déploiement de modèle](../../../managed-applications/key-vault-access.md#enable-template-deployment).
Pour plus d’informations sur Azure Key Vault, consultez [Présentation de Key Vault](../../../key-vault/key-vault-overview.md)

## <a name="parameter-types"></a>Types de paramètres

### <a name="static-parameters"></a>Paramètres statiques

Une valeur de paramètre définie dans la définition d’un blueprint est appelée **paramètre statique**, car chaque utilisation du blueprint déploiera l’artefact à l’aide de cette valeur statique. Dans l’exemple de groupe de ressources, bien que cela n’ait aucun sens pour le nom du groupe de ressources, cela peut être plus logique pour l’emplacement. Dans ce cas, chaque affectation du blueprint créerait le groupe de ressources, indépendamment de son nom durant l’affectation, au même emplacement. Cela vous permet d’être sélectif quant à ce que vous définissez comme obligatoire et ce qui peut être changé lors de l’affectation.

#### <a name="setting-static-parameters-in-the-portal"></a>Définition des paramètres statiques dans le portail

1. Lancez le service Azure Blueprint dans le portail Azure. Pour cela, cliquez sur **Tous les services**, puis recherchez et sélectionnez **Stratégie** dans le volet gauche. Dans la page **Stratégie**, cliquez sur **Blueprints**.

1. Sélectionnez **Définitions de blueprint** dans la page de gauche.

1. Cliquez sur un blueprint existant puis sur **Modifier le blueprint**, ou cliquez sur **+ Créer un blueprint** et renseignez les informations sous l’onglet **De base**.

1. Cliquez sur **Suivant : Artefacts** OU cliquez sur l’onglet **Artefacts**.

1. Les artefacts ajoutés au blueprint ayant des options de paramètre comportent la mention **X paramètres renseignés sur Y** dans la colonne **Paramètres**. Cliquez sur la ligne de l’artefact pour modifier ses paramètres.

   ![Paramètres de blueprint](../media/parameters/parameter-column.png)

1. La page **Modifier un artefact** affiche des options de valeur appropriées à l’artefact sur lequel vous avez cliqué. Chaque paramètre sur l’artefact a un titre, une zone de valeur et une case à cocher. Décochez la case pour rendre le **paramètre statique**. Dans l’exemple ci-dessous, seul l’_Emplacement_ est un **paramètre statique**, car la case est décochée, tandis que la case _Nom du groupe de ressources_ est cochée.

   ![Paramètres statiques de blueprint](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Définition des paramètres statiques à partir de l’API REST

Dans chaque URI d’API REST, vous devez remplacer les variables utilisées par vos propres valeurs :

- Remplacer `{YourMG}` par le nom de votre groupe d’administration
- Remplacer `{subscriptionId}` par votre ID d’abonnement

##### <a name="blueprint-level-parameter"></a>Paramètre de niveau blueprint

Quand vous créez un blueprint par le biais de l’API REST, vous pouvez créer des [paramètres de blueprint](#blueprint-parameters). Pour ce faire, utilisez le format de corps et d’URI d’API REST suivant :

- URI de l’API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

- Corps de la requête

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Une fois que vous avez créé un paramètre de niveau blueprint, vous pouvez l’utiliser sur les artefacts ajoutés à ce blueprint.
L’exemple d’API REST suivant crée un artefact d’attribution de rôle sur le blueprint et utilise le paramètre de niveau blueprint.

- URI de l’API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
  ```

- Corps de la requête

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

Dans cet exemple, la propriété **principalIds** a utilisé le paramètre de niveau blueprint **owners** en fournissant une valeur `[parameters('owners')]`. La définition d’un paramètre sur un artefact à l’aide d’un paramètre de niveau blueprint est toujours un exemple de **paramètre statique**, car ce paramètre ne peut pas être défini pendant l’affectation de blueprint et aura cette valeur lors de chaque affectation.

##### <a name="artifact-level-parameter"></a>Paramètre de niveau artefact

La création de **paramètres statiques** sur un artefact est similaire, mais prend directement une valeur au lieu d’utiliser la fonction `parameters()`. L’exemple suivant crée deux paramètres statiques, **tagName** et **tagValue**. La valeur de chaque paramètre est fournie directement et n’utilise pas d’appel de fonction.

- URI de l’API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
  ```

- Corps de la requête

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Paramètres dynamiques

Un **paramètre dynamique** est l’inverse d’un paramètre statique. Il s’agit d’un paramètre qui n’est pas défini sur le blueprint, mais durant chaque affectation du blueprint. Dans l’exemple de groupe de ressources, cela se justifie pour le nom du groupe de ressources, car cela permet de fournir un nom différent pour chaque affectation du blueprint.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Définition des paramètres dynamiques dans le portail

1. Lancez le service Azure Blueprint dans le portail Azure. Pour cela, cliquez sur **Tous les services**, puis recherchez et sélectionnez **Stratégie** dans le volet gauche. Dans la page **Stratégie**, cliquez sur **Blueprints**.

1. Sélectionnez **Définitions de blueprint** dans la page de gauche.

1. Cliquez avec le bouton droit sur le blueprint que vous souhaitez affecter puis sélectionnez **Affecter le blueprint**, OU cliquez sur le blueprint que vous souhaitez affecter, puis cliquez sur le bouton **Affecter le blueprint**.

1. Dans la page **Affecter le blueprint**, recherchez la section **Paramètres d’artefact**. Chaque artefact contenant au moins un **paramètre dynamique** présente l’artefact et les options de configuration. Indiquez les valeurs requises pour les paramètres avant d’affecter le blueprint. Dans l’exemple ci-dessous, _Name_ est un **paramètre dynamique** qui doit être défini pour terminer l’affectation du blueprint.

   ![Paramètre dynamique de blueprint](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Définition des paramètres dynamiques à partir de l’API REST

La définition des **paramètres dynamiques** pendant l’affectation s’effectue en fournissant directement la valeur souhaitée. Au lieu d’utiliser une fonction, telle que `parameters()`, la valeur fournie est une chaîne appropriée. Les artefacts pour un groupe de ressources sont définis avec un « nom de modèle », et des propriétés de **nom** et d’**emplacement**. Tous les autres paramètres pour chaque artefact inclus sont définis sous **Paramètres** avec une paire de clés **\<nom\>** et **valeur**. Si le blueprint est configuré pour un paramètre dynamique qui n’est pas fourni lors de l’affectation, celle-ci échoue.

- URI de l’API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

- Corps de la requête

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [cycle de vie des blueprints](lifecycle.md)
- Apprendre à personnaliser [l’ordre de séquencement des blueprints](sequencing-order.md)
- Découvrir comment utiliser le [verrouillage des ressources de blueprint](resource-locking.md)
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md)
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md)