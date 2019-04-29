---
title: Intégration continue et déploiement continu - Azure IoT Edge | Microsoft Docs
description: Configurer l’intégration continue et le déploiement continu - Azure IoT Edge avec Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f449449c542ce6ac04daa58ff37a3577f0d75aee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61221850"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Intégration continue et déploiement continu dans Azure IoT Edge

Vous pouvez facilement adopter DevOps avec vos applications Azure IoT Edge via les tâches Azure IoT Edge intégrées dans Azure Pipelines. Cet article montre comment utiliser les fonctionnalités d’intégration continue et de déploiement continu d’Azure Pipelines pour générer, tester et déployer des applications rapidement et efficacement sur Azure IoT Edge. 

Dans cet article, vous allez apprendre à utiliser les tâches Azure IoT Edge intégrées pour Azure Pipelines afin de créer deux pipelines destinés à votre solution IoT Edge. Le premier utilise votre code et crée la solution, en envoyant (push) vos images de module vers votre registre de conteneurs et en créant un manifeste de déploiement. Le second déploie vos modules vers les appareils IoT Edge ciblés.  

![Diagramme - Branches CI et CD pour le développement et la production](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Conditions préalables

* Référentiel Azure Repos. Si vous n’en avez pas, vous pouvez [créer un référentiel Git dans votre projet](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Solution IoT Edge validée et envoyée (push) vers votre référentiel. Si vous souhaitez créer un exemple de solution pour tester cet article, suivez la procédure décrite dans [Développer et déboguer des modules dans Visual Studio Code](how-to-vs-code-develop-module.md) ou [Développer et déboguer des modules C# dans Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Pour cet article, vous avez uniquement besoin du dossier de solution créé par les modèles IoT Edge dans Visual Studio Code ou Visual Studio. Vous n'avez pas à créer, envoyer (push), déployer ou déboguer ce code avant de continuer. Vous configurerez ces processus dans Azure Pipelines. 
   * Si vous créez une solution, commencez par cloner votre référentiel localement. Ensuite, lorsque vous créez la solution, vous pouvez choisir de le faire directement dans le dossier du référentiel. De là, vous pouvez facilement valider et envoyer (push) les nouveaux fichiers. 
* Registre de conteneurs vers lequel envoyer (push) vos images de module. Vous pouvez utiliser [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou un registre tiers. 
* [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) avec appareils IoT Edge à des fins de test distinct des phases de test et de déploiement de production. Vous pouvez suivre les articles de démarrage rapide pour créer un appareil IoT Edge sur [Linux](quickstart-linux.md) ou [Windows](quickstart.md).


Pour plus d’informations sur l’utilisation d’Azure Repos, consultez [Partager votre code avec Visual Studio et Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).

## <a name="configure-continuous-integration"></a>Configurer l’intégration continue
Dans cette section, vous créez un pipeline de build. Configurez le pipeline de manière à ce qu’il s’exécute automatiquement lorsque vous archivez des modifications dans l’exemple de solution IoT Edge et publiez des journaux d’activité de génération.

>[!NOTE]
>Cet article utilise le concepteur visuel Azure DevOps. Avant de suivre les étapes décrites dans cette section, désactivez la fonctionnalité d’évaluation de la nouvelle expérience de création de pipeline YAML. 
>1. Dans Azure, DevOps, sélectionnez l’icône de votre profil, puis **Fonctionnalités d'évaluation**.
>2. Désactivez **Nouvelle expérience de création de pipeline YAML**. 
>
>Pour plus d’informations, consultez [Créer un pipeline de build](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Connectez-vous à votre organisation d’Azure DevOps (**https :\//dev.azure.com/{your organisation} /**) et ouvrez le projet qui contient votre référentiel de la solution IoT Edge.

   Pour cet article, nous avons créé un référentiel appelé **IoTEdgeRepo**. Ce référentiel contient **IoTEdgeSolution** qui dispose du code d'un module nommé **filtermodule**. 

   ![Ouvrir votre projet DevOps](./media/how-to-ci-cd/init-project.png)

2. Accédez Azure Pipelines dans votre projet. Ouvrez l'onglet **Builds** et sélectionnez **Nouveau pipeline**. Si vous disposez déjà de pipelines de build, vous pouvez sélectionner le bouton **Nouveau**. Sélectionnez ensuite **Nouveau pipeline de build**.

    ![Créer un pipeline de build](./media/how-to-ci-cd/add-new-build.png)

3. Suivez les invites pour créer votre pipeline. 

   1. Fournissez les informations sur la source de votre nouveau pipeline de build. Sélectionnez le type de source **Azure Repos Git**, puis sélectionnez le projet, le référentiel et la branche où se trouve le code de votre solution IoT Edge. Sélectionnez ensuite **Continuer**. 

      ![Sélectionner la source de votre pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Sélectionnez **Projet vide** plutôt qu'un modèle. 

      ![Démarrer avec un projet vide](./media/how-to-ci-cd/start-with-empty.png)

4. Une fois votre pipeline créé, vous accédez à l’éditeur de pipeline. Dans la description de votre pipeline, sélectionnez le pool d'agents qui convient en fonction de votre plateforme cible : 
    
   * Si vous souhaitez générer vos modules dans des conteneurs de plateforme amd64 pour Linux, choisissez **Hosted Ubuntu 1604** (Ubuntu 1604 hébergé)

   * Si vous souhaitez générer vos modules dans des conteneurs de plateforme amd64 pour Windows 1809, vous devez [configurer l'agent auto-hébergé sur Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Si vous souhaitez générer vos modules dans des conteneurs de plateforme arm32v7 pour Linux, vous devez [configurer l'agent auto-hébergé sur Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurer le pool d’agents de build](./media/how-to-ci-cd/configure-env.png)

5. Votre pipeline est préconfiguré avec un travail appelé **Travail d'agent 1**. Sélectionnez le signe plus (**+**) pour ajouter trois tâches au travail : **Azure IoT Edge** deux fois, et **Publier des artefacts de build** une fois. (Placez le curseur sur le nom de chaque tâche pour afficher le bouton **Ajouter**.)

   ![Ajouter une tâche Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Une fois les trois tâches ajoutées, le travail de votre agent ressemble à l'exemple suivant :
    
   ![Trois tâches dans le pipeline de build](./media/how-to-ci-cd/add-tasks.png)

6. Sélectionnez la première tâche **Azure IoT Edge** pour la modifier. Cette tâche génère tous les modules dans la solution avec la plateforme cible que vous spécifiez. Elle génère aussi le fichier **deployment.json** qui indique à vos appareils IoT Edge comment configurer le déploiement.

   * **Nom complet** : Acceptez la valeur par défaut **Azure IoT Edge - Générer des images de module**.
   * **Action** : Acceptez la valeur par défaut **Générer des images de module**. 
   * **fichier .template.json** : Sélectionnez les points de suspension (**...** ) et accédez au fichier **deployment.template.json** du référentiel contenant votre solution IoT Edge. 
   * **Plateforme par défaut** : Sélectionnez la plateforme appropriée pour vos modules en fonction de votre appareil IoT Edge cible. 
   * **Variables de sortie** : Les variables de sortie incluent un nom de référence que vous pouvez utiliser pour configurer le chemin d’accès où votre fichier deployment.json sera généré. Définissez le nom de référence de manière à vous en souvenir, par exemple, **edge**. 

7. Sélectionnez la seconde tâche **Azure IoT Edge** pour la modifier. Cette tâche envoie (push) toutes les images de module vers le registre de conteneurs que vous sélectionnez. Elle ajoute également les informations d’identification de votre registre de conteneurs au fichier **deployment.json** pour permettre à votre appareil IoT Edge d'accéder aux images de module. 

   * **Nom d'affichage** : Le nom d’affichage est automatiquement mis à jour lorsque le champ d’action change. 
   * **Action** : Utilisez la liste déroulante pour sélectionner **Envoyer (push) des images de module**. 
   * **Type de registre de conteneurs** : Sélectionnez le type de registre de conteneurs que vous utilisez pour stocker vos images de module. Le formulaire change selon le type de registre que vous sélectionnez. Si vous choisissez **Azure Container Registry**, utilisez les listes déroulantes pour sélectionner l’abonnement Azure et le nom de votre registre de conteneurs. Si vous choisissez **Registre de conteneurs générique**, sélectionnez **Nouveau** pour créer une connexion de service de registre. 
   * **fichier .template.json** : Sélectionnez les points de suspension (**...** ) et accédez au fichier **deployment.template.json** du référentiel contenant votre solution IoT Edge. 
   * **Plateforme par défaut** : Sélectionnez la même plateforme que pour vos images de module générées.

   Si vous avez plusieurs registres de conteneurs pour héberger vos images de modules, vous devez dupliquer cette tâche, sélectionner un autre registre de conteneurs et utiliser **Bypass module(s)** (Ignorer les modules) dans les paramètres avancés pour ignorer les images qui ne concernent pas ce registre spécifique.

8. Sélectionnez la tâche **Publier des artefacts de build** pour la modifier. Indiquez le chemin d'accès au fichier de déploiement généré par la tâche de génération. Définissez la valeur **Chemin de publication** en fonction de la variable de sortie que vous avez définie dans la tâche de génération de module. Par exemple : `$(edge.DEPLOYMENT_FILE_PATH)`. Conservez les valeurs par défaut des autres options. 

9. Sélectionnez l’onglet **Déclencheurs**, puis la case à cocher **Activer l'intégration continue**. Assurez-vous que la branche qui contient votre code est incluse.

    ![Activer le déclencheur d’intégration continue](./media/how-to-ci-cd/configure-trigger.png)

10. Enregistrez le nouveau pipeline de build avec le bouton **Enregistrer**.

Ce pipeline est maintenant configuré pour s’exécuter automatiquement lorsque vous envoyez (push) un nouveau code à votre référentiel. La dernière tâche, publier les artefacts de pipeline, déclenche un pipeline de mise en production. Passez à la section suivante pour générer le pipeline de mise en production. 

## <a name="configure-continuous-deployment"></a>Configurer le déploiement continu
Dans cette section, vous créez un pipeline de mise en production configuré pour s’exécuter automatiquement quand votre pipeline de build supprime des artefacts. Ce pipeline de build affichera les journaux d’activité de déploiement dans Azure Pipelines.

Dans cette section, vous créez deux phases différentes, l'une pour les déploiements de test et l’autre pour les déploiements de production. 

### <a name="create-test-stage"></a>Créer une phase de test

Créez un pipeline et configurez sa première phase pour les déploiements d'assurance qualité (AQ). 

1. Sous l’onglet **Mises en production**, choisissez **+ Nouveau pipeline**. Sinon, si vous avez déjà des pipelines de mise en production, choisissez le bouton **+ Nouveau**, puis sélectionnez **+ Nouveau pipeline de mise en production**.  

    ![Ajouter un pipeline de mise en production](./media/how-to-ci-cd/add-release-pipeline.png)

2. Lorsque vous êtes invité à sélectionner un modèle, optez pour une **Tâche vide**.

    ![Démarrer par une tâche vide](./media/how-to-ci-cd/start-with-empty-job.png)

3. Votre nouveau pipeline de mise en production s'initialise avec une seule phase appelée **Phase 1**. Remplacez Phase 1 par **AQ**, puis traitez-la en tant qu'environnement de test. En règle générale, les pipelines de déploiement continu présentent plusieurs phases. Vous pouvez en créer davantage selon votre pratique DevOps. Fermez la fenêtre de détails de la phase une fois cette dernière renommée. 

    ![Phase Créer l’environnement de test](./media/how-to-ci-cd/QA-env.png)

4. Liez la mise en production aux artefacts de build publiés par le pipeline de build. Cliquez sur **Ajouter** dans la zone des artefacts.

   ![Ajouter des artefacts](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Dans la page **Ajouter un artefact**, sélectionnez le type de source **Build**. Sélectionnez ensuite le projet et le pipeline de build que vous avez créés. Sélectionnez ensuite **Ajouter**.

   ![Ajouter un artefact de build](./media/how-to-ci-cd/add-an-artifact.png)

6. Ouvrez les déclencheurs d’artefact, puis sélectionnez le bouton bascule pour activer le déclencheur de déploiement continu. Désormais, une mise en production est créée chaque fois qu’une nouvelle build est disponible.

   ![Configurer le déclencheur de déploiement continu](./media/how-to-ci-cd/add-a-trigger.png)

7. La phase **AQ** est préconfigurée avec un travail, mais aucune tâche. Dans le menu de pipeline, sélectionnez **Tâches**, puis la phase **AQ**.  Sélectionnez le nombre de travaux et de tâches pour configurer les tâches de cette phase.

    ![Configurer les tâches AQ (assurance qualité)](./media/how-to-ci-cd/view-stage-tasks.png)

8. Dans la phase AQ, vous devez voir un **Travail d'agent** par défaut. Vous pouvez configurer les détails relatifs au travail de l'agent, mais la tâche de déploiement est indépendante de la plateforme, ce qui signifie que vous pouvez utiliser **Hosted VS2017** (VS2017 hébergé) ou **Hosted Ubuntu 1604** (Ubuntu 1604 hébergé) dans le **Pool d’agents** (ou tout autre agent géré par vous-même). 

9. Cliquez sur le signe plus (**+**) pour ajouter une tâche. Recherchez et ajoutez **Azure IoT Edge**. 

    ![Ajouter des tâches AQ (assurance qualité)](./media/how-to-ci-cd/add-task-qa.png)

10. Sélectionnez la nouvelle tâche Azure IoT Edge et configurez-la avec les valeurs suivantes :

    * **Nom d'affichage** : Le nom d’affichage est automatiquement mis à jour lorsque le champ d’action change. 
    * **Action** : Utilisez la liste déroulante pour sélectionner **Déployer sur l’appareil IoT Edge**. Modifier la valeur d'action met également à jour le nom d'affichage de la tâche en conséquence.
    * **Abonnement Azure** : Sélectionnez l’abonnement contenant votre IoT Hub.
    * **Nom du IoT Hub** : Sélectionnez votre hub IoT. 
    * **Sélectionnez un ou plusieurs appareils** : indiquez si vous souhaitez que le pipeline de mise en production se déploie sur un ou plusieurs appareils. 
      * Si vous le déployez sur un seul appareil, entrez l'**ID d’appareil IoT Edge**. 
      * Si vous le déployez sur plusieurs appareils, spécifiez la **condition cible** des appareils. La condition cible est un filtre qui correspond à un ensemble d’appareils Edge dans IoT Hub. Pour utiliser les étiquettes des appareils en tant que condition, vous devez mettre à jour les étiquettes d’appareils correspondantes avec le jumeau d’appareil IoT Hub. Mettez à jour l'**ID de déploiement IoT Edge** et la **priorité de déploiement IoT Edge** dans les paramètres avancés. Pour plus d’informations sur la création d’un déploiement pour plusieurs appareils, consultez [Comprendre les déploiements automatiques IoT Edge](module-deployment-monitoring.md).

11. Sélectionnez **Enregistrer** pour enregistrer vos modifications dans le nouveau pipeline de mise en production. Pour revenir à la vue Pipeline, sélectionnez **Pipeline** dans le menu. 

### <a name="create-production-stage"></a>Créer une phase de production

Créez une deuxième phase dans votre pipeline de mise en production à des fins de déploiement de production. 

1. Pour cette deuxième phase de production, clonez la phase AQ. Placez votre curseur sur la phase AQ, puis cliquez sur le bouton de clonage. 

    ![Cloner la phase](./media/how-to-ci-cd/clone-stage.png)

2. Sélectionnez la nouvelle phase, appelée **Copie AQ**, pour ouvrir ses propriétés. Remplacez le nom de la phase par **PROD**, pour production. Fermez la fenêtre des propriétés de l'étape. 

3. Pour ouvrir les tâches de la phase PROD, sélectionnez **Tâches** dans le menu Pipeline, puis la phase **PROD**. 

4. Sélectionnez la tâche Azure IoT Edge afin de la configurer pour votre environnement de production. Les paramètres de déploiement sont probablement les mêmes pour AQ et PROD, sauf qu'en production, vous souhaitez cibler un ou plusieurs appareils différents. Mettez à jour le champ ID d'appareil ou la condition de la cible, et les champs ID de déploiement pour vos appareils de production. 

5. Enregistrez-le avec le bouton **Enregistrer**. Sélectionnez ensuite **Pipeline** pour revenir à la vue Pipeline.
    
6. Conformément à la configuration actuelle du pipeline de mise en production, l'artefact de build déclenchera la phase **AQ**, puis la phase **PROD** au terme de chaque nouvelle build. Cela étant, vous souhaiterez certainement intégrer plusieurs cas de test sur les appareils AQ et approuver manuellement le déploiement à des fins de production. Utilisez les étapes suivantes pour créer une condition d’approbation pour la phase PROD :

    1. Ouvrez le volet des paramètres **Conditions de prédéploiement**.

        ![Ouvrir les conditions de prédéploiement](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Définissez la condition **Approbations de prédéploiement** sur **Activé**. Ajouter un ou plusieurs utilisateurs ou groupes dans le champ **Approbateurs** et personnalisez les autres stratégies d'approbation de votre choix. Pour enregistrer vos modifications, fermez le panneau des conditions de prédéploiement.
    
       ![Définir des conditions](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Enregistrez votre pipeline de mise en production à l'aide du bouton **Enregistrer**. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Vérifier les fonctionnalités CI/CD IoT Edge avec les pipelines de build et de mise en production

Pour déclencher un travail de build, envoyez (push) une validation dans le dépôt de code source, ou déclenchez-le manuellement. Dans cette section, vous déclenchez manuellement le pipeline CI/CD pour tester son fonctionnement. Vérifiez ensuite que le déploiement a abouti.

1. Accédez au pipeline de build que vous avez créé au début de cet article. 

2. Vous pouvez déclencher un travail de build dans votre pipeline de build en sélectionnant le bouton **File d’attente**, comme indiqué dans la capture d’écran suivante.

    ![Déclencheur manuel](./media/how-to-ci-cd/manual-trigger.png)

3. Sélectionnez le travail de build afin de consulter sa progression. Si le pipeline de build s'achève correctement, il déclenche une mise en production sur la phase **AQ**. 

    ![Journaux d’activité de génération](./media/how-to-ci-cd/build-logs.png)

4. Le déploiement réussi à la phase **AQ** déclenche l’envoi d’une notification à l’approbateur. Vérifiez que les modules ont été correctement déployés sur le ou les appareils que vous avez ciblés avec la phase AQ. Accédez ensuite au pipeline de mise en production et approuvez cette dernière pour accéder à la phase PROD en sélectionnant le bouton **PROD**, puis **Approuver**. 

    ![Approbation en attente](./media/how-to-ci-cd/pending-approval.png)

5. Une fois que l’approbateur a approuvé ce changement, il peut être déployé sur **PROD**.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le déploiement IoT Edge, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle](module-deployment-monitoring.md).
* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
