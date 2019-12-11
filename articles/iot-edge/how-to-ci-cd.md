---
title: Intégration continue et déploiement continu - Azure IoT Edge
description: Configurer l’intégration continue et le déploiement continu - Azure IoT Edge avec Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 463de1f49ad8fd21c355395bec3a55d9d40474e6
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666356"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Intégration continue et déploiement continu dans Azure IoT Edge

Vous pouvez facilement adopter DevOps avec vos applications Azure IoT Edge via les tâches Azure IoT Edge intégrées dans Azure Pipelines. Cet article montre comment utiliser les fonctionnalités d’intégration continue et de déploiement continu d’Azure Pipelines pour générer, tester et déployer des applications rapidement et efficacement sur Azure IoT Edge. 

![Diagramme - Branches CI et CD pour le développement et la production](./media/how-to-ci-cd/cd.png)

Dans cet article, vous allez apprendre à utiliser les tâches Azure IoT Edge intégrées pour Azure Pipelines afin de créer deux pipelines destinés à votre solution IoT Edge. Quatre actions peuvent s'appliquer aux tâches Azure IoT Edge.
   - **Azure IoT Edge - Générer les images de module** génère les images conteneur à partir du code de votre solution IoT Edge.
   - **Azure IoT Edge - Envoyer (push) les images de module** envoie (push) les images de module au registre de conteneurs que vous avez spécifié.
   - **Azure IoT Edge - Générer le manifeste de déploiement** génère le fichier final du manifeste de déploiement IoT Edge à partir d'un fichier deployment.template.json et des variables.
   - **Azure IoT Edge - Déployer sur des appareils IoT Edge** vous aide à créer des déploiements IoT Edge sur un ou plusieurs appareils IoT Edge.

## <a name="prerequisites"></a>Prérequis

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
>Pour plus d’informations, consultez [Créer un pipeline de build](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Connectez-vous à votre organisation Azure DevOps (**https:\//dev.azure.com/{votre organisation}/** ), puis ouvrez le projet contenant votre référentiel de solutions IoT Edge.

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

   * Si vous souhaitez générer vos modules dans des conteneurs de plateforme arm32v7 ou arm64 pour Linux, vous devez [configurer l'agent auto-hébergé sur Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurer le pool d’agents de build](./media/how-to-ci-cd/configure-env.png)

5. Votre pipeline est préconfiguré avec un travail appelé **Travail d'agent 1**. Sélectionnez le signe plus ( **+** ) pour ajouter trois tâches au travail : **Azure IoT Edge** deux fois, **Copier des fichiers** une fois et **Publier des artefacts de build** une fois. (Placez le curseur sur le nom de chaque tâche pour afficher le bouton **Ajouter**.)

   ![Ajouter une tâche Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Une fois les quatre tâches ajoutées, le travail de votre agent est semblable à l'exemple suivant :
    
   ![Trois tâches dans le pipeline de build](./media/how-to-ci-cd/add-tasks.png)

6. Sélectionnez la première tâche **Azure IoT Edge** pour la modifier. Cette tâche génère tous les modules de la solution avec la plateforme cible que vous spécifiez.

   * **Nom d'affichage** : Acceptez la valeur par défaut **Azure IoT Edge - Générer des images de module**.
   * **Action** : Acceptez la valeur par défaut **Générer des images de module**. 
   * **fichier .template.json** : Sélectionnez les points de suspension ( **...** ) et accédez au fichier **deployment.template.json** du référentiel contenant votre solution IoT Edge. 
   * **Plateforme par défaut** : Sélectionnez la plateforme appropriée pour vos modules en fonction de votre appareil IoT Edge cible. 
   * **Variables de sortie** : Les variables de sortie incluent un nom de référence que vous pouvez utiliser pour configurer le chemin d’accès où votre fichier deployment.json sera généré. Définissez le nom de référence de manière à vous en souvenir, par exemple, **edge**. 

7. Sélectionnez la seconde tâche **Azure IoT Edge** pour la modifier. Cette tâche envoie (push) toutes les images de module vers le registre de conteneurs que vous sélectionnez.

   * **Nom d'affichage** : Le nom d’affichage est automatiquement mis à jour lorsque le champ d’action change. 
   * **Action** : Utilisez la liste déroulante pour sélectionner **Envoyer (push) des images de module**. 
   * **Type de registre de conteneurs** : Sélectionnez le type de registre de conteneurs que vous utilisez pour stocker vos images de module. Le formulaire change selon le type de registre que vous sélectionnez. Si vous choisissez **Azure Container Registry**, utilisez les listes déroulantes pour sélectionner l’abonnement Azure et le nom de votre registre de conteneurs. Si vous choisissez **Registre de conteneurs générique**, sélectionnez **Nouveau** pour créer une connexion de service de registre. 
   * **fichier .template.json** : Sélectionnez les points de suspension ( **...** ) et accédez au fichier **deployment.template.json** du référentiel contenant votre solution IoT Edge. 
   * **Plateforme par défaut** : Sélectionnez la même plateforme que pour vos images de module générées.

   Si vous avez plusieurs registres de conteneurs pour héberger vos images de modules, vous devez dupliquer cette tâche, sélectionner un autre registre de conteneurs et utiliser **Bypass module(s)** (Ignorer les modules) dans les paramètres avancés pour ignorer les images qui ne concernent pas ce registre spécifique.

8. Sélectionnez la tâche **Copier des fichiers** pour la modifier. Utilisez cette tâche pour copier des fichiers dans le répertoire intermédiaire des artefacts.

   * **Nom d'affichage** : Copier des fichiers vers : Emplacement de dépôt.
   * **Contenu** : Placez deux lignes dans cette section, `deployment.template.json` et `**/module.json`. Ces deux types de fichiers sont les entrées qui permettent de générer le manifeste de déploiement IoT Edge. Ils doivent être copiés dans le dossier intermédiaire des artefacts et publiés pour le pipeline de mise en production.
   * **Dossier cible** : Placez la variable `$(Build.ArtifactStagingDirectory)`. Pour en savoir plus sur la description, consultez [Générer des variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables).

9. Sélectionnez la tâche **Publier des artefacts de build** pour la modifier. Indiquez le chemin du répertoire intermédiaire des artefacts permettant d'accéder à la tâche afin que le chemin puisse être publié dans le pipeline de mise en production.
   
   * **Nom d'affichage** : Publication d'artefact : dépôt.
   * **Chemin de publication** : Placez la variable `$(Build.ArtifactStagingDirectory)`. Pour en savoir plus sur la description, consultez [Générer des variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables).
   * **Nom de l'artefact** : dépôt.
   * **Emplacement de publication des artefacts** : Azure Pipelines.


10. Sélectionnez l’onglet **Déclencheurs**, puis la case à cocher **Activer l'intégration continue**. Assurez-vous que la branche qui contient votre code est incluse.

    ![Activer le déclencheur d’intégration continue](./media/how-to-ci-cd/configure-trigger.png)

11. Enregistrez le nouveau pipeline de build avec le bouton **Enregistrer**.

Ce pipeline est maintenant configuré pour s’exécuter automatiquement lorsque vous envoyez (push) un nouveau code à votre référentiel. La dernière tâche, publier les artefacts de pipeline, déclenche un pipeline de mise en production. Passez à la section suivante pour générer le pipeline de mise en production. 

## <a name="configure-continuous-deployment"></a>Configurer le déploiement continu
Dans cette section, vous créez un pipeline de mise en production configuré pour s’exécuter automatiquement quand votre pipeline de build supprime des artefacts. Ce pipeline de build affichera les journaux d’activité de déploiement dans Azure Pipelines.

Créer un pipeline et ajouter une nouvelle phase 

1. Sous l’onglet **Mises en production**, choisissez **+ Nouveau pipeline**. Sinon, si vous avez déjà des pipelines de mise en production, choisissez le bouton **+ Nouveau**, puis sélectionnez **+ Nouveau pipeline de mise en production**.  

    ![Ajouter un pipeline de mise en production](./media/how-to-ci-cd/add-release-pipeline.png)

2. Lorsque vous êtes invité à sélectionner un modèle, optez pour une **Tâche vide**.

    ![Démarrer par une tâche vide](./media/how-to-ci-cd/start-with-empty-job.png)

3. Votre nouveau pipeline de mise en production s'initialise avec une seule phase appelée **Phase 1**. Remplacez Phase 1 par **dev**, puis traitez-la en tant qu'environnement de test. En règle générale, les pipelines de déploiement continu présentent plusieurs phases, notamment **dev**, **mise en lots** et **prod**. Vous pouvez en créer davantage selon votre pratique DevOps. Fermez la fenêtre de détails de la phase une fois cette dernière renommée. 

4. Liez la mise en production aux artefacts de build publiés par le pipeline de build. Cliquez sur **Ajouter** dans la zone des artefacts.

   ![Ajouter des artefacts](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Dans la page **Ajouter un artefact**, sélectionnez le type de source **Build**. Sélectionnez ensuite le projet et le pipeline de build que vous avez créés. Sélectionnez ensuite **Ajouter**.

   ![Ajouter un artefact de build](./media/how-to-ci-cd/add-an-artifact.png)

6. Ouvrez les déclencheurs d’artefact, puis sélectionnez le bouton bascule pour activer le déclencheur de déploiement continu. Désormais, une mise en production est créée chaque fois qu’une nouvelle build est disponible.

   ![Configurer le déclencheur de déploiement continu](./media/how-to-ci-cd/add-a-trigger.png)

7. La phase **dev** est préconfigurée avec un travail, mais aucune tâche. Dans le menu de pipeline, sélectionnez **Tâches**, puis la phase **dev**.  Sélectionnez le nombre de travaux et de tâches pour configurer les tâches de cette phase.

    ![Configurer des tâches dev](./media/how-to-ci-cd/view-stage-tasks.png)

8. Dans la phase **dev**, vous devez voir un **travail d'agent** par défaut. Vous pouvez configurer les détails relatifs au travail de l'agent, mais la tâche de déploiement est indépendante de la plateforme, ce qui signifie que vous pouvez utiliser **Hosted VS2017** (VS2017 hébergé) ou **Hosted Ubuntu 1604** (Ubuntu 1604 hébergé) dans le **Pool d’agents** (ou tout autre agent géré par vous-même). 

9. Cliquez sur le signe plus ( **+** ) pour ajouter deux tâches. Recherchez et ajoutez **Azure IoT Edge** deux fois.

    ![Ajouter des tâches dev (développement)](./media/how-to-ci-cd/add-task-qa.png)

10. Sélectionnez la première tâche **Azure IoT Edge** et configurez-la avec les valeurs suivantes :

    * **Nom d'affichage** : Le nom d’affichage est automatiquement mis à jour lorsque le champ d’action change. 
    * **Action** : Utilisez la liste déroulante pour sélectionner **Générer un manifeste de déploiement**. Modifier la valeur d'action met également à jour le nom d'affichage de la tâche en conséquence.
    * **fichier .template.json** : Placez le chemin `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. Le chemin est publié à partir du pipeline de build.
    * **Plateforme par défaut** : Choisissez la même valeur lors de la création des images de module.
    * **Chemin de sortie** : Placez le chemin `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. Ce chemin est le fichier final du manifeste de déploiement IoT Edge.

    Ces configurations permettent de remplacer les URL des images de module dans le fichier `deployment.template.json`. **Générer le manifeste de déploiement** permet également de remplacer les variables par la valeur exacte que vous avez définie dans le fichier `deployment.template.json`. Dans VS/VS Code, vous spécifiez la valeur réelle dans un fichier `.env`. Dans Azure Pipelines, vous définissez la valeur dans l'onglet Variables du pipeline de mise en production. Accédez à l'onglet Variables et configurez le nom et la valeur comme suit.

    * **ACR_ADDRESS** : votre adresse Azure Container Registry. 
    * **ACR_PASSWORD** : votre mot de passe Azure Container Registry.
    * **ACR_USER** : votre nom d'utilisateur Azure Container Registry.

    Si votre projet contient d'autres variables, vous pouvez spécifier le nom et la valeur dans cet onglet. **Générer le manifeste de déploiement** ne reconnaît que les variables de type `${VARIABLE}`. Par conséquent, veillez à utiliser ce type dans vos fichiers `*.template.json`.

    ![Configurer des variables pour le pipeline de mise en production](./media/how-to-ci-cd/configure-variables.png)

10. Sélectionnez la deuxième tâche **Azure IoT Edge** et configurez-la avec les valeurs suivantes :

    * **Nom d'affichage** : Le nom d’affichage est automatiquement mis à jour lorsque le champ d’action change. 
    * **Action** : Utilisez la liste déroulante pour sélectionner **Déployer sur des appareils IoT Edge**. Modifier la valeur d'action met également à jour le nom d'affichage de la tâche en conséquence.
    * **Abonnement Azure** : Sélectionnez l’abonnement contenant votre IoT Hub.
    * **Nom du IoT Hub** : Sélectionnez votre hub IoT. 
    * **Sélectionnez un ou plusieurs appareils** : indiquez si vous souhaitez que le pipeline de mise en production se déploie sur un ou plusieurs appareils. 
      * Si vous le déployez sur un seul appareil, entrez l'**ID d’appareil IoT Edge**. 
      * Si vous le déployez sur plusieurs appareils, spécifiez la **condition cible** des appareils. La condition cible est un filtre qui correspond à un ensemble d'appareils IoT Edge dans IoT Hub. Pour utiliser les étiquettes des appareils en tant que condition, vous devez mettre à jour les étiquettes d’appareils correspondantes avec le jumeau d’appareil IoT Hub. Mettez à jour l'**ID de déploiement IoT Edge** et la **priorité de déploiement IoT Edge** dans les paramètres avancés. Pour plus d’informations sur la création d’un déploiement pour plusieurs appareils, consultez [Comprendre les déploiements automatiques IoT Edge](module-deployment-monitoring.md).
    * Développez Paramètres avancés, sélectionnez **ID de déploiement IoT Edge** et placez la variable `$(System.TeamProject)-$(Release.EnvironmentName)`. Le projet et le nom de la version sont ainsi mappés avec votre ID de déploiement IoT Edge.

11. Sélectionnez **Enregistrer** pour enregistrer vos modifications dans le nouveau pipeline de mise en production. Pour revenir à la vue Pipeline, sélectionnez **Pipeline** dans le menu. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Vérifier les fonctionnalités CI/CD IoT Edge avec les pipelines de build et de mise en production

Pour déclencher un travail de build, envoyez (push) une validation dans le dépôt de code source, ou déclenchez-le manuellement. Dans cette section, vous déclenchez manuellement le pipeline CI/CD pour tester son fonctionnement. Vérifiez ensuite que le déploiement a abouti.

1. Accédez au pipeline de build que vous avez créé au début de cet article. 

2. Vous pouvez déclencher un travail de build dans votre pipeline de build en sélectionnant le bouton **File d’attente**, comme indiqué dans la capture d’écran suivante.

    ![Déclencheur manuel](./media/how-to-ci-cd/manual-trigger.png)

3. Sélectionnez le travail de build afin de consulter sa progression. Si le pipeline de build s'achève correctement, il déclenche une mise en production en phase **dev**. 

    ![Journaux d’activité de génération](./media/how-to-ci-cd/build-logs.png)

4. La mise en production **dev** réussie crée un déploiement IoT Edge sur les appareils IoT Edge cibles.

    ![Mise en production en phase dev](./media/how-to-ci-cd/pending-approval.png)

5. Cliquez sur la phase **dev** pour afficher les journaux de mise en production.

    ![Journaux d’activité de mise en production](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Étapes suivantes
* Exemple de bonnes pratiques IOT Edge DevOps dans [Projet Azure DevOps pour IoT Edge](how-to-devops-project.md)
* Pour plus d’informations sur le déploiement IoT Edge, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle](module-deployment-monitoring.md).
* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
