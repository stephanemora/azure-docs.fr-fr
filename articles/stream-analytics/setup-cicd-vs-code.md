---
title: Intégrez et développer avec Azure Stream Analytique CI/CD npm package en continu
description: Cet article décrit comment utiliser Azure Stream Analytique CI/CD npm package pour configurer une intégration continue et le processus de déploiement.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 7fe46712d610d881c21653461d12e4f8efecb468
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827874"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>Intégrez et développer avec le package npm de Stream Analytique CI/CD en continu
Cet article décrit comment utiliser le package npm Azure Stream Analytique CI/CD pour configurer une intégration continue et le processus de déploiement.

## <a name="build-the-vs-code-project"></a>Générez le projet Visual Studio Code

Vous pouvez activer l’intégration continue et déploiement pour les travaux Azure Stream Analytique à l’aide de la **asa-streamanalytics-cicd** package npm. Le package npm fournit les outils permettant de générer des modèles Azure Resource Manager de [les projets Stream Analytique Visual Studio Code](quick-create-vs-code.md). Il peut être utilisé sur Windows, macOS et Linux sans installer Visual Studio Code.

Une fois que vous avez [téléchargé le package](https://www.npmjs.com/package/azure-streamanalytics-cicd), utilisez la commande suivante pour générer les modèles Azure Resource Manager. Si le **outputPath** n’est pas spécifié, les modèles seront placés dans le **déployer** dossier sous le projet **bin** dossier.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

Quand un projet Stream Analytique Visual Studio Code est correctement généré, il génère les fichiers suivants du modèle Azure Resource Manager deux sous le **bin / [Debug/Retail] /Deploy** dossier : 

*  Fichier de modèle Resource Manager

       [ProjectName].JobTemplate.json 

*  Fichier de paramètres Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Les paramètres par défaut dans le fichier parameters.json proviennent des paramètres de votre projet Visual Studio. Si vous voulez effectuer un déploiement dans un autre environnement, remplacez les paramètres en conséquence.

> [!NOTE]
> Pour toutes les informations d’identification, les valeurs par défaut sont Null. Vous **devez** les définir avant le déploiement dans le cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Découvrez comment [effectuer un déploiement avec un fichier de modèle Resource Manager et Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Découvrez comment [utiliser un objet en tant que paramètre dans un modèle Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Pour utiliser une identité managée pour Azure Data Lake Store Gen1 comme récepteur de sortie, vous devez fournir l’accès au principal de service à l’aide de PowerShell avant de déployer sur Azure. Découvrez plus d’informations sur le [déploiement d’ADLS Gen1 avec une identité managée et le modèle Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créez un travail de cloud d’Azure Stream Analytique dans Visual Studio Code (version préliminaire)](quick-create-vs-code.md)
* [Tester des requêtes Stream Analytique localement à l’aide de Visual Studio Code (version préliminaire)](vscode-local-run.md)
* [Explorez Analytique Stream Azure avec Visual Studio Code (version préliminaire)](vscode-explore-jobs.md)
