---
title: Bouton Déployer dans Azure
description: Utilisez le bouton pour déployer des modèles Azure Resource Manager à partir d’un référentiel GitHub.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 77d23852941177aca35a582f64b21bfac02d7942
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108736898"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Utiliser un bouton de déploiement pour déployer des modèles à partir du référentiel GitHub

Cet article explique comment utiliser le bouton **Déployer sur Azure** pour déployer des modèles à partir d’un référentiel GitHub. Vous pouvez ajouter le bouton directement au fichier _README.md_ dans votre référentiel GitHub. Vous pouvez également ajouter le bouton à une page web faisant référence au dépôt.

L’étendue du déploiement est déterminée par le schéma du modèle. Pour plus d'informations, consultez les pages suivantes :

- [resource groups](deploy-to-resource-group.md)
- [subscriptions](deploy-to-subscription.md)
- [groupes d’administration](deploy-to-management-group.md)
- [tenants](deploy-to-tenant.md)

## <a name="use-common-image"></a>Utiliser une image courante

Pour ajouter le bouton à votre page web ou à votre référentiel, utilisez l’image suivante :

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

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
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json
```

Ensuite, convertissez l’URL en une valeur encodée URL. Vous pouvez utiliser un encodeur en ligne ou exécuter une commande. L’exemple PowerShell suivant montre comment encoder une valeur par URL.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

L’exemple d’URL a la valeur suivante lorsque l’URL est encodée.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json
```

Chaque lien commence par la même URL de base :

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Ajoutez votre lien de modèle encodé par URL à la fin de l’URL de base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json
```

Vous avez votre URL complète pour le lien.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Si vous utilisez [Git avec Azure Repos](/azure/devops/repos/git/) au lieu d’un référentiel GitHub, vous pouvez toujours utiliser le bouton **Déployer dans Azure**. Assurez-vous que votre référentiel est public. Utilisez l’[opération Items](/rest/api/azure/devops/git/items/get) pour récupérer le modèle. Votre requête doit respecter le format suivant :

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Encodez cette URL de demande.

## <a name="create-deploy-to-azure-button"></a>Créer le bouton Déployer dans Azure

Enfin, mettez le lien et l’image ensemble.

Pour ajouter le bouton dans le fichier _README.md_ de votre référentiel GitHub ou d’une page web en utilisant Markdown, utilisez :

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json)
```

Pour le langage HTML, utilisez :

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Pour Git avec Azure Repos, le bouton se présente au format suivant :

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Déployer le modèle

Pour tester la solution complète, sélectionnez le bouton suivant :

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json)

Le portail affiche un volet qui vous permet de fournir facilement des valeurs de paramètre. Les paramètres sont préremplis avec les valeurs par défaut du modèle. Le nom de paramètre en casse mixte *storageAccountType* défini dans le modèle est converti en une chaîne séparée par des espaces lorsqu’il est affiché sur le portail.

![Utiliser le portail pour déployer](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les modèles, voir [Comprendre la structure et la syntaxe des modèles ARM](template-syntax.md).
