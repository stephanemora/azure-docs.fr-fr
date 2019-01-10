---
title: Intégration continue et déploiement continu - Azure IoT Edge | Microsoft Docs
description: Configurer l’intégration continue et le déploiement continu - Azure IoT Edge avec Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c4f5d6888d581cb44702a8d76e1ebbb13845091
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582913"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Intégration continue et déploiement continu dans Azure IoT Edge

Vous pouvez facilement adopter DevOps avec vos applications Azure IoT Edge via les tâches Azure IoT Edge intégrées dans Azure Pipelines ou le [plug-in Azure IoT Edge pour Jenkins](https://plugins.jenkins.io/azure-iot-edge) sur votre serveur Jenkins. Cet article montre comment utiliser les fonctionnalités d’intégration continue et de déploiement continu d’Azure Pipelines pour générer, tester et déployer des applications rapidement et efficacement sur Azure IoT Edge. 

Dans cet article, vous allez apprendre à :
* Créer et archiver un exemple de solution IoT Edge
* Configurer la fonctionnalité CI (intégration continue) pour générer la solution
* Configurer le déploiement continu (CD) pour déployer la solution et afficher les réponses

![Diagramme - Branches CI et CD pour le développement et la production](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Créer un exemple de solution Azure IoT Edge à l’aide de Visual Studio Code

Dans cette section, vous allez créer un exemple de solution IoT Edge contenant des tests unitaires que vous pouvez exécuter dans le cadre du processus de compilation. Avant de suivre les instructions de cette section, effectuez les étapes décrites dans [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](how-to-develop-multiple-modules-vscode.md).

1. Dans la palette de commandes de VS Code, tapez et exécutez la commande **Azure IoT Edge : New IoT Edge solution**. Ensuite, sélectionnez le dossier de votre espace de travail, fournissez le nom de la solution (le nom par défaut est **EdgeSolution**), et créez un module C# (**FilterModule**) en tant que premier module utilisateur dans cette solution. Vous devez également spécifier le référentiel d’image Docker pour votre premier module. Le référentiel d’image par défaut est basé sur un registre Docker local (`localhost:5000/filtermodule`). Remplacez-le par un registre Azure Container Registry (`<your container registry address>/filtermodule`) ou Docker Hub pour une intégration continue approfondie.

    ![Configurer Azure Container Registry](./media/how-to-ci-cd/acr.png)

2. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Vous pouvez aussi taper et exécuter **Azure IoT Edge : Add IoT Edge module** pour ajouter d’autres modules. Le dossier racine contient un dossier `modules`, un dossier `.vscode` et un fichier modèle du manifeste de déploiement. L’ensemble des codes du module utilisateur sont des sous-dossiers du dossier `modules`. L’élément `deployment.template.json` est le modèle du manifeste de déploiement. Certains des paramètres de ce fichier sont analysés à partir de l’élément `module.json`, qui existe dans chaque dossier de module.

3. Votre exemple de solution IoT Edge est maintenant prêt. Le module C# par défaut joue le rôle de module de canal de messages. Dans l’élément `deployment.template.json`, vous pouvez voir que cette solution comporte deux modules. Le message sera généré à partir du module `tempSensor`, acheminé directement via `FilterModule`, puis envoyé vers votre IoT hub.

4. Enregistrez ces projets, puis effectuez la validation dans votre dépôt Azure Repos.
    
> [!NOTE]
> Pour plus d’informations sur l’utilisation d’Azure Repos, consultez [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) (Partager votre code avec Visual Studio et Azure Repos).


## <a name="configure-azure-pipelines-for-continuous-integration"></a>Configurer Azure Pipelines pour l’intégration continue
Dans cette section, vous allez créer un pipeline de build configuré pour s’exécuter automatiquement quand vous archivez des changements apportés à l’exemple de solution IoT Edge. Ce pipeline de build affichera les journaux de génération dans Azure Pipelines.

1. Connectez-vous à votre organisation Azure DevOps (**https://dev.azure.com/{your organisation}/**), puis ouvrez le projet où vous avez archivé l’exemple d’application.

    ![Archiver du code dans Azure Pipelines](./media/how-to-ci-cd/init-project.png)

1. Dans votre offre Azure Pipelines, ouvrez l’onglet **Builds**, choisissez **+ Nouveau pipeline**. Si vous disposez déjà de pipelines de build, choisissez le bouton **+ Nouveau** à la place. Sélectionnez ensuite **Nouveau pipeline de build**.

    ![Créer un pipeline de build](./media/how-to-ci-cd/add-new-build.png)

1. Si vous y êtes invité, sélectionnez les dépôts Azure pour votre source. Sélectionnez ensuite le projet, le dépôt et la branche où se trouve votre code. Choisissez **Continuer**.

    ![Sélectionner Azure Repos Git](./media/how-to-ci-cd/select-vsts-git.png)

    Dans la fenêtre **Sélectionner un modèle**, cliquez sur **Processus vide**.

    ![Démarrer avec un projet vide](./media/how-to-ci-cd/start-with-empty.png)

1. Dans l’éditeur de pipeline, choisissez le pool d’agents. 
    
    * Si vous souhaitez générer vos modules dans des conteneurs de plateforme amd64 pour Linux, choisissez **Hosted Ubuntu 1604** (Ubuntu 1604 hébergé)
    * Si vous souhaitez générer vos modules dans des conteneurs de plateforme amd64 pour Windows, choisissez **Hosted VS2017** (VS2017 hébergé) 
    * Si vous souhaitez générer vos modules dans des conteneurs de plateforme arm32v7 pour Linux, vous devez configurer votre propre agent de build en cliquant sur le bouton **Gérer**.
    
    ![Configurer le pool d’agents de build](./media/how-to-ci-cd/configure-env.png)

1. Dans le travail d’agent, ouvrez l’option « + » pour ajouter trois tâches au pipeline de build. Les deux premières sont liées à **Azure IoT Edge**. La troisième est liée à la **publication des artefacts de build**
    
    ![Ajouter des tâches au pipeline de génération](./media/how-to-ci-cd/add-tasks.png)

1. Dans la première tâche **Azure IoT Edge**, remplacez le **Nom d’affichage** par **Azure IoT Edge - Build module images** (Générer des images de modules) puis, dans la liste déroulante **Action**, sélectionnez **Générer des images de modules**. Dans le contrôle **.template.json file** (Fichier .template.json), sélectionnez le fichier **deployment.template.json** qui décrit votre solution IoT Edge. Choisissez ensuite **Plateforme par défaut**, veillez à sélectionner la même plateforme que votre appareil IoT Edge. Cette tâche génère tous les modules dans la solution avec la plateforme cible que vous avez spécifiée. Générez également le fichier **deployment.json** ; vous pouvez trouver le chemin du fichier dans Variables de sortie. Affectez la valeur `edge` à l’alias pour cette variable.
    
    ![Configurer la tâche de génération d’images de module](./media/how-to-ci-cd/build-and-push.png)

1. Dans la seconde tâche **Azure IoT Edge**, remplacez le **Nom d’affichage** par **Azure IoT Edge - Push module images** (Envoyer [push] des images de modules) puis, dans la liste déroulante **Action**, sélectionnez **Envoyer (push) des images de modules**. Choisissez Type de registre de conteneurs, veillez à configurer et sélectionner le même registre que dans votre code (module.json). Dans le contrôle **.template.json file** (Fichier .template.json), sélectionnez le fichier **deployment.template.json** qui décrit votre solution IoT Edge. Choisissez ensuite **Plateforme par défaut**, veillez à sélectionner la même plateforme pour vos images de modules générées. Cette tâche envoie toutes les images de modules dans le registre de conteneurs que vous avez sélectionné. Ajoutez également les informations d’identification du registre de conteneurs dans le fichier **deployment.json** ; vous pouvez trouver le chemin du fichier dans Variables de sortie. Affectez la valeur `edge` à l’alias pour cette variable. Si vous avez plusieurs registres de conteneurs pour héberger vos images de modules, vous devez dupliquer cette tâche, sélectionner un autre registre de conteneurs et utiliser **Bypass module(s)** (Ignorer les modules) dans les paramètres avancés pour ignorer les images qui ne concernent pas ce registre spécifique.

    ![Configurer la tâche d’envoi (push) d’images de module](./media/how-to-ci-cd/push.png)

1. Dans la tâche **Publier les artefacts de build**, vous devez spécifier le fichier de déploiement généré par la tâche de build. Affectez la valeur `$(edge.DEPLOYMENT_FILE_PATH)` à **Chemin de publication**.

    ![Configurer la tâche de publication d’artefact](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Ouvrez l’onglet **Déclencheurs** et activez le déclencheur **Intégration continue**. Assurez-vous que la branche qui contient votre code est incluse.

    ![Activer le déclencheur d’intégration continue](./media/how-to-ci-cd/configure-trigger.png)

    Enregistrez le nouveau pipeline de build avec le bouton **Enregistrer**.


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>Configurer Azure Pipelines pour le déploiement continu
Dans cette section, vous allez créer un pipeline de mise en production configuré pour s’exécuter automatiquement quand votre pipeline de build supprime des artefacts. Ce pipeline de build affichera les journaux de déploiement dans Azure Pipelines.

1. Sous l’onglet **Mises en production**, choisissez **+ Nouveau pipeline**. Sinon, si vous avez déjà des pipelines de mise en production, choisissez le bouton **+ Nouveau**, puis sélectionnez **+ Nouveau pipeline de mise en production**.  

    ![Ajouter un pipeline de mise en production](./media/how-to-ci-cd/add-release-pipeline.png)

    Dans la fenêtre **Sélectionner un modèle**, choisissez **Commencer avec un travail vide**.

    ![Démarrer par une tâche vide](./media/how-to-ci-cd/start-with-empty-job.png)

2. Le pipeline de mise en production s’initialise ensuite avec une seule phase : **Phase 1**. Renommez la **Phase 1** en **AQ**, puis traitez-la comme un environnement de test. Dans un pipeline de déploiement continu classique, il existe généralement plusieurs phases. Vous pouvez en créer davantage en fonction de votre expérience sur DevOps.

    ![Phase Créer l’environnement de test](./media/how-to-ci-cd/QA-env.png)

3. Liez la mise en production aux artefacts de build. Cliquez sur **Ajouter** dans la zone des artefacts.

    ![Ajouter des artefacts](./media/how-to-ci-cd/add-artifacts.png)  
    
    Dans la page **Ajouter un artefact**, choisissez le type de source **Build**. Sélectionnez ensuite le projet et le pipeline de build que vous avez créés. Sélectionnez ensuite **Ajouter**.

    ![Ajouter un artefact de build](./media/how-to-ci-cd/add-an-artifact.png)

    Ouvrez le déclencheur de déploiement continu pour que la mise en production soit créée chaque fois qu’une nouvelle build est disponible.

    ![Configurer le déclencheur de déploiement continu](./media/how-to-ci-cd/add-a-trigger.png)

4. Accédez à **Phase AQ**, puis configurez les tâches de cette phase.

    ![Configurer les tâches AQ (assurance qualité)](./media/how-to-ci-cd/view-stage-tasks.png)

   La tâche de déploiement est indépendante de la plateforme, ce qui signifie que vous pouvez choisir **Hosted VS2017** (VS2017 hébergé) ou **Hosted Ubuntu 1604** (Ubuntu 1604 hébergé) dans le **Pool d’agents** (ou tout autre agent géré par vous-même). Sélectionnez « + », puis ajoutez une tâche.

    ![Ajouter des tâches AQ (assurance qualité)](./media/how-to-ci-cd/add-task-qa.png)

5. Dans la tâche Azure IoT Edge, accédez à la liste déroulante **Action**, puis sélectionnez **Deploy to IoT Edge device** (Déployer sur l’appareil IoT Edge). Sélectionnez votre **abonnement Azure**, puis entrez le **nom de votre hub IoT**. Vous pouvez choisir d’effectuer le déploiement sur un ou plusieurs appareils. Si vous effectuez le déploiement sur **plusieurs appareils**, vous devez spécifier la **condition cible** des appareils. La condition cible est un filtre qui correspond à un ensemble d’appareils Edge dans IoT Hub. Pour utiliser les étiquettes des appareils en tant que condition, vous devez mettre à jour les étiquettes d’appareils correspondantes avec le jumeau d’appareil IoT Hub. Remplacez **ID de déploiement IoT Edge** par « deploy-qa » dans les paramètres avancés. Supposons que plusieurs appareils IoT Edge aient l’étiquette « qa » (« aq »). La configuration de la tâche doit être identique à celle de la capture d’écran suivante. 

    ![Déployer en AQ (assurance qualité)](./media/how-to-ci-cd/deploy-to-qa.png)

    Enregistrer le nouveau pipeline de mise en production avec le bouton **Enregistrer**. Sélectionnez ensuite **Pipeline** pour revenir au pipeline.

6. La deuxième phase concerne votre environnement de production. Pour ajouter une nouvelle phase « PROD », clonez la phase « QA » et renommez la phase clonée en **PROD**,

    ![Cloner la phase](./media/how-to-ci-cd/clone-stage.png)

7. Configurez les tâches pour votre environnement de production. Supposons que plusieurs appareils IoT Edge aient l’étiquette « prod ». Dans les configurations de tâches, mettez à jour la condition cible sur « prod », puis définissez l’ID de déploiement comme étant « deploy-prod » dans les paramètres avancés. Enregistrez-le avec le bouton **Enregistrer**. Sélectionnez ensuite **Pipeline** pour revenir au pipeline.
    
    ![Déployer en production](./media/how-to-ci-cd/deploy-to-prod.png)

7. Notre artefact de build va être déclenché de manière continue à la phase **QA** (AQ), puis à la phase **PROD**. Toutefois, la plupart du temps, vous devez intégrer des cas de test sur les appareils AQ (assurance qualité), et les approuver manuellement. Ils seront déployés plus tard sur l’environnement PROD. Configurez une approbation à la phase PROD, comme dans la capture d’écran suivante.

    1. Ouvrez le volet des paramètres de **Conditions de prédéploiement**.

        ![Ouvrir les conditions de prédéploiement](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Définissez **Activé** dans **Approbations de prédéploiement**. Renseignez l’entrée **Approbateurs**. Enregistrez-le avec le bouton **Enregistrer**.
    
        ![Définir des conditions](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Votre pipeline de mise en production est configuré comme dans la capture d’écran suivante.

    ![Pipeline de mise en production](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Vérifier les fonctionnalités CI/CD IoT Edge avec les pipelines de build et de mise en production

Dans cette section, vous allez déclencher une build pour faire fonctionner le pipeline CI/CD. Vérifiez ensuite la réussite du déploiement.

1. Pour déclencher un travail de build, envoyez (push) une validation dans le dépôt de code source, ou déclenchez-le manuellement. Vous pouvez déclencher un travail de build dans votre pipeline de build en sélectionnant le bouton **File d’attente**, comme indiqué dans la capture d’écran suivante.

    ![Déclencheur manuel](./media/how-to-ci-cd/manual-trigger.png)

2. Si le pipeline de build s’achève correctement, il déclenche une mise en production sur la phase **QA** (AQ). Accédez aux journaux de pipeline de build pour voir la capture d’écran suivante.

    ![Journaux de génération](./media/how-to-ci-cd/build-logs.png)

3. Le déploiement réussi à la phase **QA** déclenche l’envoi d’une notification à l’approbateur. Accédez au pipeline de mise en production pour voir la capture d’écran suivante. 

    ![Approbation en attente](./media/how-to-ci-cd/pending-approval.png)


4. Une fois que l’approbateur a approuvé ce changement, il peut être déployé sur **PROD**.

    ![Déployer en production](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le déploiement IoT Edge, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle](module-deployment-monitoring.md).
* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
