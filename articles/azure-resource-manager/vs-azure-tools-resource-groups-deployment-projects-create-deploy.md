---
title: Projets de groupe de ressources Azure avec Visual Studio | Microsoft Docs
description: Utilisez Visual Studio pour créer un projet de groupe de ressources Azure et déployer les ressources dans Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2019
ms.author: tomfitz
ms.openlocfilehash: 36815010c3e8fccc2ec24ce344071d0836da219b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097455"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio

Avec Visual Studio, vous pouvez créer un projet qui déploie votre infrastructure et votre code sur Azure. Par exemple, vous pouvez définir l’hôte web, le site web et la base de données de votre application, et déployer cette infrastructure parallèlement au code. Visual Studio fournit de nombreux modèles de démarrage différents pour déployer des scénarios courants. Dans cet article, vous déployez une application web et SQL Database.  

Cet article montre comment utiliser [Visual Studio 2017 avec des charges de travail ASP.NET et de développement installées](/dotnet/azure/dotnet-tools). Si vous utilisez Visual Studio 2015 Update 2 et le kit de développement logiciel (SDK) Microsoft Azure pour .NET 2.9 ou Visual Studio 2013 avec Azure SDK 2.9, votre expérience sera sensiblement identique.

## <a name="create-azure-resource-group-project"></a>Créer un projet de groupe de ressources Azure

Au cours de cette procédure, vous allez créer un projet de groupe de ressources Azure avec un modèle **Application web + SQL**.

1. Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**, choisissez **C#** ou **Visual Basic** (le langage choisi n’a aucun impact pendant les phases ultérieures, car ces projets présentent uniquement du contenu JSON et PowerShell). Choisissez ensuite **Cloud** et le projet de **Groupe de ressources Azure**.
   
    ![Projet de déploiement cloud](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)
2. Choisissez le modèle à déployer sur Azure Resource Manager. Notez qu’il existe de nombreuses options différentes selon le type de projet que vous voulez déployer. Pour cet article, sélectionnez le modèle **Application web + SQL**.
   
    ![Sélectionner un modèle](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)
   
    Le modèle que vous choisissez est seulement un point de départ ; vous pouvez ajouter et supprimer des ressources en fonction des besoins de votre scénario.
   
   > [!NOTE]
   > Visual Studio extrait une liste des modèles disponibles en ligne. La liste peut évoluer.
   > 
   > 
   
    Visual Studio crée un projet de déploiement de groupe de ressources Azure pour une application web et une base de données SQL.
3. Pour voir les éléments créés, consultez les nœuds du projet de déploiement.
   
    ![afficher les nœuds](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)
   
    Puisque nous avons choisi le modèle Application web + SQL pour cet exemple, vous voyez les fichiers suivants : 
   
   | Nom de fichier | Description |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Script PowerShell qui exécute des commandes PowerShell à déployer sur Azure Resource Manager.<br />**Remarque** Visual Studio utilise ce script PowerShell pour déployer votre modèle. Les modifications apportées à ce script affectent également le déploiement dans Visual Studio. Par conséquent, modifiez ce script avec prudence. |
   | WebSiteSQLDatabase.json |Le modèle Resource Manager qui définit l’infrastructure que vous voulez déployer sur Azure, et les paramètres que vous pouvez fournir au cours du déploiement. Il définit également les dépendances entre les ressources de manière à ce que Resource Manager les déploie dans le bon ordre. |
   | WebSiteSQLDatabase.parameters.json |Un fichier de paramètres qui contient les valeurs requises par le modèle. Vous transmettez des valeurs de paramètres pour personnaliser chaque déploiement. |
   
    Tous les projets de déploiement de groupe de ressources Azure contiennent ces fichiers de base. D'autres projets peuvent contenir des fichiers supplémentaires pour prendre en charge d'autres fonctionnalités.

## <a name="customize-the-resource-manager-template"></a>Personnaliser le modèle de gestionnaire de ressources
Vous pouvez personnaliser un projet de déploiement en modifiant les modèles JSON qui décrivent les ressources à déployer. JSON signifie JavaScript Object Notation. Il s'agit d'un format de données sérialisées facile à utiliser. Les fichiers JSON utilisent un schéma que vous référencez au début de chaque fichier. Si vous souhaitez comprendre le schéma, vous pouvez le télécharger et l’analyser. Le schéma définit les éléments valides, les types et les formats des champs et les valeurs énumérées possibles pour une propriété. Pour en savoir plus sur les éléments du modèle Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

Pour travailler sur votre modèle, ouvrez **WebSiteSQLDatabase.json**.

L’éditeur Visual Studio fournit des outils pour vous aider à modifier le modèle Resource Manager. La fenêtre **Structure JSON** permet de voir facilement les éléments définis dans votre modèle.

![afficher la structure JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Il vous suffit de sélectionner l’un des éléments de la structure pour accéder à cette partie du modèle et mettre en évidence le JSON correspondant.

![explorer JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Vous pouvez ajouter une ressource en sélectionnant le bouton **Ajouter une ressource** en haut de la fenêtre Structure JSON, ou en double-cliquant sur **Ressources** et en sélectionnant **Ajouter une nouvelle ressource**.

![ajouter une ressource](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Pour ce tutoriel, sélectionnez **Compte de stockage** et donnez-lui un nom. Choisissez un nom qui ne contient pas plus de 11 caractères et uniquement des chiffres et des lettres minuscules.

![ajouter du stockage](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Notez qu’en plus de la ressource, un paramètre pour le compte de stockage de type et une variable pour le nom du compte de stockage ont été ajoutés.

![afficher la structure](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Le paramètre **storageType** est prédéfini avec des types autorisés et un type par défaut. Vous pouvez laisser ces valeurs ou les modifier pour votre scénario. Si vous ne souhaitez autoriser une personne à déployer un compte de stockage **Premium_LRS** via ce modèle, supprimez ce dernier des types autorisés. 

```json
"storageType": {
  "type": "string",
  "defaultValue": "Standard_LRS",
  "allowedValues": [
    "Standard_LRS",
    "Standard_ZRS",
    "Standard_GRS",
    "Standard_RAGRS"
  ]
}
```

Visual Studio fournit également des fonctionnalités IntelliSense pour vous aider à comprendre les propriétés qui sont disponibles lors de la modification du modèle. Par exemple, pour modifier les propriétés de votre plan App Service, accédez à la ressource **HostingPlan** et ajoutez une valeur pour les **propriétés**. Notez qu’intellisense affiche les valeurs disponibles et fournit une description de cette valeur.

![afficher intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Vous pouvez définir **numberOfWorkers** sur 1.

```json
"properties": {
  "name": "[parameters('hostingPlanName')]",
  "numberOfWorkers": 1
}
```

## <a name="deploy-the-resource-group-project-to-azure"></a>Déployer le projet de groupe de ressources sur Azure
Vous êtes maintenant prêt à déployer votre projet. Lorsque vous déployez un projet de groupe de ressources Azure, vous le déployez dans un groupe de ressources Azure. Un groupe de ressources est un regroupement logique de ressources qui partagent un cycle de vie commun.

1. Dans le menu contextuel du nœud du projet de déploiement, choisissez **Déployer** > **Nouveau**.
   
    ![Déployer, élément de menu Nouveau déploiement](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)
   
    La boîte de dialogue **Déployer vers le groupe de ressources** s’affiche.
   
    ![Boîte de dialogue Déployer vers le groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)
2. Dans la zone de liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un. Pour créer un groupe de ressources, ouvrez la zone de liste déroulante **Groupe de ressources** et sélectionnez **Créer**.
   
    ![Boîte de dialogue Déployer vers le groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)
   
    La boîte de dialogue **Créer un groupe de ressources** s’affiche. Donnez à votre groupe un nom et un emplacement, puis sélectionnez le bouton **Créer**.
   
    ![Boîte de dialogue Créer un groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
3. Modifiez les paramètres du déploiement en sélectionnant le bouton **Modifier les paramètres**.
   
    ![Bouton Modifier les paramètres](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)
4. Fournissez des valeurs pour les paramètres vides et sélectionnez le bouton **Enregistrer**. Les paramètres vides sont **hostingPlanName**, **administratorLogin**, **administratorLoginPassword** et **databaseName**.
   
    **hostingPlanName** spécifie le nom du [plan App Service](../app-service/overview-hosting-plans.md) à créer. 
   
    **administratorLogin** spécifie le nom d’utilisateur de l’administrateur SQL Server. N’utilisez pas de noms d’administrateur communs comme **sa** ou **admin**. 
   
    **administratorLoginPassword** spécifie le mot de passe d’administrateur SQL Server. L’option **Enregistrer les mots de passe en texte brut dans le fichier de paramètres** n’est pas sécurisée. Ne sélectionnez pas cette option. Étant donné que le mot de passe n’est pas enregistré en texte brut, vous devez indiquer ce mot de passe à nouveau lors du déploiement. 
   
    **databaseName** spécifie le nom de la base de données à créer. 
   
    ![Boîte de dialogue Modifier les paramètres](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
5. Sélectionnez le bouton **Déployer** pour déployer le projet dans Azure. Une console PowerShell s’ouvre en dehors de l’instance de Visual Studio. Entrez le mot de passe d’administrateur SQL Server dans la console PowerShell lorsque vous y êtes invité. **Votre console PowerShell peut être masquée par d’autres éléments ou réduite dans la barre des tâches.** Recherchez-la et sélectionnez-la pour indiquer le mot de passe.
   
   > [!NOTE]
   > Il se peut que Visual Studio vous invite à installer les cmdlets Azure PowerShell. Si vous y êtes invité, installez-les. Vous devez disposer des modules Azure PowerShell pour déployer correctement des groupes de ressources. Le script PowerShell due projet ne fonctionne pas avec le nouveau [module Azure PowerShell Az](/powershell/azure/new-azureps-module-az). 
   >
   > Pour plus d’informations, consultez [Installer et configurer des modules Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
   > 
   > 
6. Ce déploiement peut prendre quelques minutes. La fenêtre **Sortie** indique l’état du déploiement. Lorsque le déploiement est terminé, le dernier message indique que le déploiement a été réalisé avec succès :
   
        ... 
        18:00:58 - Successfully deployed template 'websitesqldatabase.json' to resource group 'DemoSiteGroup'.
7. Dans un navigateur, ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte. Pour afficher le groupe de ressources, sélectionnez **Groupes de ressources** et le groupe de ressources vers lequel vous avez effectué le déploiement.
   
    ![sélectionner un groupe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)
8. Vous verrez toutes les ressources déployées. Notez que le nom du compte de stockage n’est pas exactement celui spécifié lors de l’ajout de cette ressource. Le compte de stockage doit être unique. Le modèle ajoute automatiquement une chaîne de caractères au nom que vous avez spécifié pour créer un nom unique. 
   
    ![afficher des ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)
9. Si vous modifiez et redéployez votre projet, choisissez le groupe de ressources existant dans le menu contextuel du projet de groupe de ressources Azure. Dans le menu contextuel, sélectionnez **Déployer**, puis le groupe de ressources que vous avez déployé.
   
    ![Groupe de ressources Azure déployé](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Déployer le code avec votre infrastructure
À ce stade, vous avez déployé l’infrastructure de votre application, mais aucun code réel n’est déployé avec le projet. Cet article montre comment déployer une application web et des tables SQL Database lors du déploiement. Si vous déployez une machine virtuelle au lieu d’une application web, vous devrez exécuter du code sur la machine dans le cadre du déploiement. Le processus de déploiement du code pour une application web ou pour la configuration d’une machine virtuelle est quasiment le même.

1. Ajoutez un projet à votre solution Visual Studio. Cliquez avec le bouton droit sur la solution et sélectionnez **Ajouter** > **Nouveau projet**.
   
    ![ajouter un projet](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)
2. Ajoutez une **application web ASP.NET**. 
   
    ![ajouter une application web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
3. Sélectionnez **MVC**.
   
    ![sélectionner MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
4. Une fois votre application web créée par Visual Studio, vous voyez les deux projets dans la solution.
   
    ![afficher les projets](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)
5. À présent, vous devez vous assurer que votre projet de groupe de ressources est informé du nouveau projet. Revenez à votre projet de groupe de ressources (AzureResourceGroup1). Cliquez avec le bouton droit sur **Références** et sélectionnez **Ajouter une référence**.
   
    ![ajouter une référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)
6. Sélectionnez le projet d’application web que vous avez créé.
   
    ![ajouter une référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
   
    En ajoutant une référence, vous liez le projet d’application web au projet de groupe de ressources et définissez automatiquement trois propriétés clés. Vous voyez ces propriétés dans la fenêtre **Propriétés** de la référence.
   
      ![voir une référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
   
    Les propriétés sont les suivantes :
   
   * **Propriétés supplémentaires** contient l’emplacement intermédiaire du package de déploiement web qui est envoyé vers Azure Storage. Notez le dossier (ExampleApp) et le fichier (package.zip). Vous devez connaître ces valeurs, car elles doivent être fournies en tant que paramètres lors du déploiement de l’application. 
   * **Inclure le chemin d’accès au fichier** contient le chemin d’accès à l’emplacement de création du package. **Inclure les cibles** contient la commande que ce déploiement exécute. 
   * La valeur par défaut **Build;Package** permet au déploiement de construire et créer un package de déploiement web (package.zip).  
     
     Vous n’avez pas besoin de profil de publication, car le déploiement obtient les informations nécessaires à partir des propriétés pour créer le package.
7. Revenez à WebSiteSQLDatabase.json et ajoutez une ressource au modèle.
   
    ![ajouter une ressource](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)
8. Cette fois-ci, sélectionnez **Web Deploy pour les applications Web**. 
   
    ![ajouter web deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
9. Redéployez votre projet de groupe de ressources vers le groupe de ressources. Cette fois, il y a de nouveaux paramètres. Vous n’avez pas besoin de fournir des valeurs pour **_artifactsLocation** ou **_artifactsLocationSasToken** car Visual Studio les génère automatiquement. Toutefois, vous devez définir le dossier et le nom de fichier sur le chemin d’accès qui contient le package de déploiement (présenté comme **ExampleAppPackageFolder** et **ExampleAppPackageFileName** dans l’image suivante). Indiquez les valeurs que vous avez vues précédemment dans les propriétés de référence (**ExampleApp** et **package.zip**).
   
    ![ajouter web deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
   
    Pour le **compte de stockage des artefacts**, sélectionnez celui déployé avec ce groupe de ressources.
10. Une fois le déploiement terminé, sélectionnez votre application web dans le portail. Sélectionnez l’URL pour accéder au site.
    
     ![parcourir le site](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)
11. Notez que vous avez déployé avec succès l’application ASP.NET par défaut.
    
     ![afficher l’application déployée](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-an-operations-dashboard-to-your-deployment"></a>Ajouter un tableau de bord des opérations à votre déploiement
Vous n’êtes pas limité aux seules ressources qui sont disponibles via l’interface de Visual Studio. Vous pouvez personnaliser votre déploiement en ajoutant une ressource personnalisée à votre modèle. Pour afficher l’ajout d’une ressource, vous ajoutez un tableau de bord opérationnel pour gérer la ressource déployée.

1. Ouvrez le fichier WebsiteSqlDeploy.json et ajoutez le bloc de code JSON suivant après la ressource de compte de stockage, mais avant le `]` de fermeture de la section de ressources.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

2. Redéployez votre groupe de ressources. Examinez votre tableau de bord sur le portail Azure et notez que le tableau de bord partagé a été ajoutée à votre liste de choix.

   ![Tableau de bord personnalisé](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

3. Sélectionnez le tableau de bord.

   ![Tableau de bord personnalisé](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Vous pouvez gérer l’accès au tableau de bord en utilisant des groupes RBAC. Vous pouvez également personnaliser l’apparence du tableau de bord après son déploiement. Toutefois, si vous redéployez le groupe de ressources, le tableau de bord conserve son état par défaut dans votre modèle. Pour plus d’informations sur la création de tableaux de bord, consultez [Créer par programmation des tableaux de bord Azure](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment créer et déployer des modèles avec Visual Studio. Le didacticiel suivant vous montre comment rechercher les informations à partir des références de modèle pour pouvoir créer un compte de stockage Azure chiffré.

> [!div class="nextstepaction"]
> [Créer un compte de stockage chiffré](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
