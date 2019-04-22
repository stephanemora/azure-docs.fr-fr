---
title: Installer des packages dans un bloc-notes Jupyter sur Azure
description: Guide pratique pour installer des packages Python, R et F# à partir d’un bloc-notes Jupyter en cours d’exécution sur Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 504158f248cde3a399475cdec99de3e6a4ebfcc5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276853"
---
# <a name="install-packages-from-within-a-notebook"></a>Installer des packages à partir d’un bloc-notes

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

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour configurer et gérer des projets](configure-manage-azure-notebooks-projects.md)
- [Guide pratique : Présenter un diaporama](present-jupyter-notebooks-slideshow.md)
