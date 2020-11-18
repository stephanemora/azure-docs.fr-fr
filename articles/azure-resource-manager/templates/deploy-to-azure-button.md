---
title: Bouton Déployer dans Azure
description: Utilisez le bouton pour déployer des modèles Azure Resource Manager à partir d’un référentiel GitHub.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 7d002508f6b2402f8cff40fb0369896080ecbbad
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490897"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Utiliser un bouton de déploiement pour déployer des modèles à partir du référentiel GitHub

Cet article explique comment utiliser le bouton **Déployer sur Azure** pour déployer des modèles à partir d’un référentiel GitHub. Vous pouvez ajouter le bouton directement au fichier README.md dans votre dépôt GitHub. Vous pouvez également ajouter le bouton à une page web faisant référence au dépôt.

L’étendue du déploiement est déterminée par le schéma du modèle. Pour plus d'informations, consultez les pages suivantes :

* [resource groups](deploy-to-resource-group.md)
* [subscriptions](deploy-to-subscription.md)
* [groupes d’administration](deploy-to-management-group.md)
* [locataires](deploy-to-tenant.md).

## <a name="use-common-image"></a>Utiliser une image courante

Pour ajouter le bouton à votre page web ou à votre référentiel, utilisez l’image suivante :

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

L’image apparaît comme suit :

![Bouton Déployer dans Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Créer une URL pour le déploiement du modèle

Pour créer l’URL de votre modèle, commencez par l’URL brute du modèle dans votre référentiel. Pour afficher l’URL brute, sélectionnez **Brute**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="sélectionnez Brute":::

Le format de l’URL est le suivant :

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Ensuite, convertissez l’URL en une valeur encodée URL. Vous pouvez utiliser un encodeur en ligne ou exécuter une commande. L’exemple PowerShell suivant montre comment encoder une valeur par URL.

```powershell
[uri]::EscapeDataString($url)
```

L’exemple d’URL a la valeur suivante lorsque l’URL est encodée.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Chaque lien commence par la même URL de base :

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Ajoutez votre lien de modèle encodé par URL à la fin de l’URL de base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Vous avez votre URL complète pour le lien.

Si vous utilisez [Git avec Azure Repos](/azure/devops/repos/git/) au lieu d’un référentiel GitHub, vous pouvez toujours utiliser le bouton Déployer dans Azure. Assurez-vous que votre référentiel est public. Utilisez l’[opération Items](/rest/api/azure/devops/git/items/get) pour récupérer le modèle. Votre requête doit respecter le format suivant :

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Encodez cette URL de demande.

## <a name="create-deploy-to-azure-button"></a>Créer le bouton Déployer dans Azure

Enfin, mettez le lien et l’image ensemble.

Pour ajouter le bouton dans le fichier README.md de votre référentiel GitHub ou d’une page web en utilisant Markdown, utilisez :

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Pour le langage HTML, utilisez :

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Pour Git avec Azure Repos, le bouton se présente au format suivant :

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%252Freponame%252Fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Déployer le modèle

Pour tester la solution complète, sélectionnez le bouton suivant :

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Le portail affiche un volet qui vous permet de fournir facilement des valeurs de paramètre. Les paramètres sont préremplis avec les valeurs par défaut du modèle.

![Utiliser le portail pour déployer](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour apprendre à créer des modèles, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
