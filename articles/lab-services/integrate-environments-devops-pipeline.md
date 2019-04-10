---
title: Intégrer des environnements dans des Pipelines de Azure dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment intégrer des environnements d’Azure DevTest Labs dans votre Azure DevOps, intégration continue (CI) et les pipelines de livraison continue (CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357392"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Intégrer des environnements à vos pipelines Azure DevOps CI/CD
Vous pouvez utiliser l’extension Azure DevTest Labs Tasks qui est installée dans les Services Azure DevOps (anciennement Visual Studio Team Services) pour intégrer facilement votre intégration continue (CI) / pipeline de livraison continue (CD) build-et-mise en production avec Azure Dev/test. Ces extensions rendent plus facile à déployer rapidement un [environnement](devtest-lab-test-env.md) pour une tâche de test et spécifique puis supprimez-le une fois le test terminé. 

Cet article explique comment créer et déployer un environnement, puis supprimer l’environnement, toutes dans un pipeline complet. Vous exécuterait normalement chacune de ces tâches individuellement dans votre pipeline de build-test-déploiement personnalisé. Les extensions utilisées dans cet article sont ajoutent ces [créer/supprimer des tâches de DTL VM](devtest-lab-integrate-ci-cd-vsts.md):

- Créer un environnement
- Supprimer un environnement

## <a name="before-you-begin"></a>Avant de commencer
Avant de pouvoir intégrer votre pipeline CI/CD avec Azure DevTest Labs, installer [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) extension à partir de Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Créer et configurer le laboratoire pour les environnements
Cette section décrit comment créer et configurer un laboratoire où l’environnement Azure est déployé sur.

1. [Créer un laboratoire](devtest-lab-create-lab.md) si vous n’en avez pas déjà. 
2. Configurer le laboratoire et créer un modèle d’environnement en suivant les instructions de cet article : [Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec des modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Pour cet exemple, utilisez un modèle de démarrage rapide Azure existant [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Copie le **201-web-app-redis-cache-sql-database** dossier vers le **ArmTemplate** dossier dans le référentiel configuré à l’étape 2.

## <a name="create-a-release-definition"></a>Création d’une définition de version
Pour créer la définition de mise en production, effectuez les étapes suivantes :

1.  Sur le **versions** onglet de la **hub de Build et mise en production**, sélectionnez le **signe plus (+)** bouton.
2.  Dans la fenêtre **Créer une définition de mise en production**, sélectionnez le modèle **Vide**, puis sélectionnez **Suivant**.
3.  Sélectionnez **Choisir plus tard**, puis sélectionnez **Créer** pour créer une définition de mise en production avec un environnement par défaut et aucun artefact lié.
4.  Pour ouvrir le menu contextuel, dans la nouvelle définition de mise en production, sélectionnez le **points de suspension (...)**  suivant pour le nom de l’environnement et sélectionnez **configurer les variables**.
5.  Dans la fenêtre **Configurer l’environnement**, pour les variables que vous utilisez dans les tâches de définition de mise en production, entrez les valeurs suivantes :
1.  Pour **administratorLogin**, entrez le nom de connexion d’administrateur SQL.
2.  Pour **administratorLoginPassword**, entrez le mot de passe à utiliser par la connexion d’administrateur SQL. Utilisez l’icône en forme de cadenas pour masquer et sécuriser le mot de passe.
3.  Pour **databaseName**, entrez le nom de la base de données SQL.
4.  Ces variables sont spécifiques pour les environnements d’exemple, les environnements différents peuvent avoir différentes variables.

## <a name="create-an-environment"></a>Créer un environnement
L’étape suivante du déploiement consiste à créer l’environnement à utiliser pour le développement ou à des fins de tests.

1. Dans la définition de mise en production, sélectionnez **Ajouter des tâches**.
2. Sur le **tâches** onglet, ajoutez une tâche d’Azure DevTest Labs créer un environnement. Configurez la tâche comme indiqué ci-dessous :
    1. Pour **Abonnement RM Azure**, sélectionnez une connexion dans la liste **Connexions au service Azure disponibles**, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. Pour **nom Lab**, sélectionnez le nom de l’instance que vous avez créé précédemment *.
3. Pour **nom du référentiel**, sélectionnez le référentiel dans lequel le modèle Resource Manager (201) a été envoyé à *.
4. Pour **nom du modèle**, sélectionnez le nom de l’environnement que vous avez enregistré dans votre référentiel de code source *. 
5. Le **nom Lab**, **nom du référentiel**, et **nom du modèle** sont les représentations sous forme conviviales de l’ID de ressource Azure. Entrer manuellement le nom convivial pour provoquer des erreurs, utilisez les listes déroulantes pour sélectionner les informations.
6. Pour **nom de l’environnement**, entrez un nom pour identifier de façon unique l’instance de l’environnement du laboratoire.  Il doit être unique dans le laboratoire.
7. Le **fichier de paramètres** et **paramètres**, autoriser des paramètres personnalisés à passer à l’environnement. Une ou les deux peuvent être utilisés pour définir les valeurs de paramètre. Pour cet exemple, la section Parameters servira. Utilisez les noms des variables que vous avez définies dans l’environnement, par exemple : `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Pour plus d’informations dans le modèle d’environnement peuvent être transmises dans la section de sortie du modèle. Vérifiez **créer les variables de sortie en fonction de la sortie de modèle d’environnement** afin d’autres tâches peuvent utiliser les données. `$(Reference name.Output Name)` est le modèle à suivre. Par exemple, si le nom de référence a été DTL et le nom de sortie dans le modèle a été l’emplacement de la variable serait `$(DTL.location)`.

## <a name="delete-the-environment"></a>Supprimer l’environnement
L’étape finale consiste à supprimer l’environnement que vous avez déployé dans votre instance Azure DevTest Labs. Vous normalement, vous supprimez l’environnement une fois que vous exécutez les tâches de développement ou exécutez les tests dont vous avez besoin sur les ressources déployées.

Dans la définition de version, sélectionnez **ajouter des tâches**, puis, dans le **déployer** onglet, ajoutez un **Azure DevTest Labs supprimer environnement** tâche. Configurez-le comme suit :

1. Pour supprimer la machine virtuelle, consultez [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Pour **Abonnement RM Azure**, sélectionnez une connexion dans la liste **Connexions au service Azure disponibles**, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Pour **nom Lab**, sélectionnez le laboratoire dans lequel l’environnement existe.
    3. Pour **nom de l’environnement**, entrez le nom de l’environnement à supprimer.
2. Entrez un nom pour la définition de mise en production, puis enregistrez-le.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 
- [Créer des environnements de plusieurs machines virtuelles avec modèles Resource Manager](devtest-lab-create-environment-from-arm.md).
- Modèles de démarrage rapide Resource Manager pour l’automatisation de DevTest Labs à partir de la [référentiel DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- [Page Résolution des problèmes de VSTS](/azure/devops/pipelines/troubleshooting)

