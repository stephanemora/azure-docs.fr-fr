---
title: Cas de test pour les fichiers de paramètres pour le kit de ressources de test Azure Resource Manager
description: Décrit les tests de fichiers de paramètres exécutés par le kit de ressources de test de modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 37fefd84a6385e003a4bb501a789003e8c63d461
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393539"
---
# <a name="test-cases-for-parameter-files"></a>Cas de test pour les fichiers de paramètres

Cet article décrit les tests qui sont exécutés avec la [boîte à outils de test de modèle](test-toolkit.md) pour les [fichiers de paramètres](parameter-files.md). Par exemple, un fichier nommé _azuredeploy.parameters.json_. Les exemples incluent les noms des tests et des exemples de code avec lesquels les tests **réussissent** ou **échouent**. Pour plus d’informations sur l’exécution de tests ou sur l’exécution d’un test spécifique, consultez [Paramètres de test](test-toolkit.md#test-parameters).

La boîte à outils inclut les [cas de test](template-test-cases.md) pour les modèles Azure Resource Manager (modèles ARM) et les fichiers de modèles principaux nommés _azuredeploy.json_ ou _maintemplate.json_.

## <a name="use-valid-contentversion"></a>Utiliser un contentVersion valide

Nom du test : DeploymentParameters doit avoir ContentVersion

`contentVersion` doit contenir une chaîne au format `1.0.0.0` et utiliser uniquement des nombres.

Dans l’exemple suivant, le test **échoue**, car `contentVersion` manque.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

Dans l’exemple suivant, le test **échoue**, parce que `contentVersion` n’est pas une chaîne.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": {},
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="file-must-include-parameters"></a>Le fichier doit inclure des paramètres

Nom du test : DeploymentParameters doit avoir des paramètres

Un fichier de paramètres doit inclure la section `parameters`.

Dans l’exemple suivant, le test **échoue**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="use-valid-schema-version"></a>Utiliser une version de schéma valide

Nom du test : DeploymentParameters doit avoir un schéma

Le fichier de paramètres doit inclure une version de schéma valide.

Il existe deux versions de schéma valides pour les fichiers de paramètres :

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#`

Dans l’exemple suivant, le test **échoue**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2021-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="parameters-must-contain-values"></a>Les paramètres doivent contenir des valeurs

Nom du test : DeploymentParameters doit avoir une valeur

Un paramètre doit contenir une `value` ou une `reference`. Pour les secrets tels qu’un mot de passe, un coffre de clés utilise une `reference` dans le fichier de paramètres. Pour plus d’informations, consultez l’article [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](key-vault-parameter.md).

Dans l’exemple suivant, le test **échoue**, parce que `stgAcctName` n’a pas de `value`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {}
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la boîte à outils de test, consultez [Utiliser le kit de ressources de test de modèle ARM](test-toolkit.md).
- Pour les tests de modèle ARM, consultez [Cas de test pour les modèles ARM](template-test-cases.md).
- Pour les tests createUiDefinition, consultez [Cas de test pour createUiDefinition.json](createUiDefinition-test-cases.md).
- Pour en savoir plus sur les tests pour tous les fichiers, consultez [Cas de test pour tous les fichiers](all-files-test-cases.md).
