---
title: Commandes et vue d'ensemble de l'interface CLI Bicep
description: Décrit les commandes que vous pouvez utiliser dans l'interface CLI Bicep. Ces commandes incluent la création de modèles Azure Resource Manager à partir de Bicep.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: dd1f292d4ce60353d2f8cecaaa83e38b26bdfaa3
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111540806"
---
# <a name="bicep-cli-commands"></a>Commandes de l'interface CLI Bicep

Cet article décrit les commandes que vous pouvez utiliser dans l'interface CLI Bicep. L'[interface CLI Bicep](./install.md) doit être installée pour pouvoir exécuter les commandes.

Cet article explique comment exécuter les commandes dans Azure CLI. Si vous n'utilisez pas Azure CLI, exécutez les commandes sans `az` au début de celle-ci. Par exemple, `az bicep version` devient ``bicep version``.

## <a name="build"></a>build

La commande `build` convertit un fichier Bicep en modèle Azure Resource Manager (modèle ARM). En général, vous n'avez pas besoin d'exécuter cette commande car elle s'exécute automatiquement lorsque vous déployez un fichier Bicep. Exécutez-la manuellement lorsque vous souhaitez voir le modèle ARM JSON créé à partir de votre fichier Bicep.

L'exemple suivant convertit un fichier Bicep nommé _main.bicep_ en modèle ARM nommé _main.json_. Le nouveau fichier est créé dans le même répertoire que le fichier Bicep.

```azurecli
az bicep build --file main.bicep
```

L'exemple suivant enregistre le fichier _main.json_ dans un autre répertoire.

```azurecli
 az bicep build --file main.bicep --outdir c:\jsontemplates
```

L'exemple suivant spécifie le nom et l'emplacement du fichier à créer.

```azurecli
az bicep build --file main.bicep --outfile c:\jsontemplates\azuredeploy.json
```

Pour imprimer le fichier sur `stdout`, utilisez :

```azurecli
az bicep build --file main.bicep --stdout
```

## <a name="decompile"></a>decompile

La commande `decompile` convertit le modèle ARM JSON en fichier Bicep.

```azurecli
az bicep decompile --file main.json
```

Pour plus d’informations sur l'utilisation de cette commande, consultez [Décompiler le modèle ARM JSON en Bicep](decompile.md).

## <a name="install"></a>installer

La commande `install` ajoute l'interface CLI Bicep à votre environnement local. Pour plus d'informations, consultez [Installer les outils Bicep](install.md).

Pour installer la version la plus récente, utilisez :

```azurecli
az bicep install
```

Pour installer une version spécifique :

```azurecli
az bicep install --version v0.3.255
```

## <a name="list-versions"></a>list-versions

La commande `list-versions` renvoie toutes les versions disponibles de l'interface CLI Bicep. Utilisez cette commande pour savoir si vous devez [mettre à niveau](#upgrade) ou [installer](#install) une nouvelle version.

```azurecli
az bicep list-versions
```

La commande renvoie le tableau des versions disponibles.

```azurecli
[
  "v0.4.1",
  "v0.3.539",
  "v0.3.255",
  "v0.3.126",
  "v0.3.1",
  "v0.2.328",
  "v0.2.317",
  "v0.2.212",
  "v0.2.59",
  "v0.2.14",
  "v0.2.3",
  "v0.1.226-alpha",
  "v0.1.223-alpha",
  "v0.1.37-alpha",
  "v0.1.1-alpha"
]
```

## <a name="upgrade"></a>upgrade

La commande `upgrade` procède à la mise à jour de la version installée vers la dernière version.

```azurecli
az bicep upgrade
```

## <a name="version"></a>version

La commande `version` renvoie la version installée.

```azurecli
az bicep version
```

La commande indique le numéro de version.

```azurecli
Bicep CLI version 0.4.1 (e2387595c9)
```

Si vous n'avez pas installé l'interface CLI Bicep, un message d'erreur apparaît pour indiquer que celle-ci est introuvable.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement d'un fichier Bicep, consultez :

* [Azure CLI](deploy-cli.md)
* [Cloud Shell](deploy-cloud-shell.md)
* [PowerShell](deploy-powershell.md)
