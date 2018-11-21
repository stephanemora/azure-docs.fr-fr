---
title: Intégration continue et déploiement continu dans Azure IoT Edge | Microsoft Docs
description: Vue d’ensemble de l’intégration continue et du déploiement continu pour Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 06dec64a55aaece4cd67ebf0485e34aa206a8936
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633731"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Intégration continue et déploiement continu dans Azure IoT Edge

Vous pouvez facilement adopter DevOps avec vos applications Azure IoT Edge via [Azure IoT Edge pour Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) ou le [plug-in Azure IoT Edge pour Jenkins](https://plugins.jenkins.io/azure-iot-edge). Cet article montre comment utiliser les fonctionnalités d’intégration continue et de déploiement continu d’Azure Pipelines et de Microsoft TFS (Team Foundation Server) pour générer, tester et déployer des applications rapidement et efficacement sur Azure IoT Edge. 

Dans cet article, vous allez apprendre à :
* Créer et archiver un exemple de solution IoT Edge
* Installez l’extension Azure IoT Edge pour votre compte Azure DevOps.
* Configurer la fonctionnalité CI (intégration continue) pour générer la solution
* Configurer le déploiement continu (CD) pour déployer la solution et afficher les réponses

Comptez 30 minutes pour effectuer les étapes décrites dans cet article.

![CI et CD](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Créer un exemple de solution Azure IoT Edge à l’aide de Visual Studio Code

Dans cette section, vous allez créer un exemple de solution IoT Edge contenant des tests unitaires que vous pouvez exécuter dans le cadre du processus de compilation. Avant de suivre les instructions de cette section, effectuez les étapes décrites dans [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Dans la palette de commandes de VS Code, tapez et exécutez la commande **Azure IoT Edge : New IoT Edge solution** (Azure IoT Edge : nouvelle solution IoT Edge). Ensuite, sélectionnez le dossier de votre espace de travail, fournissez le nom de la solution (le nom par défaut est **EdgeSolution**), et créez un module C# (**FilterModule**) en tant que premier module utilisateur dans cette solution. Vous devez également spécifier le référentiel d’image Docker pour votre premier module. Le référentiel d’image par défaut est basé sur un registre Docker local (`localhost:5000/filtermodule`). Vous devez le remplacer par un registre Azure Container Registry (`<your container registry address>/filtermodule`) ou un Hub Docker pour une intégration continue approfondie.

    ![Configurer ACR](./media/how-to-ci-cd/acr.png)

2. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Vous pouvez éventuellement taper et exécuter **Azure IoT Edge : Add IoT Edge module** (Azure IoT Edge : ajouter un module IoT Edge) pour ajouter d’autres modules. Le dossier racine contient un dossier `modules`, un dossier `.vscode` et un fichier modèle du manifeste de déploiement. L’ensemble des codes du module utilisateur sont des sous-dossiers du dossier `modules`. L’élément `deployment.template.json` est le modèle du manifeste de déploiement. Certains des paramètres de ce fichier sont analysés à partir de l’élément `module.json`, qui existe dans chaque dossier de module.

3. Votre exemple de solution IoT Edge est maintenant prêt. Le module C# par défaut joue le rôle de module de canal de messages. Dans l’élément `deployment.template.json`, vous pouvez voir que cette solution comporte deux modules. Le message sera généré à partir du module `tempSensor`, acheminé directement via `FilterModule`, puis envoyé vers votre IoT hub.

4. Enregistrez ces projets, puis archivez-les dans votre dépôt Azure Repos ou TFS.
    
> [!NOTE]
> Pour plus d’informations sur l’utilisation d’Azure Repos, consultez [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) (Partager votre code avec Visual Studio et Azure Repos).


## <a name="configure-azure-pipelines-for-continuous-integration"></a>Configurer Azure Pipelines pour l’intégration continue
Dans cette section, vous allez créer un pipeline de build configuré pour s’exécuter automatiquement quand vous archivez des changements apportés à l’exemple de solution IoT Edge. Ce pipeline de build affichera les journaux de génération dans Azure Pipelines.

1. Connectez-vous à votre organisation Azure DevOps (**https://dev.azure.com/{your organisation}/**), puis ouvrez le projet où vous avez archivé l’exemple d’application.

    ![Code archivé](./media/how-to-ci-cd/init-project.png)

1. Accédez à [Azure IoT Edge pour Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) sur la Place de marché Azure DevOps. Cliquez sur **Get it free** (Obtenir gratuitement) et suivez les instructions de l’Assistant pour ajouter cette extension à votre organisation Azure DevOps ou la télécharger sur votre serveur TFS.

    ![Installer l’extension](./media/how-to-ci-cd/install-extension.png)

1. Dans votre offre Azure Pipelines, ouvrez le hub **Build & mise en production** puis, sous l’onglet **Builds**, choisissez **+ Nouveau pipeline**. Si vous disposez déjà de pipelines de build, choisissez le bouton **+ Nouveau** à la place.

    ![Nouveau pipeline](./media/how-to-ci-cd/add-new-build.png)

1. Si vous y êtes invité, sélectionnez **Git** pour le type de source. Sélectionnez ensuite le projet, le dépôt et la branche où se trouve votre code. Choisissez **Continuer**.

    ![Sélectionner git](./media/how-to-ci-cd/select-vsts-git.png)

    Dans la fenêtre **Sélectionner un modèle**, cliquez sur **Processus vide**.

    ![Sélectionner un modèle](./media/how-to-ci-cd/start-with-empty.png)

1. Dans l’éditeur de pipeline, choisissez le pool d’agents. 
    
    * Si vous souhaitez générer vos modules dans des conteneurs de plateforme amd64 pour Linux, choisissez **Hosted Ubuntu 1604** (Ubuntu 1604 hébergé)
    * Si vous souhaitez générer vos modules dans des conteneurs de plateforme amd64 pour Windows, choisissez **Hosted VS2017** (VS2017 hébergé) 
    * Si vous souhaitez générer vos modules dans des conteneurs de plateforme arm32v7 pour Linux, vous devez configurer votre propre agent de build en cliquant sur le bouton **Gérer**.
    
    ![Configurer l’agent de build](./media/how-to-ci-cd/configure-env.png)

1. Dans le travail d’agent, cliquez sur « + » pour ajouter deux tâches au pipeline de build. La première est liée à **Azure IoT Edge**. La seconde est liée à la **publication des artefacts de build**
    
    ![Ajouter des tâches](./media/how-to-ci-cd/add-tasks.png)

1. Dans la première tâche **Azure IoT Edge**, remplacez le **Nom d’affichage** par **Module Build and Push**, puis dans la liste déroulante **Action**, sélectionnez **Build and Push** (Générer et envoyer). Dans la zone de texte **Module.json File** (Fichier Module.json), ajoutez le chemin d’accès ci-dessous. Choisissez ensuite **Type de registre de conteneurs**, puis veillez à configurer et sélectionner le même registre que dans votre code (module.json). Cette tâche générera et enverra (push) tous vos modules dans la solution, et effectuera la publication dans le registre de conteneurs que vous avez spécifié. Si vos modules sont destinés à être envoyés (push) à différents registres, vous pouvez avoir plusieurs tâches **Génération et envoi (push) des modules**. Si la solution IoT Edge ne se trouve pas sous la racine de votre dépôt de code, vous pouvez spécifier le **chemin de la racine de la solution Edge** dans la définition de build.
    
    ![Générer et envoyer (push)](./media/how-to-ci-cd/build-and-push.png)

1. Dans la tâche **Publier les artefacts de build**, vous devez spécifier le fichier de déploiement généré par la tâche de build. Affectez la valeur « config/deployment.json » à **Chemin de publication**. Si vous avez défini le **Chemin de la racine de la solution Edge** au cours de la dernière tâche, vous devrez joindre le chemin racine ici. Par exemple, si le chemin de la racine de la solution Edge est « ./edgesolution », le **Chemin de publication** doit être « ./edgesolution/config/deployment.json ». Le fichier `deployment.json` est généré durant le processus de génération. Vous pouvez donc ignorer les lignes d’erreur en rouge dans la zone de texte. 

    ![Publier un artefact](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Ouvrez l’onglet **Déclencheurs** et activez le déclencheur **Intégration continue**. Assurez-vous que la branche qui contient votre code est incluse.

    ![Configurer le déclencheur](./media/how-to-ci-cd/configure-trigger.png)

    Enregistrez le nouveau pipeline de build. Cliquez sur le bouton **Enregistrer** .


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>Configurer Azure Pipelines pour le déploiement continu
Dans cette section, vous allez créer un pipeline de mise en production configuré pour s’exécuter automatiquement quand votre pipeline de build supprime des artefacts. Ce pipeline de build affichera les journaux de déploiement dans Azure Pipelines.

1. Sous l’onglet **Mises en production**, choisissez **+ Nouveau pipeline**. Sinon, si vous avez déjà des pipelines de mise en production, choisissez le bouton **+ Nouveau**.  

    ![Ajouter un pipeline de mise en production](./media/how-to-ci-cd/add-release-pipeline.png)

    Dans la fenêtre **Sélectionner un modèle**, choisissez **Commencer avec un travail vide**.

    ![Commencer avec un travail vide](./media/how-to-ci-cd/start-with-empty-job.png)

2. Le pipeline de mise en production s’initialise ensuite avec une seule phase : **Phase 1**. Renommez la **Phase 1** en **AQ**, puis traitez-la comme un environnement de test. Dans un pipeline de déploiement continu classique, il existe généralement plusieurs phases. Vous pouvez en créer davantage en fonction de votre expérience sur DevOps.

    ![Créer une phase](./media/how-to-ci-cd/QA-env.png)

3. Liez la mise en production aux artefacts de build. Cliquez sur **Ajouter** dans la zone des artefacts.

    ![Ajouter des artefacts](./media/how-to-ci-cd/add-artifacts.png)  
    
    Dans la page **Ajouter un artefact**, choisissez le type de source **Build**. Sélectionnez ensuite le projet et le pipeline de build que vous avez créés. Cliquez ensuite sur **Ajouter**.

    ![Ajouter un artefact](./media/how-to-ci-cd/add-an-artifact.png)

    Ouvrez le déclencheur de déploiement continu pour que la mise en production soit créée chaque fois qu’une nouvelle build est disponible.

    ![Configurer le déclencheur](./media/how-to-ci-cd/add-a-trigger.png)

4. Accédez à **Phase AQ**, puis configurez les tâches de cette phase.

    ![Configurer les tâches AQ (assurance qualité)](./media/how-to-ci-cd/view-stage-tasks.png)

   La tâche de déploiement est indépendante de la plateforme, ce qui signifie que vous pouvez choisir **Hosted VS2017** (VS2017 hébergé) ou **Hosted Ubuntu 1604** (Ubuntu 1604 hébergé) dans le **Pool d’agents** (ou tout autre agent géré par vous-même). Cliquez sur « + » et ajoutez une tâche.

    ![Ajouter des tâches AQ (assurance qualité)](./media/how-to-ci-cd/add-task-qa.png)

5. Dans la tâche Azure IoT Edge, accédez à la liste déroulante **Action**, puis sélectionnez **Deploy to IoT Edge device** (Déployer sur l’appareil IoT Edge). Sélectionnez votre **abonnement Azure**, puis entrez le **nom de votre hub IoT**. Vous pouvez spécifier un **ID de déploiement** IoT Edge ainsi que la **priorité** du déploiement. Vous pouvez également choisir d’effectuer le déploiement sur un ou plusieurs appareils. Si vous effectuez le déploiement sur **plusieurs appareils**, vous devez spécifier la **condition cible** des appareils. La condition cible est un filtre qui correspond à un ensemble d’appareils Edge dans IoT Hub. Pour utiliser les étiquettes des appareils en tant que condition, vous devez mettre à jour les étiquettes d’appareils correspondantes avec le jumeau d’appareil IoT Hub. Supposons que plusieurs appareils IoT Edge aient l’étiquette « qa » (« aq »). La configuration de la tâche doit être identique à celle de la capture d’écran suivante. 

    ![Déployer en AQ (assurance qualité)](./media/how-to-ci-cd/deploy-to-qa.png)

    Enregistrez le nouveau pipeline de mise en production. Cliquez sur le bouton **Enregistrer** . Cliquez ensuite sur **Pipeline** pour revenir au pipeline.

6. La deuxième phase concerne votre environnement de production. Pour ajouter une nouvelle phase « PROD », clonez simplement la phase « QA » (AQ) et renommez la phase clonée en **PROD**,

    ![Cloner la phase](./media/how-to-ci-cd/clone-stage.png)

7. Configurez les tâches pour votre environnement de production. Supposons que plusieurs appareils IoT Edge aient l’étiquette « prod ». Dans les configurations de tâches, mettez à jour la condition cible sur « prod », puis définissez l’ID de déploiement comme étant « deploy-prod » (« déploiement-prod »). Cliquez sur le bouton **Enregistrer** . Cliquez ensuite sur **Pipeline** pour revenir au pipeline.
    
    ![Déployer en production](./media/how-to-ci-cd/deploy-to-prod.png)

7. Notre artefact de build va être déclenché de manière continue à la phase **QA** (AQ), puis à la phase **PROD**. Toutefois, la plupart du temps, vous devez intégrer des cas de test sur les appareils AQ (assurance qualité), et les approuver manuellement. Ils seront déployés plus tard sur l’environnement PROD. Configurez une approbation à la phase PROD, comme suit.

    1. Ouvrez le volet des paramètres de **Conditions de prédéploiement**.

        ![Ouvrir les conditions de prédéploiement](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Définissez **Activé** dans **Approbations de prédéploiement**. Renseignez l’entrée **Approbateurs**. Cliquez ensuite sur **Enregistrer**.
    
        ![Définir des conditions](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Votre pipeline de mise en production est configuré comme suit.

    ![Pipeline de mise en production](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Vérifier les fonctionnalités CI/CD IoT Edge avec les pipelines de build et de mise en production

Dans cette section, vous allez déclencher une build pour faire fonctionner le pipeline CI/CD. Vérifiez ensuite la réussite du déploiement.

1. Pour déclencher un travail de build, envoyez (push) une validation dans le dépôt de code source, ou déclenchez-le manuellement. Vous pouvez déclencher un travail de build dans votre pipeline de build en cliquant sur le bouton **File d’attente**, comme indiqué dans la capture d’écran suivante.

    ![Déclencheur manuel](./media/how-to-ci-cd/manual-trigger.png)

2. Si le pipeline de build s’achève correctement, il déclenche une mise en production sur la phase **QA** (AQ). Accédez aux journaux de pipeline de build pour voir ce qui suit.

    ![Journaux de génération](./media/how-to-ci-cd/build-logs.png)

3. Le déploiement réussi à la phase **QA** déclenche l’envoi d’une notification à l’approbateur. Accédez au pipeline de mise en production pour voir ce qui suit. 

    ![Approbation en attente](./media/how-to-ci-cd/pending-approval.png)


4. Une fois que l’approbateur a approuvé ce changement, il peut être déployé sur **PROD**.

    ![Déployer en production](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le déploiement IoT Edge, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle](module-deployment-monitoring.md).
* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
