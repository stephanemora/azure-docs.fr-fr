---
title: Exécuter une fabrique d’images à partir d’Azure DevOps dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment créer une fabrique d’images personnalisées dans Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 5a3d6e51a71f6aab742fe042d6e6e281192319a4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523016"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Exécuter une fabrique d’images depuis Azure DevOps
Cet article couvre les tâches de préparation nécessaires à l’exécution de la fabrique d’images à partir d’Azure DevOps (anciennement Visual Studio Team Services).

> [!NOTE]
> N’importe quel moteur d’orchestration fonctionnera ! Azure DevOps n’est pas obligatoire. La fabrique d’images est exécutée à l’aide de scripts Azure PowerShell, il peut donc être exécuté manuellement, à l’aide du Planificateur de tâches Windows, les autres systèmes CI/CD et ainsi de suite.

## <a name="create-a-lab-for-the-image-factory"></a>Créer un laboratoire pour la fabrique d’images
La première étape de configuration de la fabrique d’images consiste à créer un laboratoire dans Azure DevTest Labs. Ce laboratoire est le laboratoire de fabrique d’image où nous créons les machines virtuelles et que vous enregistrez les images personnalisées. Ce laboratoire est considéré comme partie du processus global de la fabrique image. Une fois que vous avez créé un laboratoire, prenez soin d’enregistrer le nom dans la mesure où vous en aurez besoin ultérieurement.

## <a name="scripts-and-templates"></a>Scripts et des modèles
L’étape suivante dans l’adoption de la fabrique d’images pour votre équipe est de comprendre ce qui est disponible. Les scripts de fabrique d’images et les modèles sont disponibles publiquement dans la [DevTest Labs GitHub référentiel](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). Voici une brève présentation des éléments :

- Fabrique d’images. Il est le dossier racine. 
    - Configuration. Les entrées de la fabrique d’images
        - GoldenImages. Ce dossier contient les fichiers JSON qui représentent les définitions des images personnalisées.
        - Labs.JSON. Fichier où les équipes vous inscrire pour recevoir des images personnalisées spécifiques.
- Scripts. Le moteur de la fabrique d’images.

Les articles dans cette section fournissent plus d’informations sur ces scripts et des modèles. 

## <a name="create-an-azure-devops-team-project"></a>Créer un projet d’équipe Azure DevOps
Azure DevOps vous permettent de stocker le code source, exécutez la commande PowerShell Azure au même endroit. Vous pouvez planifier des exécutions périodiques pour maintenir les images à jour. De bonnes options pour les résultats de la journalisation pour diagnostiquer les problèmes.  À l’aide d’Azure DevOps n’est toutefois pas une exigence, vous pouvez utiliser n’importe quel moteur/atelier qui peuvent se connecter à Azure et peut exécuter Azure PowerShell.

Si vous avez un compte DevOps existant ou un projet que vous souhaitez utiliser à la place, ignorez cette étape.

Pour commencer, créez un compte gratuit dans Azure DevOps. Visitez https://www.visualstudio.com/ et sélectionnez **d’évaluation gratuite** juste en dessous **Azure DevOps** (anciennement VSTS). Vous devez choisir un nom de compte unique et veillez à choisir gérer le code à l’aide de Git. Une fois que cela est créé, enregistrez l’URL à votre projet d’équipe. Voici un exemple d’URL : `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>Vérifiez dans la fabrique d’images vers Git
Tous les PowerShell, modèles et configuration pour la fabrique d’images se trouvent dans le [référentiel DevTest Labs GitHub public](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). Le moyen le plus rapide pour obtenir le code dans votre projet d’équipe consiste à importer un dépôt. Cette action extrait dans le référentiel de dev/test entier (et vous obtiendrez donc documentations supplémentaires et les exemples). 

1. Visitez le projet Azure DevOps que vous avez créé à l’étape précédente (URL se présente comme **https :\//\<accountname >.visualstudio.com/MyFirstProject**).
2. Sélectionnez **importer un dépôt**.
3. Entrez le **URL de clone** pour le dépôt de laboratoires DevTest : `https://github.com/Azure/azure-devtestlab`.
4. Sélectionnez **Importer**.

    ![Importation de dépôt Git](./media/set-up-devops-lab/import-git-repo.png)

Si vous décidez de vérifier uniquement dans exactement ce qui est nécessaire (fichiers image factory), suivez les étapes [ici](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) pour cloner le référentiel Git et de transmettre uniquement les fichiers situés dans le **scripts/ImageFactory** directory.

## <a name="create-a-build-and-connect-to-azure"></a>Créer une build et de se connecter à Azure
À ce stade, vous disposez des fichiers source stockés dans un référentiel Git dans Azure DevOps. Maintenant, vous devez configurer un pipeline pour exécuter Azure PowerShell. Il existe un grand nombre d’options permettant d’effectuer ces étapes. Dans cet article, vous utilisez la définition de build par souci de simplicité, mais il fonctionne avec génération de DevOps, mise en production DevOps (un ou plusieurs environnements), les autres moteurs d’exécution comme le Planificateur de tâches Windows ou n’importe quel autre atelier qui peut exécuter Azure PowerShell.

> [!NOTE]
> Un point important à prendre en compte que certains des fichiers PowerShell prennent beaucoup de temps à s’exécuter quand il existe beaucoup (10 +) des images personnalisées pour créer. Agents de Build/mise en production de DevOps hébergé gratuit ont un délai d’expiration de 30 minutes, vous ne pouvez pas utiliser l’agent hébergé gratuitement une fois que vous commencez à créer de nombreuses images. Ce défi de délai d’attente s’applique à quelque atelier vous décidez d’utiliser, il est judicieux de vérifier à l’avance que vous pouvez étendre les délais d’attente par défaut pour les scripts Azure PowerShell en cours d’exécution longue. Dans le cas d’Azure DevOps, vous pouvez utiliser des Agents hébergés payants ou utiliser votre propre agent de build.

1. Pour commencer, sélectionnez **configurer Build** sur la page d’accueil de votre projet DevOps :

    ![Bouton de générer le programme d’installation](./media/set-up-devops-lab/setup-build-button.png)
2. Spécifiez un **nom** pour la build (par exemple : Générer et fournir des Images aux dev/test). 
3. Sélectionnez un **vide** définition de build, puis sélectionnez **appliquer** pour créer votre build. 
4. À ce stade, vous pouvez choisir **hébergé** pour l’agent de build. 
5. **Enregistrer** la définition de build.

    ![Définition de build](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurer les variables de build
Pour simplifier les paramètres de ligne de commande, encapsulent les valeurs de clé qui dirigent la fabrique d’images à un ensemble de variables de build. Sélectionnez le **Variables** onglet et vous verrez une liste de plusieurs variables par défaut. Voici la liste des variables à saisir dans à Azure DevOps :


| Nom de la variable | Valeur | Notes |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Il s’agit du chemin d’accès complet dans le référentiel pour le **Configuration** dossier. Si vous avez importé le dépôt entier ci-dessus, la valeur à gauche est correcte. Sinon, mettez à jour pour pointer vers l’emplacement de Configuration. |
| DevTestLabName | MyImageFactory | Le nom du laboratoire dans Azure DevTest Labs est utilisé en tant que la fabrique pour produire des images. Si vous n’en avez pas, créez-le. Assurez-vous que l’atelier est dans le même abonnement que le point de terminaison de service a accès à. |
| ImageRetention | 1 | Le nombre d’images que vous souhaitez enregistrer de chaque type. Valeur par défaut la valeur 1. |
| MachinePassword | ******* | Mot de passe compte administrateur intégré pour les machines virtuelles. Il s’agit d’un compte temporaire, assurez-vous qu’il est sécurisé. Sélectionnez la petite icône de verrou sur la droite pour vous assurer qu’il est une chaîne sécurisée. |
| MachineUserName | ImageFactoryUser | Le nom d’utilisateur du compte administrateur intégré pour les machines virtuelles. Il s’agit d’un compte temporaire. |
| StandardTimeoutMinutes | 30 | Le délai d’attente que nous choisissions pour les opérations Azure courantes. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | L’ID de l’abonnement où le laboratoire existe et que le point de terminaison de service a accès à. |
| VMSize | Standard_A3 | La taille de la machine virtuelle à utiliser pour le **créer** étape. Les machines virtuelles créées sont temporaires. La taille doit être celui qui est [activé pour le laboratoire](devtest-lab-set-lab-policy.md). Vérifiez qu’il existe suffisamment [quota de cœurs d’abonnement](../azure-subscription-service-limits.md). 

![Les variables de génération](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Connexion à Azure
L’étape suivante consiste à configurer le service principal. Il s’agit d’une identité dans Azure Active Directory qui permet à l’agent de build DevOps de fonctionner dans Azure sur l’utilisateur. Pour configurer, commencez par vous ajouter l’étape de génération du premier Azure PowerShell.

1. Sélectionnez **ajouter une tâche**.
2. Recherchez **Azure PowerShell**. 
3. Une fois que vous trouvez, sélectionnez **ajouter** pour ajouter la tâche à la build. Lorsque vous effectuez cette opération, vous verrez la tâche s’affichent sur le côté gauche comme ajouté.

![Configurer PowerShell étape](./media/set-up-devops-lab/set-up-powershell-step.png)

Le moyen le plus rapide pour configurer un service principal consiste à laisser Azure DevOps à faire pour nous. 

1. Sélectionnez le **tâche** vous venez d’ajouter.
2. Pour **Type de connexion Azure**, choisissez **Azure Resource Manager**. 
3. Sélectionnez le **gérer** lien pour configurer le principal du service. 

Pour plus d’informations, consultez ce [billet de blog](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Lorsque vous sélectionnez le **gérer** lien, vous tombez au bon endroit dans DevOps (deuxième capture d’écran dans le billet de blog) pour configurer la connexion à Azure. Veillez à choisir **point de terminaison de Service Azure Resource Manager** lors de la configuration de cette fonction.

## <a name="complete-the-build-task"></a>Terminer la tâche de génération
Si vous sélectionnez la tâche de génération, vous verrez tous les détails dans le volet droit doit être renseigné. 

1. Tout d’abord, nommez la tâche de génération : **Créer des Machines virtuelles**. 
2. Choisissez le **principal du service** vous avez créé en choisissant **Azure Resource Manager**
3. Choisissez le **point de terminaison de service**. 
4. Pour **chemin d’accès du Script**, sélectionnez **... (points de suspension)**  sur la droite.
5. Accédez à **MakeGoldenImageVMs.ps1** script. 
6. Paramètres de script doivent ressembler à ceci : `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Terminer la définition de build](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>File d’attente la build
Nous allons vérifier que vous avez tout configuré correctement en file d’attente une nouvelle build. Pendant l’exécution de la build, basculez vers le [Azure portal](https://portal.azure.com) et sélectionnez **toutes les Machines virtuelles** dans votre laboratoire de fabrique d’image pour confirmer que tout fonctionne correctement. Vous devez voir trois machines virtuelles sont créés dans le laboratoire.

![Machines virtuelles dans le laboratoire](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Étapes suivantes 
La première étape de configuration de la fabrique d’images basée sur Azure DevTest Labs est terminée. Dans l’article suivant de la série, vous obtenez les machines virtuelles généralisées et enregistrés dans des images personnalisées. Ensuite, vous les avez distribuées à vos autres laboratoires. Consultez l’article suivant de la série : [Enregistrer des images personnalisées et la distribuer à plusieurs laboratoires](image-factory-save-distribute-custom-images.md).
