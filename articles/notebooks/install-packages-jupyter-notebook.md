---
title: Installer des packages dans des blocs-notes Jupyter – Version préliminaire d’Azure Notebooks
description: Découvrez comment installer des packages Python, R et F# à partir d’un bloc-notes Jupyter en cours d’exécution sur Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 43d38c9f3a4b0095967ab3e103ea729ec86ea2bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831214"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Installer des packages à partir de la version préliminaire d’Azure Notebooks

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Bien qu’il soit possible de configurer [l’environnement pour un notebook au niveau du projet](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), il peut se révéler intéressant d’installer directement des packages dans un bloc-notes en particulier.

Les packages installés à partir du bloc-notes s’appliquent uniquement à la session de serveur active. Les installations de packages ne sont pas conservées une fois le serveur arrêté.

## <a name="python"></a>Python

Pour installer des packages dans Python, vous pouvez utiliser pip ou conda à l’aide de commandes dans des cellules de code :

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Si la sortie de la commande indique que l’exigence est déjà satisfaite, Azure Notebooks est susceptible d’inclure le package par défaut. Le package peut également être installé grâce à une [étape de configuration de l’environnement du projet](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Pour installer des packages R, vous pouvez utiliser la fonction `install.packages` sur CRAN ou GitHub dans une cellule de code :

```r
install.packages("package_name")
```

Vous pouvez également installer des préversions et d’autres packages de développement sur GitHub à l’aide de la bibliothèque devtools :

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pour installer des packages F#, vous pouvez appeler le gestionnaire de dépendances Paket dans [nuget.org](https://www.nuget.org) dans des cellules de code. Tout d’abord, chargez le gestionnaire Paket :

```fsharp
#load "Paket.fsx"
```

Ensuite, installez les packages :

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Chargez ensuite le générateur Paket :
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Ouvrez la bibliothèque :
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Étapes suivantes

- [Procédure : configurer et gérer des projets](configure-manage-azure-notebooks-projects.md)
- [Procédure : présenter un diaporama](present-jupyter-notebooks-slideshow.md)
