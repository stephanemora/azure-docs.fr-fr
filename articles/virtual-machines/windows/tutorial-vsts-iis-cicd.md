---
title: Didacticiel - Créer un pipeline CI/CD dans Azure avec Azure DevOps Services | Microsoft Docs
description: Ce didacticiel explique comment créer un pipeline Azure DevOps Services pour une intégration et une livraison continues permettant de déployer une application web vers IIS sur une machine virtuelle Windows dans Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 4b4d514ec8bfd78b303a7f51c2a4072507da5be9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968987"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-azure-devops-services-and-iis"></a>Didacticiel : créer un pipeline d’intégration continue avec Azure DevOps Services et IIS
Pour automatiser les phases de création, de test et de déploiement du développement de l’application, vous pouvez utiliser un pipeline d’intégration et de déploiement continus (CI/CD). Dans ce didacticiel, vous créez un pipeline CI/CD à l’aide d’Azure DevOps Services et d’une machine virtuelle Windows dans Azure qui exécute IIS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Publier une application web ASP.NET dans un projet Azure DevOps Services
> * Créer un pipeline de build qui est déclenché par des validations de code
> * Installer et configurer IIS sur une machine virtuelle dans Azure
> * Ajouter l’instance IIS à un groupe de déploiement dans Azure DevOps Services
> * Créer un pipeline de mise en production pour publier de nouveaux packages de déploiement web sur IIS
> * test du pipeline CI/CD

Ce tutoriel requiert le module Azure PowerShell version 5.7.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-project-in-azure-devops-services"></a>Créer un projet dans Azure DevOps Services
Azure DevOps Services simplifie la collaboration et le développement sans nécessiter de solution de gestion de code en local. Azure DevOps Services offre des fonctionnalités de test de code, de génération et d’analyse d’application dans le cloud. Vous pouvez choisir le référentiel de contrôle de version et l’IDE qui conviennent le mieux au développement de votre code. Pour ce didacticiel, vous pouvez utiliser une organisation gratuite pour créer une application web ASP.NET de base et un pipeline CI/CD. Si vous n’avez pas encore d’organisation Azure DevOps Services, [créez-en une](http://go.microsoft.com/fwlink/?LinkId=307137).

Pour gérer le processus de validation de code, les pipelines de build et les pipelines de mise en production, créez un projet dans Azure DevOps Services comme suit :

1. Ouvrez votre tableau de bord Azure DevOps Services dans un navigateur web, puis choisissez **Nouveau projet**.
2. Entrez *myWebApp* comme **nom de projet**. Laissez toutes les autres valeurs par défaut pour utiliser le contrôle de version *Git* et le processus d’élément de travail *Agile*.
3. Choisissez l’option **Partager avec** *Membres de l’équipe*, puis sélectionnez **Créer**.
5. Une fois votre projet créé, sélectionnez l’option **Initialize with a README or gitignore** (Initialiser avec un fichier README ou gitignore), puis **Initialiser**.
6. À l’intérieur de votre nouveau projet, choisissez **Tableaux de bord** en haut, puis sélectionnez **Ouvrir dans Visual Studio**.


## <a name="create-aspnet-web-application"></a>Création d’une application web ASP.NET
À l’étape précédente, vous avez créé un projet dans Azure DevOps Services. La dernière étape ouvre votre nouveau projet dans Visual Studio. Vous gérez vos validations de code dans la fenêtre **Team Explorer**. Créez une copie locale de votre nouveau projet, puis créez une application web ASP.NET à partir d’un modèle comme suit :

1. Sélectionnez **Cloner** pour créer un référentiel git local de votre projet Azure DevOps Services.
    
    ![Cloner un référentiel à partir d’un projet Azure DevOps Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Sous **Solutions**, sélectionnez **Nouveau**.

    ![Création de la solution d’application web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Sélectionnez les modèles **web**, puis choisissez le modèle **Application web ASP.NET**.
    1. Entrez un nom pour votre application, tel que *myWebApp*, et décochez la case **Créer un répertoire pour la solution**.
    2. Si l’option est disponible, décochez la case **Add Application Insights to project** (Ajouter Application Insights au projet). Application Insights nécessite que vous autorisiez votre application web dans Azure Application Insights. Pour faire simple, dans ce didacticiel, ignorez cette procédure.
    3. Sélectionnez **OK**.
4. Dans la liste des modèles, choisissez **MVC**.
    1. Pour **Modifier l’authentification**, sélectionnez **Aucune authentification**, puis sélectionnez **OK**.
    2. Sélectionnez **OK** pour créer votre solution.
5. Dans la fenêtre **Team Explorer**, choisissez **Modifications**.

    ![Valider des modifications locales apportées au dépôt Git d’Azure Repos](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Dans la zone de texte de validation, entrez un message tel que *Validation initiale*. Dans le menu déroulant, choisissez **Commit All and Sync** (Tout valider et synchroniser).


## <a name="create-build-pipeline"></a>Créer un pipeline de build
Dans Azure DevOps Services, vous utilisez un pipeline de build pour préciser la manière dont votre application doit être générée. Dans ce didacticiel, nous créons un pipeline de base qui prend notre code source, génère la solution, puis crée le package de déploiement web que nous pouvons utiliser pour exécuter l’application web sur un serveur IIS.

1. Dans votre projet Azure DevOps Services, choisissez **Build & Release** (Générer et publier) en haut, puis sélectionnez **Builds**.
3. Sélectionnez **+ Nouvelle définition**.
4. Choisissez le modèle **ASP.NET (préversion)** et sélectionnez **Appliquer**.
5. Laissez les valeurs par défaut pour les tâches. Sous **Obtenir les sources**, assurez-vous que le référentiel *myWebApp* et la branche *master* sont sélectionnés.

    ![Créer un pipeline de build dans un projet Azure DevOps Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Sur l’onglet **Déclencheurs**, déplacez le curseur **Enable this trigger** (Activer ce déclencheur) sur *Activé*.
7. Enregistrez le pipeline de build et mettez en file d’attente une nouvelle build en sélectionnant **Save & queue** (Enregistrer et mettre en file d’attente), puis de nouveau **Save & queue** (Enregistrer et mettre en file d’attente). Conservez les valeurs par défaut et sélectionnez **File d’attente**.

Vérifiez que la build est planifiée sur un agent hébergé, puis commencez la génération. Le résultat ressemble à l’exemple suivant :

![Génération réussie de projet Azure DevOps Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Créer une machine virtuelle
Pour fournir une plateforme sur laquelle exécuter votre application web ASP.NET, vous avez besoin d’une machine virtuelle Windows qui exécute IIS. Azure DevOps Services utilise un agent pour interagir avec l’instance IIS lorsque vous validez le code et que les builds sont déclenchées.

Créez une machine virtuelle Windows Server 2016 avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant permet de créer une machine virtuelle nommée *myVM* dans l’emplacement *USA Est*. Le groupe de ressources *myResourceGroupAzureDevOpsServices* et les ressources réseau connexes sont également créés. Pour autoriser le trafic web, le port TCP *80* est ouvert à la machine virtuelle. Lorsque vous y êtes invité, fournissez un nom d’utilisateur et un mot de passe qui serviront d’informations d’identification de connexion pour la machine virtuelle :

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupAzureDevOpsServices" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

Pour vous connecter à votre machine virtuelle, obtenez l’adresse IP publique avec [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) comme suit :

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Créez une session Bureau à distance vers votre machine virtuelle :

```cmd
mstsc /v:<publicIpAddress>
```

Sur la machine virtuelle, ouvrez une invite de commande **Administrateur PowerShell**. Installez IIS et les fonctionnalités .NET requises comme suit :

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Créer un groupe de déploiement

Pour diffuser le package de déploiement web vers le serveur IIS, vous définissez un groupe de déploiement dans Azure DevOps Services. Ce groupe vous permet de spécifier quels serveurs sont la cible de nouvelles builds lorsque vous validez du code dans Azure DevOps Services et que les builds sont terminées.

1. Dans Azure DevOps Services, choisissez **Build & Release** (Générer et publier), puis sélectionnez **Groupes de déploiement**.
2. Choisissez **Add Deployment group** (Ajouter un groupe de déploiement).
3. Entrez un nom pour le groupe, tel que *myIIS*, puis sélectionnez **Créer**.
4. Dans la section **Register machines** (Enregistrer des ordinateurs), assurez-vous que l’option *Windows* est sélectionnée, puis cochez la case **Use a personal access token in the script for authentication** (Utiliser un jeton d’accès personnel dans le script pour l’authentification).
5. Sélectionnez **Copy script to clipboard** (Copier le script dans le Presse-papiers).


### <a name="add-iis-vm-to-the-deployment-group"></a>Ajouter une machine virtuelle IIS au groupe de déploiement
Une fois le groupe de déploiement créé, ajoutez-y chaque instance IIS. Azure DevOps Services génère un script qui télécharge et configure un agent sur la machine virtuelle qui reçoit les nouveaux packages de déploiement web, puis l’applique à IIS.

1. Dans la session **Administrateur PowerShell** sur votre machine virtuelle, collez le script copié à partir d’Azure DevOps Services et exécutez-le.
2. Lorsque vous êtes invité à configurer les balises pour l’agent, choisissez *Y* et entrez *web*.
3. Lorsque vous êtes invité à indiquer le compte d’utilisateur, appuyez sur *Retour* pour accepter les valeurs par défaut.
4. Attendez que le script se termine avec un message de type *Service vstsagent.account.computername démarré correctement*.
5. Dans la page **Groupes de déploiement** du menu **Build & Release** (Générer et publier), ouvrez le groupe de déploiement *myIIS*. Sur l’onglet **Machines**, vérifiez que votre machine virtuelle est répertoriée.

    ![Machine virtuelle ajoutée au groupe de déploiement Azure DevOps Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-pipeline"></a>Créer un pipeline de mise en production
Pour publier vos builds, vous créez un pipeline de mise en production dans Azure DevOps Services. Ce pipeline est déclenché automatiquement en cas de réussite de la génération de votre application. Vous choisissez le groupe de déploiement dans lequel distribuer votre application web et définissez les paramètres IIS appropriés.

1. Choisissez **Build & Release** (Générer et publier), puis sélectionnez **Builds**. Sélectionnez le pipeline de build créé à l’étape précédente.
2. Sous **Effectuées récemment**, choisissez la build la plus récente, puis sélectionnez **Mise en production**.
3. Choisissez **Oui** pour créer un pipeline de mise en production.
4. Choisissez le modèle **vide**, puis sélectionnez **Suivant**.
5. Vérifiez que le projet et le pipeline de build source sont renseignés avec votre projet.
6. Cochez la case **Déploiement continu**, puis sélectionnez **Créer**.
7. Sélectionnez la liste déroulante en regard de **+ Ajouter des tâches** et choisissez *Add a deployment group phase* (Ajouter une phase de déploiement de groupe).
    
    ![Ajouter une tâche à un pipeline de mise en production dans Azure DevOps Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Choisissez **Ajouter** en regard de **IIS Web App Deploy (Preview)** (Déploiement d’application web IIS (préversion)), puis sélectionnez **Fermer**.
9. Sélectionnez la tâche parente **Run on deployment group** (Exécuter sur le groupe de déploiement).
    1. Pour **Groupe de déploiement**, sélectionnez le groupe de déploiement que vous avez créé précédemment, par exemple *myIIS*.
    2. Dans la zone **Machine tags** (Balises de machine), sélectionnez **Ajouter** et choisissez la balise *web*.
    
    ![Tâche de groupe de déploiement de pipeline de mise en production pour IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Sélectionnez la tâche **Déployer : déploiement de l’application web IIS** pour configurer les paramètres de votre instance IIS comme suit :
    1. Pour **Nom du site web**, entrez *Site web par défaut*.
    2. Laissez tous les autres paramètres par défaut.
12. Choisissez **Enregistrer**, puis sélectionnez **OK** deux fois.


## <a name="create-a-release-and-publish"></a>Création d’une version et publication
Vous pouvez désormais distribuer votre package de déploiement web en tant que nouvelle version. Cette étape communique avec l’agent sur chaque instance qui fait partie du groupe de déploiement, distribue le package de déploiement web, puis configure IIS pour exécuter l’application web mise à jour.

1. Dans votre pipeline de mise en production, sélectionnez **+ Mise en production**, puis **Créer une mise en production**.
2. Vérifiez que la build la plus récente est sélectionnée dans la liste déroulante, ainsi que **Déploiement automatisé : après la création de la mise en production**. Sélectionnez **Créer**.
3. Une petite bannière apparaît en haut de votre pipeline de mise en production, par exemple, *La mise en production 'Mise en production-1' a été créée*. Sélectionnez le lien de la mise en production.
4. Ouvrez l’onglet **Journaux** pour surveiller la progression de la mise en production.
    
    ![Réussite de la mise en production Azure DevOps Services et de la distribution du package de déploiement web](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Une fois la mise en production terminée, ouvrez un navigateur web et entrez l’adresse IP publique de votre machine virtuelle. Votre application web ASP.NET est en cours d’exécution.

    ![Application web ASP.NET s’exécutant sur une machine virtuelle IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Test de l’ensemble du pipeline CI/CD
Votre application web s’exécutant sur IIS, testez à présent l’ensemble du pipeline CI/CD. Lorsque vous apportez une modification dans Visual Studio et validez votre code, une build est déclenchée, qui déclenche ensuite une mise en production de votre package de déploiement web mis à jour sur IIS :

1. Dans Visual Studio, ouvrez la fenêtre **Explorateur de solutions**.
2. Recherchez et ouvrez *myWebApp | Vues | Accueil | Index.cshtml*
3. Modifiez la ligne 6 en :

    `<h1>ASP.NET with Azure DevOps Services and CI/CD!</h1>`

4. Enregistrez le fichier .
5. Ouvrez la fenêtre **Team Explorer**, sélectionnez le projet *myWebApp*, puis choisissez **Modifications**.
6. Entrez un message de validation, tel que *Test du pipeline CI/CD*, puis choisissez **Commit All and Sync** (Tout valider et synchroniser) dans le menu déroulant.
7. Dans l’espace de travail Azure DevOps Services, une nouvelle build est déclenchée à partir de la validation du code. 
    - Choisissez **Build & Release** (Générer et publier), puis sélectionnez **Builds**. 
    - Choisissez votre pipeline de build, puis sélectionnez la build **en file d’attente et en cours d’exécution** pour afficher la progression.
9. Une fois la génération réussie, une nouvelle mise en production est déclenchée.
    - Choisissez **Build & Release** (Générer et publier), puis **Mises en production**, pour voir le package de déploiement web distribué sur votre machine virtuelle IIS. 
    - Sélectionnez l’icône **Actualiser** pour mettre à jour l’état. Lorsque la colonne *Environnements* affiche une coche verte, cela signifie que la mise en production a bien été déployée sur IIS.
11. Pour afficher les modifications que vous avez appliquées, actualisez votre site web IIS dans un navigateur.

    ![Application web ASP.NET s’exécutant sur une machine virtuelle IIS à partir d’un pipeline CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une application web ASP.NET dans Azure DevOps Services et configuré des pipelines de build et de mise en production pour déployer de nouveaux packages de déploiement web sur IIS à chaque validation de code. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Publier une application web ASP.NET dans un projet Azure DevOps Services
> * Créer un pipeline de build qui est déclenché par des validations de code
> * Installer et configurer IIS sur une machine virtuelle dans Azure
> * Ajouter l’instance IIS à un groupe de déploiement dans Azure DevOps Services
> * Créer un pipeline de mise en production pour publier de nouveaux packages de déploiement web sur IIS
> * test du pipeline CI/CD

Passez à l’étape suivante du didacticiel pour apprendre à installer la pile SQL&#92;IIS&#92;.NET sur deux machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Pile SQL&#92;IIS&#92;.NET](tutorial-iis-sql.md)
