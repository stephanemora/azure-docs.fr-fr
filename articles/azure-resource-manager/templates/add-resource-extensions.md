---
title: Configuration de post-déploiement au moyen d’extensions
description: Découvrez comment utiliser les extensions de modèle Azure Resource Manager pour fournir des configurations de post-déploiement.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023495"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fournir des configurations de post-déploiement au moyen d’extensions

Les extensions de modèle sont de petites applications permettant d’exécuter des tâches de configuration et d’automatisation post-déploiement sur des ressources Azure. Les extensions de machine virtuelle sont les plus appréciées. Voir [Extensions et fonctionnalités de machine virtuelle pour Windows](../../virtual-machines/extensions/features-windows.md) et [Extensions et fonctionnalités de machine virtuelle pour Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensions

Les extensions existantes sont :

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Pour connaître les extensions qui sont disponibles, accédez à la [référence de modèle](https://docs.microsoft.com/azure/templates/). Dans **Filtrer par titre**, entrez **extension**.

Pour savoir comment utiliser ces extensions, consultez :

- [Tutoriel : Déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager](template-tutorial-deploy-vm-extensions.md).
- [Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
