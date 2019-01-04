---
title: Pipeline CI/CD avec Azure DevOps Projects - Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects facilite la prise en main d’Azure. Il vous aide à lancer une application Azure IoT Edge de votre choix en quelques étapes rapides.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074212"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Créer un pipeline CI/CD pour IoT Edge avec Azure DevOps Projects (préversion)

Configurez l’intégration continue (CI) et la livraison continue (CD) pour votre application IoT Edge avec Azure DevOps Projects. DevOps Projects simplifie la configuration initiale d’un pipeline de build et de mise en production dans Azure Pipelines.

Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Projects crée un pipeline CI/CD dans Azure DevOps. Vous pouvez créer une organisation Azure DevOps ou utiliser une organisation existante. DevOps Projects crée également des ressources Azure dans l’abonnement Azure de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Dans le volet gauche, sélectionnez l’icône **Créer une ressource** dans la barre de navigation gauche, puis recherchez **DevOps Projects**.  

1.  Sélectionnez **Créer**.

## <a name="select-a-sample-application-and-azure-service"></a>Sélectionner un exemple d’application et le service Azure

1. Vos modules Azure IoT Edge peuvent être écrits en [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) et [Java](tutorial-java-module.md). Sélectionnez votre langage préféré pour démarrer une nouvelle application. Vous pouvez sélectionner **.NET**, **Node.js**, **Python**, **C** ou **Java**, puis cliquez sur **Suivant**.

    ![Sélectionner le langage pour créer une application](./media/how-to-devops-project/select-language.png)

2. Sélectionnez **IoT simple (préversion)**, puis cliquez sur **Suivant**.

    ![Sélectionner le framework IoT simple](media/how-to-devops-project/select-iot.png)

3. Sélectionnez **IoT Edge**, puis cliquez sur **Suivant**.

    ![Sélectionner le service IoT Edge](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurer Azure DevOps et un abonnement Azure

1. Créez gratuitement une organisation Azure DevOps ou choisissez une organisation existante.

    a. Choisissez un nom pour votre projet. 

    b. Sélectionnez votre abonnement et un emplacement Azure, choisissez un nom pour votre application, puis sélectionnez **Terminé**.  

    ![Nommer et créer l’application](media/how-to-devops-project/select-devops.png)

1. Après quelques minutes, le tableau de bord DevOps Projects s’affiche dans le portail Azure. Un exemple d’application IoT Edge est configuré dans un dépôt de votre organisation Azure DevOps, une build est exécutée et votre application est déployée sur l’appareil IoT Edge. Ce tableau de bord donne une visibilité sur votre dépôt de code, le pipeline CI/CD et votre application dans Azure.

    ![Afficher l’application dans le portail DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

DevOps Projects crée un dépôt Git dans Azure Repos ou dans GitHub. Pour voir le dépôt et changer le code de votre application, effectuez les étapes suivantes :

1. À gauche du tableau de bord DevOps Projects, sélectionnez le lien correspondant à votre branche **master**.  
Ce lien ouvre une vue sur le référentiel Git récemment créé.

1. Pour afficher l’URL du clone du référentiel, sélectionnez **Clone** en haut à droite du navigateur. Vous pouvez cloner votre dépôt Git dans VS Code ou d’autres outils préférés. Dans les prochaines étapes, vous allez utiliser le navigateur web pour effectuer des changements de code directement dans la branche master et les valider.

    ![Cloner le dépôt Git](media/how-to-devops-project/clone.png)

1. Sur la gauche du navigateur, accédez au fichier **modules/FilterModule/module.json**.

1. Sélectionnez **Modifier**, puis modifiez `"version"` sous `"tag"`. Par exemple, vous pouvez la changer en `"version": "${BUILD_BUILDID}"` pour utiliser des [variables de build Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) dans le cadre de votre étiquette d’image de module Azure IoT Edge.

    ![Modifier la version de façon à accepter des variables de build](media/how-to-devops-project/update-module-json.png)

1. Sélectionnez **Valider**, puis enregistrez vos modifications.

1. Dans le navigateur, accédez au tableau de bord Azure DevOps Projects.  Vous devez maintenant voir qu’une build est en cours. Les modifications que vous avez apportées font automatiquement l’objet d’une build et d’un déploiement via un pipeline CI/CD.

    ![Afficher l’état en cours](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Examiner le pipeline CI/CD

Dans l’étape précédente, Azure DevOps Projects a configuré automatiquement un pipeline CI/CD complet pour votre application IoT Edge. Explorez et personnalisez le pipeline selon vos besoins. Pour vous familiariser avec les pipelines de build et de mise en production d’Azure DevOps, effectuez les étapes suivantes.

1. En haut du tableau de bord DevOps Projects, sélectionnez **Pipelines de build**.  
Ce lien ouvre un onglet du navigateur et le pipeline de build Azure DevOps pour votre nouveau projet.

1. Sélectionnez **Modifier**.

    ![Modifier le pipeline de build](media/how-to-devops-project/click-edit-button.png)

1. Dans ce volet, vous pouvez examiner les différentes tâches de votre pipeline de build. La build effectue différentes tâches, comme la récupération (fetch) de sources dans le dépôt Git, la génération d’images de module IoT Edge, la publication de modules IoT Edge et la publication des sorties utilisées pour les déploiements. Pour plus d’informations sur les tâches Azure IoT Edge pour l’intégration continue, vous pouvez visiter [Configurer Azure Pipelines pour l’intégration continue](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration).

    ![Afficher les tâches d’intégration continue](media/how-to-devops-project/ci.png)

1. En haut du pipeline de build, sélectionnez le nom du pipeline de build.

1. Remplacez le nom de votre pipeline de build par un nom plus descriptif, sélectionnez **Enregistrer et mettre en file d’attente**, puis sélectionnez **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.   
Dans le volet **Historique**, vous pouvez voir une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure Pipelines gère les modifications apportées au pipeline de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**. DevOps Projects a créé automatiquement un déclencheur CI, et chaque validation dans le dépôt lance une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**. En fonction de votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Mise en production** sous **Pipelines**. DevOps Projects crée un pipeline de mise en production pour gérer les déploiements sur Azure IoT Edge.

    ![Afficher le pipeline de mise en production](media/how-to-devops-project/release-pipeline.png)

1. Sélectionnez **Modifier**. Le pipeline de mise en production contient un pipeline qui définit le processus de mise en production.  

1. Sous **Artefacts**, sélectionnez **Déposer**. Le pipeline de build que vous avez examiné aux étapes précédentes génère la sortie utilisée pour l’artefact. 

1. En regard de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  
Ce pipeline de mise en production a un déclencheur CD activé, qui effectue un déploiement chaque fois qu’un nouvel artefact de build est disponible. Si vous le souhaitez, vous pouvez désactiver le déclencheur, vos déploiements nécessitant alors une exécution manuelle.  

1. Sur la gauche, sélectionnez **Tâches**. Les tâches sont les activités effectuées par votre processus de déploiement. Dans cet exemple, une tâche a été créée pour déployer vos images de module sur Azure IoT Edge. Pour plus d’informations sur les tâches Azure IoT Edge pour le déploiement continu, vous pouvez visiter [Configurer Azure Pipelines pour le déploiement continu](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment).

    ![Afficher les tâches de déploiement continu](media/how-to-devops-project/dev-release.png)

1. Sur la droite, sélectionnez **Afficher les mises en production**. Cette vue affiche un historique des mises en production.

1. Sélectionnez les points de suspension (...) en regard d’une de vos mises en production, puis sélectionnez **Ouvrir**.  
Il y a plusieurs menus à explorer, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**. Cette vue montre les validations de code associées au déploiement spécifique. 

1. Sélectionnez **Journaux**. Les journaux contiennent des informations utiles sur le processus de déploiement. Ils peuvent être affichés pendant et après les déploiements.


## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer Azure App Service et les autres ressources associées que vous avez créées. Utilisez la fonctionnalité **Supprimer** du tableau de bord DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus d’informations sur les tâches pour Azure IoT Edge sur Azure DevOps dans [Intégration continue et déploiement continu sur Azure IoT Edge](how-to-ci-cd.md)
* Pour plus d’informations sur le déploiement IoT Edge, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle](module-deployment-monitoring.md).
* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
