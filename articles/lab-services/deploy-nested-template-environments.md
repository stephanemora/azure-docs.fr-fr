---
title: Déployer des environnements de modèle Resource Manager imbriquée dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment déployer des modèles Azure Resource Manager imbriqués pour fournir des environnements avec Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835284"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Déployer des modèles Azure Resource Manager imbriqués pour les environnements de test
Un déploiement imbriqué vous permet d’exécuter d’autres modèles d’Azure Resource Manager à partir d’un modèle Resource Manager principal. Il vous permet de décomposer votre déploiement en un ensemble de modèles ciblés et à un usage particulier. Il offre des avantages en termes de test, réutilisation et la lisibilité. L’article [à l’aide de modèles liés lors du déploiement de ressources Azure](../azure-resource-manager/resource-group-linked-templates.md) fournit une bonne vue d’ensemble de cette solution avec plusieurs exemples de code. Cet article fournit un exemple qui est spécifique à Azure DevTest Labs. 

## <a name="key-parameters"></a>Paramètres de clé
Si vous pouvez créer votre propre modèle Resource Manager à partir de zéro, nous vous recommandons d’utiliser le [projet de groupe de ressources Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) dans Visual Studio, ce qui rend faciles à développer et déboguer des modèles. Lorsque vous ajoutez une ressource de déploiement imbriqué à azuredeploy.json, Visual Studio ajoute plusieurs éléments pour que le modèle plus flexible. Ces éléments incluent le sous-dossier avec le fichier de modèle et les paramètres secondaire, des noms de variables dans le fichier de modèle principal et deux paramètres pour l’emplacement de stockage pour les nouveaux fichiers. Le **_artifactsLocation** et **_artifactsLocationSasToken** sont les paramètres de clé qui utilise le dev/test. 

Si vous n’êtes pas familiarisé avec le fonctionnement de la dev/test avec les environnements, consultez [créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec des modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Vos modèles sont stockés dans le référentiel lié au laboratoire dans DevTest Labs. Lorsque vous créez un nouvel environnement avec ces modèles, les fichiers sont déplacés dans un conteneur de stockage Azure dans le laboratoire. Pour être en mesure d’identifier et de copier les fichiers imbriqués, dev/test identifie les paramètres _artifactsLocation et _artifactsLocationSasToken et copie les sous-dossiers jusqu’au conteneur de stockage. Ensuite, il insère automatiquement l’emplacement et le jeton de Signature d’accès partagé (SAP) dans les paramètres. 

## <a name="nested-deployment-example"></a>Exemple de déploiement imbriqué
Voici un exemple simple d’un déploiement imbriqué :

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Le dossier dans le référentiel contenant ce modèle comporte un sous-dossier `nestedtemplates` avec les fichiers **NestOne.json** et **NestOne.parameters.json**. Dans le **azuredeploy.json**, URI pour le modèle est généré à l’aide de l’emplacement des artefacts, le dossier de modèles imbriqués, imbriqués nom du fichier modèle. De même, l’URI pour les paramètres est intégrée à l’aide de l’emplacement des artefacts, le dossier de modèle imbriqué et le fichier de paramètres pour le modèle imbriqué. 

Voici l’image de la même structure de projet dans Visual Studio : 

![Structure de projet dans Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Vous pouvez ajouter des dossiers supplémentaires dans le dossier principal, mais pas tous plus en un seul niveau. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour plus d’informations sur les environnements : 

- [Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurer et utiliser des environnements publics dans Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Connecter un environnement de réseau virtuel de votre laboratoire Azure DevTest Labs](connect-environment-lab-virtual-network.md)