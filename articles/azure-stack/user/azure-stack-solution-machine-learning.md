---
title: Créer une solution de périphérie de Machine Learning avec Azure et Azure Stack | Microsoft Docs
description: Découvrez comment créer une solution de périphérie de Machine Learning à l’aide de Python avec Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: c195f2ee24b61a57c098d5214a37f65e80845074
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410202"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>Didacticiel : Créer une solution de périphérie de Machine Learning avec Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Découvrez comment créer une solution de périphérie de Machine Learning avec Azure et Azure Stack.

Dans ce tutoriel, vous créez un exemple d’environnement pour :

> [!div class="checklist"]
> - Créer un compte de stockage et un conteneur pour les données propres.
> - Créer une machine DSVM (Data Science Virtual Machine) Ubuntu dans le portail Azure.
> - Déployer des services Azure Machine Learning dans Azure pour créer et former des modèles.
> - Créer des comptes de service Azure Machine Learning.
> - Déployer et utiliser Azure Container Registry.
> - Déployer un cluster Kubernetes sur Azure Stack.
> - Créer un compte Stockage Azure Stack et une file d’attente de stockage pour les données.
> - Créer une fonction Azure Stack pour déplacer les données propres d’Azure Stack vers Azure.

**Quand utiliser cette solution ?**

 -  Votre organisation utilise une approche DevOps ou en a planifié une dans un avenir proche.
 -  Vous souhaitez mettre en œuvre des pratiques CI/CD dans votre implémentation d’Azure Stack et dans le cloud public.
 -  Vous souhaitez consolider le pipeline CI/CD dans des environnements cloud et locaux.
 -  Vous souhaitez développer des applications à l’aide de services cloud ou locaux.
 -  Vous souhaitez tirer parti de compétences de développement cohérentes pour les applications cloud et locales.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local et active le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tout lieu.  
> 
> Le livre blanc [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Étude des conceptions pour les applications hybrides) se penche sur les fondements de la qualité logicielle (sélection élective, extensibilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception des applications hybrides, en réduisant les risques dans les environnements de production.

## <a name="prerequisites"></a>Prérequis

Certains composants sont nécessaires pour créer ce cas d’usage, et leur préparation peut prendre un certain temps :

 -  Un partenaire OEM/matériel Azure peut déployer un système Azure Stack de production, et tous les utilisateurs peuvent déployer un kit ASDK

 -  Un opérateur Azure Stack doit également déployer le service App Service, créer des plans et des offres, créer un abonnement de locataire et ajouter l’image Windows Server 2016

 -  La configuration d’un réseau hybride et d’App Service est requise (en savoir plus sur l’[intégration d’applications avec des réseaux virtuels.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  L’[agent de build et de mise en production](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) privé pour l’intégration VSTS doit être configuré avant le déploiement. (Vérifiez que tous les composants utilisés répondent aux exigences avant de commencer.)

Il est nécessaire de bien connaître Azure et Azure Stack au préalable. Pour en savoir plus avant de continuer, lisez les rubriques suivantes :

 -  [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Concepts clés d’Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Guide de solution CI/CD hybride Azure Stack](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Microsoft Azure**

 -  Abonnement Azure (créez un [ compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  URL d’application web créée par une [application web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) dans Azure

 -  Déploiement de [Kubernetes ACS (Azure Container Service) sur Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Déploiement du service Azure Machine Learning (préversion) : [didacticiel en 4 parties](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)

 -  [Compte](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template) Azure Machine Learning : Expérimentation

**Azure Stack**

 -  Système intégré Azure Stack ou déploiement du Kit de développement Azure Stack.

    - La page [Installer le Kit de développement Azure Stack](/articles/azure-stack/asdk/asdk-install) comporte des instructions permettant d’installer Azure Stack.
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Cette installation peut nécessiter plusieurs heures.

 -  Déploiement des services PaaS [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) sur Azure Stack

 -  [Plan/offres](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dans l’environnement Azure Stack

 -  [Abonnement de locataire](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dans l’environnement Azure Stack

 -  Image de machine virtuelle Ubuntu Server (disponible dans la [Place de marché Azure Stack](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)). Cette machine virtuelle est générée dans l’abonnement du locataire sur Azure Stack en tant qu’agent de build privé, tout comme les machines virtuelles Kubernetes. Si cette image n’est pas disponible, l’opérateur Azure Stack peut vous aider à vous assurer qu’elle est ajoutée à l’environnement. (N’utilisez pas la build 18.04 Ubuntu, car elle n’est pas prise en charge actuellement.)

 -  Application web au sein de l’abonnement du locataire (Notez la nouvelle URL d’application web pour une utilisation ultérieure.)

 -  Déploiement d’une machine virtuelle d’agent de build privé Linux VSTS au sein de l’abonnement du locataire

 -  Déploiement de [Kubernetes ACS (Azure Container Service) sur Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**Outils de développeur**

 -  [Espace de travail VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (le processus d’inscription crée un projet nommé « MyFirstProject »)

 -  Installation de [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) et connexion [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)

 -  [Clone local](https://www.visualstudio.com/docs/git/gitquickstart) du projet

 -  Installation du [sous-système Linux pour Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) (pour BASH et SSH)

 -  Installation de [Docker pour Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe)

 -  Installation d’[Azure Machine Learning Workbench (préversion)](https://aka.ms/azureml-wb-msi)

 -  Installation de l’environnement [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe)

**VSTS**

 -  **Compte VSTS.** Configurez rapidement l’intégration continue de build, de test et de déploiement. Pour plus d’informations sur les comptes VSTS, consultez [Create the VSTS Account](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts) (Créer le compte VSTS).

 -  **Référentiel de code.** Avec les référentiels de code existants dans GitHub, BitBucket, DropBox, OneDrive et TFS, la plateforme VSTS permet de tirer parti de plusieurs référentiels de code pour rationaliser le pipeline de développement. Pour plus d’informations sur les référentiels de code, consultez le didacticiel [Get Started with Git and VSTS](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) (Prise en main de Git et VSTS).

 -  **Connexion de service.** Connectez-vous à des services externes et distants pour exécuter des tâches de test, de build ou de déploiement. Pour plus d’informations sur les connexions de service VSTS, consultez [Service Endpoints for Build and Release](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts) (Points de terminaison de service pour la build et la mise en production).

 -  **Définitions de build.** Définissez des processus de build automatisés et composez un ensemble de tâches par le biais du catalogue des tâches. Pour plus d’informations sur les définitions de build, consultez la documentation [Créer une définition de build](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts).

 -  **Définitions de mise en production.** Définissez le processus de déploiement pour une collection d’environnements où des artefacts d’application sont déployés. Pour plus d’informations sur les définitions de mise en production, consultez la documentation [Créer une définition de mise en production](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts).

 -  **Pool d’agents de build Linux VSTS hébergés.** Créez, testez et déployez rapidement des applications à l’aide d’un agent hébergé qui est géré et maintenu par Microsoft. Pour plus d’informations sur les agents de build VSTS hébergés, consultez la documentation [Agents hébergés](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts).

## <a name="step-1-create-a-storage-account"></a>Étape 1 : création d’un compte de stockage

Créer un compte de stockage et un conteneur pour les données propres.

1.  Connectez-vous au [*portail Azure*](https://portal.azure.com/).

2.  Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Tous les services**. Faites défiler les options pour accéder à **Stockage**, puis sélectionnez **Comptes de stockage**. Dans la fenêtre **Comptes de stockage**, sélectionnez **Ajouter**.

3.  Nommez le compte de stockage.

    > [!Note]  
    > Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Le nom du compte de stockage doit être unique dans Azure. Le portail Azure signale si le nom du compte de stockage sélectionné est déjà utilisé.

4.  Sélectionnez le modèle de déploiement à utiliser : **Resource Manager**.

5.  Sélectionnez le type de compte de stockage : **Usage général v1**, puis spécifiez le niveau de performance : **Standard**.

6.  Sélectionnez l’option de réplication pour le compte de stockage : **GRS**.

7.  Sélectionnez un nouvel abonnement du compte de stockage.

8.  Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant.

9.  Sélectionnez l’emplacement géographique du compte de stockage.

10. Sélectionnez **Créer** pour créer le compte de stockage.

    ![Alt text](\media\azure-stack-solution-machine-learning\image1.png)

11.  Choisissez le compte de stockage récemment créé.

12.  Sélectionnez **Blobs**.

    ![Alt text](media\azure-stack-solution-machine-learning\image2.png)

13.  Sélectionnez **+ Conteneur**, puis **Conteneur**.

    ![Alt text](media\azure-stack-solution-machine-learning\image3.png)

14.  Nommez le conteneur **uploadeddata** et choisissez le type d’accès **Conteneur**.

15.  Sélectionnez **Créer**.

    ![Alt text](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>Étape 2 : Créer une machine Data Science Virtual Machine

Créez une machine DSVM (Data Science Virtual Machine) Ubuntu dans le portail Azure.

1.  Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/)

2.  Cliquez sur le lien **+NOUVEAU**, puis recherchez « Data Science Virtual Machine for Linux Ubuntu CSP ».

    ![Alt text](media\azure-stack-solution-machine-learning\image5.png)

1.  Choisissez **Data Science Virtual Machine for Linux (Ubuntu)** dans la liste, puis suivez les instructions à l’écran pour créer la machine DSVM.

    ![Alt text](media\azure-stack-solution-machine-learning\image6.png)

> ![Important]  
> **Choisissez** Mot de passe** comme *type d’authentification*.

Placez la nouvelle machine DSVM dans le même groupe de ressources que le compte de stockage nouvellement créé. Tous les objets Edge ML sont déployés dans Azure au sein de ce groupe de ressources.

1.  Dans les paramètres, configurez les fonctionnalités facultatives

    a.  Sélectionnez le **compte de stockage** que vous avez préalablement créé.

    b.  Créez un **réseau virtuel**, un **sous-réseau** et une **adresse IP publique**. Cette opération doit créer par défaut un nom basé sur le nom du groupe de ressources.

    c.  Créez un **NSG**. Les règles appropriées doivent déjà être appliquées.

    d.  Pour le **compte de stockage Diagnostics**, sélectionnez le compte de stockage créé précédemment.

    e.  Remarque : Une fois AAD activé et configuré pour l’abonnement Azure, Managed Service Identity peut aussi être activé.

2.  Sélectionnez **OK**.

### <a name="connect-to-the-dsvm"></a>Se connecter à la machine DSVM

Une fois que la machine DSVM a été créée, connectez-vous à la machine virtuelle à partir du sous-système Windows pour Linux.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  Tapez YES lorsque vous êtes invité à confirmer la connexion à la machine virtuelle.

2.  Entrez le mot de passe créé pour la machine DSVM.

### <a name="update-the-dsvm"></a>Mettre à jour la machine DSVM 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>Étape 3 : Déployer des services Azure Machine Learning

Déployez des services Azure Machine Learning dans Azure.

Les services Azure Machine Learning (préversion) forment une solution d’analytique avancée et de science des données de bout en bout intégrée. Cette solution permet aux scientifiques des données professionnels de préparer des données, de développer des expériences et de déployer des modèles à l’échelle du cloud.

Cette section explique comment :

> [!div class="checklist"]
> - Créer des comptes de service pour les services Azure Machine Learning
> - Installer Azure Machine Learning Workbench et s’y connecter
> - Créer un projet dans Workbench
> - Exécuter un script dans ce projet
> - Accéder à l’interface de ligne de commande

Dans le cadre du portefeuille Microsoft Azure, les services Azure Machine Learning requièrent un abonnement Azure. Pour obtenir un abonnement Azure, créez un [ compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Des autorisations adéquates sont requises pour créer des ressources telles que des groupes de ressources, des machines virtuelles, etc.

Vous pouvez installer l’application Azure Machine Learning Workbench sur les systèmes d’exploitation suivants :

 -  Windows 10 ou Windows Server 2016
 -  macOS Sierra ou High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>Étape 4 : Créer des services Azure Machine Learning

Créer des comptes de service Azure Machine Learning.

Utilisez le portail Azure pour configurer les comptes Azure Machine Learning :

1.  Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide des informations d’identification de l’abonnement Azure à utiliser. Pour obtenir un abonnement Azure, créez un [ compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    ![Alt text](media\azure-stack-solution-machine-learning\image7.png)

1.  Cliquez sur le bouton **Créer une ressource** (+) dans le coin supérieur gauche du portail.

    ![Créer une ressource dans le portail Azure](media\azure-stack-solution-machine-learning\image8.png)

1.  Tapez **Machine Learning** dans la barre de recherche. Sélectionnez le résultat de la recherche intitulé **Machine Learning Experimentation (preview)** (Expérimentation Machine Learning préversion.

    ![Recherche Azure Machine Learning](media\azure-stack-solution-machine-learning\image9.png)

1.  Dans le volet **Machine Learning : Expérimentation**, faites défiler l’écran vers le bas et sélectionnez **Créer** pour commencer à définir le compte d’expérimentation.

    ![Azure Machine Learning - créer un compte Expérimentation](media\azure-stack-solution-machine-learning\image10.png)

1.  Dans le volet **Machine Learning : Expérimentation**, configurez le compte Machine Learning : Expérimentation.

    | Paramètre | Valeur suggérée pour le didacticiel | Description |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nom du compte Expérimentation | Nom unique | Entrez un nom unique qui identifie le compte. Utilisez le nom d’un service ou d’un projet qui identifie le mieux l’expérimentation. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. |
    | Abonnement | Abonnement | Choisissez l’abonnement Azure à utiliser pour l’expérimentation. Lorsque plusieurs abonnements existent, sélectionnez l’abonnement approprié dans lequel la ressource est facturée. |
    | Groupe de ressources | Groupe de ressources | Utilisez un groupe de ressources existant dans l’abonnement, ou entrez un nom afin de créer un groupe de ressources pour ce compte d’expérimentation. |
    | Lieu | Région la plus proche de vos utilisateurs | Choisissez l’emplacement le plus proche des utilisateurs et des ressources de données. |
    | Nombre d’utilisateurs | 2 | Entrez le nombre d’utilisateurs. Découvrez comment [le nombre de places a un impact sur la tarification](https://azure.microsoft.com/pricing/details/machine-learning/).<br><br>Pour ce démarrage rapide, seuls deux postes sont nécessaires. Des sièges peuvent être ajoutés ou supprimés en fonction des besoins dans le portail Azure. |
    | Compte de stockage | Nom unique | Sélectionnez **Créer** et indiquez un nom pour créer un [compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal). Le nom doit comprendre entre 3 et 24 caractères, et seuls des caractères alphanumériques peuvent être utilisés. Vous pouvez également sélectionner **Utiliser l’existant** et choisir le compte de stockage existant dans la liste. Le compte de stockage est obligatoire et sert à stocker les artefacts de projet et les données de l’historique des exécutions. |
    | Espace de travail pour le compte Expérimentation | IrisGarden<br>(nom utilisé dans les tutoriels) | Fournissez un nom pour un espace de travail de ce compte. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. Cet espace de travail contient les outils requis pour créer, gérer et publier des expériences. |
    | Affecter le propriétaire de l’espace de travail | Compte | Sélectionnez le compte comme propriétaire de l’espace de travail. |
    | Créer un compte Gestion des modèles | **check** | Créez un compte de Gestion des modèles. Cela permet de déployer et gérer les modèles en tant que services web en temps réel. <br><br>Même si cela reste facultatif, il est recommandé de créer le compte Gestion des modèles en même temps que le compte Expérimentation. |
    | Nom du compte | Nom unique | Choisissez un nom unique qui identifie le compte Gestion des modèles. Utilisez le nom d’un service ou d’un projet identifiant au mieux l’expérimentation. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. |
    | Niveau tarifaire de Gestion des modèles | **DEVTEST** | Sélectionnez **Aucun niveau tarifaire sélectionné** pour spécifier le niveau tarifaire du nouveau compte Gestion des modèles. Pour réduire les coûts, sélectionnez le niveau tarifaire DEVTEST s’il est disponible sur l’abonnement (disponibilité limitée). Dans le cas contraire, sélectionnez le niveau tarifaire S1. Choisissez Sélectionner pour enregistrer la sélection du niveau tarifaire. |
    | Épingler au tableau de bord | case activée | Sélectionnez l’option **Épingler au tableau de bord** pour faciliter le suivi du compte Machine Learning : Expérimentation sur la première page du tableau de bord du portail Azure. |

    ![Configuration du compte Machine Learning - Expérimentation](media\azure-stack-solution-machine-learning\image11.png)

1.  Sélectionnez **Créer** pour commencer le processus de création du compte Expérimentation et du compte Gestion des modèles.

    ![Configuration du compte Machine Learning - Expérimentation](media\azure-stack-solution-machine-learning\image12.png)

    La création du compte peut prendre quelques instants. Vérifiez l’état du processus de déploiement en sélectionnant l’icône de Notifications (cloche) dans la barre d’outils du portail Azure.

    ![Notifications du portail Azure](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>Installer Workbench et s’y connecter 

Azure Machine Learning Workbench est disponible pour Windows ou macOS. Consultez la liste des [plateformes prises en charge](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation).

**Attention :** L’installation nécessite environ une heure.

1.  Téléchargez et lancez le programme d’installation de Workbench le plus récent.

    > [!Important]  
    > Téléchargez le programme d’installation entièrement sur le disque, puis exécutez-le à partir de là. Ne l’exécutez pas directement à partir du widget de téléchargement du navigateur.<br>**Sous Windows :<br>** a. Téléchargez [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).<br>b. Double-cliquez sur le programme d’installation téléchargé dans l’Explorateur de fichiers.

1.  Suivez les instructions à l’écran dans le programme d’installation.

    **L’installation nécessite environ 30 minutes. **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    Le programme d’installation télécharge et configure toutes les dépendances nécessaires, comme Python, Miniconda et d’autres bibliothèques associées. Cette installation inclut aussi l’outil en ligne de commande multiplateforme Azure, également appelé Azure CLI.

1.  Lancez Workbench en sélectionnant le bouton **Launch Workbench** (Lancer Workbench) du dernier écran du programme d’installation.

    Si le programme d’installation est fermé, vous pouvez la démarrer par le biais du raccourci du Bureau **Machine Learning Workbench**.

1.  Sélectionnez **Se connecter avec Microsoft** pour vous authentifier auprès d’Azure Machine Learning Workbench. Utilisez les mêmes informations d’identification que celles du portail Azure pour créer les comptes Expérimentation et Gestion des modèles.

    Une fois que vous êtes authentifié, Workbench utilise le premier compte Expérimentation qu’il trouve dans les abonnements Azure et affiche tous les espaces de travail et projets associés à ce compte.

    > [!Tip]  
    > Basculez vers un autre compte Expérimentation à l’aide de l’icône dans le coin inférieur gauche de la fenêtre d’application Workbench.

### <a name="create-a-new-project-in-workbench"></a>Créer un projet dans Workbench

1.  Ouvrez l’application Azure Machine Learning Workbench et connectez-vous si nécessaire.

    - Sous Windows, démarrez-la par le biais du raccourci du Bureau **Machine Learning Workbench**.

    - Sur macOS, sélectionnez **Azure Machine Learning Workbench** dans Launchpad.

1.  Dans le volet **PROJETS**, sélectionnez le signe plus (+) et choisissez **Nouveau projet**.

    ![Nouvel espace de travail](media\azure-stack-solution-machine-learning\image14.png)

1.  Renseignez les champs du formulaire et sélectionnez le bouton **Créer** permettant de créer un projet dans Workbench.

    | Champ | Valeur suggérée pour le didacticiel | Description |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nom du projet | myIris | Entrez un nom unique qui identifie le compte. Utilisez le nom d’un service ou d’un projet identifiant au mieux l’expérimentation. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. |
    | Répertoire du projet | c:\Temp\ | Spécifiez le répertoire dans lequel le projet est créé. |
    | Description du projet | laisser vide | Champ facultatif utile pour décrire les projets. |
    | URL du référentiel GIT de Visualstudio.com | laisser vide | Champ facultatif. Associez un projet à un répertoire Git sur Visual Studio Team Services pour le contrôle de code source et la collaboration. [Découvrez comment configurer un référentiel](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project). |
    | Espace de travail sélectionné | IrisGarden (s’il existe) | Choisissez un espace de travail créé pour le compte Expérimentation dans le portail Azure. <br>Avec le démarrage rapide, l’espace de travail nommé IrisGarden est répertorié. Sinon, utilisez l’espace de travail portant le nom du compte Expérimentation ou un nom de compte par défaut. |
    | Modèle de projet | Classifying Iris | Les modèles contiennent des scripts et des données utilisables pour découvrir le produit. Ce modèle contient les scripts et les données nécessaires pour ce démarrage rapide et les autres didacticiels du site de documentation. |

    ![Nouveau projet](media\azure-stack-solution-machine-learning\image15.png)

1.  Un projet est créé et le tableau de bord du projet s’ouvre avec ce projet. Explorez la page d’accueil du projet, les sources de données, les blocs-notes et les fichiers de code source.

    ![Ouvrir le projet](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>Joindre une cible de calcul DSVM

Une fois la machine DSVM créée, attachez-la au projet Azure ML.

1.  Dans l’application Azure ML Workbench, démarrez l’interface CLI Azure ML Workbench en sélectionnant **Fichier**->**Ouvrir PowerShell**

    ![Alt text](media\azure-stack-solution-machine-learning\image17.png)

1.  À l’invite de commandes PowerShell, entrez la commande suivante :

    ```PowerShell  
        az login
    ```

1.  L’invite suivante s’affiche :

     ![Alt text](media\azure-stack-solution-machine-learning\image18.png)

1.  Accédez au site comme indiqué dans l’invite de commandes et entrez le code fourni.

    ![Alt text](media\azure-stack-solution-machine-learning\image19.png)

1.  Cliquez sur Continuer lorsque vous y êtes invité, puis sélectionnez le compte Azure auquel le compte Expérimentation Azure ML est associé.

    ![Alt text](media\azure-stack-solution-machine-learning\image20.png)

1.  L’interface CLI Azure ML Workbench envoie ensuite l’invite suivante :

    ![Alt text](media\azure-stack-solution-machine-learning\image21.png)

1.  Une fois la connexion au compte ML et à l’espace de travail établie, attachez la machine DSVM.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    La notification suivante s’affiche :

    ![Alt text](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

Cette opération prend un certain temps et génère une quantité importante de texte lorsqu’elle extrait différentes images Docker, les inscrit et applique ensuite le code nécessaire et les applications.

Les expérimentations peuvent maintenant être exécutées sur cette machine DSVM.

### <a name="create-a-data-preparation-package"></a>Créer un package de préparation de données

Explorez et démarrez ensuite la préparation des données dans Azure Machine Learning Workbench. Chaque transformation effectuée dans Workbench est stockée au format JSON dans un package de préparation de données locales (fichier \*.dprep). Ce package de préparation des données est le conteneur principal du travail de préparation des données dans Workbench.

Ce package de préparation des données peut être transmis ultérieurement à un runtime, par exemple local-C\#/CoreCLR, Scala/Spark ou Scala/HDI.

1.  Sélectionnez l’icône Dossier pour ouvrir l’affichage des fichiers, puis sélectionnez le fichier **iris.csv** pour l’ouvrir.

    Ce fichier contient un tableau de 5 colonnes et 50 lignes. Quatre colonnes sont des colonnes de fonctionnalités numériques. La cinquième colonne est une colonne de cible de chaînes. Aucune colonne n’a de nom d’en-tête.

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  Dans la **Vue de données**, cliquez sur le signe « plus » (**+**) pour ajouter une nouvelle source de données. La page **Ajouter une source de données** s’ouvre.

    ![Afficher des données dans Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image24.png)

1.  Sélectionnez **Fichiers texte (\*.csv, \*.json, \*.txt, etc.)**.

    ![Source de données dans Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image25.png)

1.  Sélectionnez **Suivant**.

2.  Accédez au fichier **iris.csv**, puis sélectionnez **Terminer**. Les valeurs par défaut pour les paramètres seront utilisées, comme le type de données et du séparateur.

    > [!Important]  
    > Sélectionnez le fichier **iris.csv** dans le répertoire de projet actif pour cet exercice. Sinon, les étapes suivantes risquent d’échouer.

    ![Sélectionner iris](media\azure-stack-solution-machine-learning\image26.png)

1.  Le fichier `*iris-1.dsource` est créé. Ce fichier est nommé de façon unique à l’aide de `-1`, car l’exemple de projet est déjà fourni avec un fichier **iris.dsource** non numéroté.

    Le fichier s’ouvre et les données s’affichent. Une série d’en-têtes de colonne, de **Colonne1** à **Colonne5**, est automatiquement ajoutée à ce jeu de données. Si vous faites défiler les données vers le bas, vous noterez que la dernière ligne du jeu de données est vide. La ligne est vide, car le fichier CSV comporte un saut de ligne supplémentaire.

    ![Affichage de données Iris](media\azure-stack-solution-machine-learning\image27.png)

1.  Cliquez sur le bouton **Métriques**. Les histogrammes sont générés et affichés.

    Revenez à la vue de données en sélectionnant le bouton **Données**.

    ![Affichage de données Iris](media\azure-stack-solution-machine-learning\image28.png)

1.  Observez les histogrammes. Un ensemble complet de statistiques a été calculé pour chaque colonne.

    ![Affichage de données Iris](media\azure-stack-solution-machine-learning\image29.png)

1.  Commencez à créer un package de préparation des données en sélectionnant le bouton **Préparation**. La boîte de dialogue **Préparer** s’ouvre.

    L’exemple de projet contient un fichier de préparation des données **iris.dprep** sélectionné par défaut.

    ![Affichage de données Iris](media\azure-stack-solution-machine-learning\image30.png)

1.  Créez un package de préparation de données en sélectionnant **+ Nouveau package de préparation de données** à partir du menu.

    ![Affichage de données Iris](media\azure-stack-solution-machine-learning\image31.png)

1.  Entrez une nouvelle valeur pour le nom du package, utilisez **iris-1**, puis sélectionnez **OK**.

    Un package de préparation de données nommé **iris-1.dprep** est créé et ouvert dans l’éditeur de préparation des données.

    ![Affichage de données Iris](media\azure-stack-solution-machine-learning\image32.png)

    Il est ensuite nécessaire de préparer les données.

1.  Sélectionnez chaque en-tête de colonne pour activer la modification du texte d’en-tête. Ensuite, renommez chaque colonne comme suit :

    Dans l’ordre, entrez **Longueur des sépales**, **Largeur des sépales**, **Longueur des pétales**, **Largeur des pétales** et **Espèces** pour les cinq colonnes.

    ![Renommer les colonnes](media\azure-stack-solution-machine-learning\image33.png)

1.  Comptez les valeurs distinctes :

    1.  Sélectionnez la colonne **Espèces**

    2.  Cliquez avec le bouton droit pour la sélectionner.

    3.  Sélectionnez **Nombre de valeurs** dans le menu.

        Le volet **Inspecteurs** s’ouvre au-dessous des données. Un histogramme comprenant quatre barres s’affiche. La colonne cible comporte quatre valeurs distinctes : **Iris-virginica**, **Iris-versicolor**, **Iris-setosa** et une valeur **(null)**.

    ![Sélectionner les nombres de valeur](media\azure-stack-solution-machine-learning\image34.png)

    ![Nombre de valeurs de l’histogramme](media\azure-stack-solution-machine-learning\image35.png)

1.  Pour exclure les valeurs Null, sélectionnez la barre « (Null) », puis le signe moins (**-**).

    Ensute, la ligne (Null) devient grise pour indiquer qu’elle a été filtrée.

    ![Filtrer les valeurs Null](media\azure-stack-solution-machine-learning\image36.png)

1.  Prenez en compte les étapes de préparation de données individuelles qui sont décrites en détail dans le volet **ÉTAPES**. Une fois que les colonnes sont renommées et les lignes correspondant à une valeur null filtrées, chaque action est enregistrée comme une étape de préparation des données. Modifiez les étapes individuelles pour ajuster leurs paramètres, réorganiser les étapes et supprimer des étapes.

    ![Alt text](media\azure-stack-solution-machine-learning\image37.png)

1.  Fermez l’éditeur de préparation des données. Sélectionnez l’icône **x** sur l’onglet **iris-1** avec l’icône de graphique pour fermer l’onglet. Le travail est automatiquement sauvegardé dans le fichier **iris-1.dprep** sous le titre **Préparations des données**.

    ![fermez](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>Générer du code Python pour appeler un package de préparation de données

La sortie d’un package de préparation de données peut être explorée directement dans Python ou dans un Bloc-notes Jupyter. Les packages peuvent être exécutés sur plusieurs runtimes, notamment Python local, Spark (y compris dans Docker) et HDInsight.

1.  Recherchez le fichier **iris-1.dprep** dans l’onglet Préparation des données.

2.  Cliquez avec le bouton droit sur le fichier **iris-1.dprep**, puis sélectionnez **Générer un fichier de code d’accès aux données** dans le menu contextuel.

    ![Générer le code](media\azure-stack-solution-machine-learning\image39.png)

    Un nouveau fichier nommé **iris-1.py** s’ouvre et affiche les lignes de code suivantes pour appeler la logique créée en tant que package de préparation des données :

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    En fonction du contexte d’exécution du code, drep peut représenter différents types de tramedonnées :

    -  Lors de l’exécution sur un runtime Python, une [trame de données pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) est utilisée.

    -  Lors de l’exécution dans un contexte Spark, une [trame de données Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) est utilisée.

### <a name="review-irissklearnpy-and-the-configuration-files"></a>Examiner les fichiers de configuration et iris_sklearn.py

1.  Dans le projet ouvert, sélectionnez le bouton **Fichiers** (icône de dossier) dans le volet tout à gauche pour ouvrir la liste des fichiers dans le dossier de projet.

    ![Ouvrir le projet Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image40.png)

1.  Sélectionnez le fichier de script Python **iris_sklearn.py**.

    ![Choisir un script](media\azure-stack-solution-machine-learning\image41.png)

    Le code s’ouvre dans un nouvel onglet de l’éditeur de texte dans Workbench.

    > [!Note]  
    > Le code affiché peut différer légèrement du code précédent, car cet exemple de projet est fréquemment mis à jour.

    ![Ouvrir un fichier](media\azure-stack-solution-machine-learning\image42.png)

1.  Examinez le code de script Python pour vous familiariser avec le style de codage.

    Le script **iris_sklearn.py** effectue les tâches suivantes :  

    -  Il charge le package de préparation des données par défaut nommé **iris.dprep** pour créer un [cadre de données pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -   Il ajoute des caractéristiques aléatoires pour rendre le problème plus difficile à résoudre. Le caractère aléatoire est nécessaire, car Iris est un petit jeu de données qui est facilement classé avec près de 100 % de précision.

    -  Il utilise la bibliothèque de machine learning scikit-learn pour générer un modèle de régression logistique. Cette bibliothèque est fournie avec Azure Machine Learning Workbench par défaut.

    -  Il sérialise le modèle avec la bibliothèque [pickle](https://docs.python.org/3/library/pickle.html) dans un fichier dans le dossier `outputs`.

    -  Il charge le modèle sérialisé, puis le désérialise en mémoire.

    -  Il utilise le modèle désérialisé pour faire une prédiction sur un nouvel enregistrement.

    -  Il trace deux graphiques, une matrice de confusion et une courbe ROC multiclasses, à l’aide de la bibliothèque [matplotlib](https://matplotlib.org/), puis les enregistre dans le dossier outputs. Installez cette bibliothèque dans l’environnement si elle n’apparaît pas.

    -  Il trace automatiquement le taux de régularisation et la précision du modèle dans l’historique des exécutions. L’objet `run_logger` est utilisé pour enregistrer le taux de régularisation et la précision du modèle dans les journaux.

### <a name="run-irissklearnpy-in-the-local-environment"></a>Exécuter iris_sklearn.py dans l’environnement local

1.  Démarrez l’interface de ligne de commande Azure Machine Learning :

    1.  Lancez Azure Machine Learning Workbench.

    2.  Dans le menu Workbench, sélectionnez **Fichier**> **Ouvrir l’invite de commande**.

    La fenêtre de l’interface de ligne de commande Azure Machine Learning commence dans le dossier du projet `C:\Temp\\myIris\` sur Windows. Ce projet est le même que celui créé dans la première partie du didacticiel.

    > [!Important]  
    > Utilisez cette fenêtre d’interface CLI pour accomplir les étapes suivantes.

1.  Dans la fenêtre d’interface CLI, installez la bibliothèque de traçage Python, **matplotlib** si ce n’est pas déjà fait.

    Le script **iris_sklearn.py** possède des dépendances sur les deux packages Python : **scikit-learn** et **matplotlib**. Pour plus de simplicité, le package **scikit-learn** est installé par Azure Machine Learning Workbench. Cependant, il peut être nécessaire d’installer **matplotlib**.

    Si vous passez aux étapes suivantes sans installer **matplotlib**, le code dans ce didacticiel peut néanmoins s’exécuter correctement. Toutefois, le code ne pourra pas produire la sortie de la matrice de confusion et les tracés de courbe ROC multiclasses indiqués dans les visualisations de l’historique.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    Cette installation prend environ une minute.

1.  Revenez à l’application Workbench.

2.  Recherchez l’onglet appelé **iris_sklearn.py**.

    ![Rechercher l’onglet avec le script](media\azure-stack-solution-machine-learning\image43.png)

1.  Dans la barre d’outils de cet onglet, sélectionnez **local** comme environnement d’exécution, puis andiris_sklearn.pyas comme script à exécuter. Ils peuvent être déjà sélectionnés.

    ![Alt text](media\azure-stack-solution-machine-learning\image44.png)

1.  Passez à droite de la barre d’outils et entrez 0.01 dans le champ **Arguments**.

    Cette valeur correspond au taux de régularisation du modèle de régression logistique.

    ![Choix de l’option « local » et du script](media\azure-stack-solution-machine-learning\image45.png)

1.  Sélectionnez le bouton **Exécuter**. Un travail est planifié immédiatement. Le travail est répertorié dans le volet **Travaux** sur le côté droit de la fenêtre Workbench.

    ![Choix de l’option « local » et du script](media\azure-stack-solution-machine-learning\image46.png)

    Après quelques instants, l’état du travail passe de **Soumission** à **Exécution**, puis à **Terminé**.

1.  Sélectionnez **Terminé** dans le texte d’état du travail dans le volet **Travaux**.

    ![Exécuter sklearn](media\azure-stack-solution-machine-learning\image47.png)

    Une fenêtre indépendante s’ouvre et affiche le texte de sortie standard (stdout) du script en cours d’exécution. Pour fermer le texte stdout, sélectionnez le bouton **Fermer** (**x**) dans le coin supérieur droit de la fenêtre indépendante.

    ![Sortie standard](media\azure-stack-solution-machine-learning\image48.png)

1.  Dans le même état du travail au sein du volet **Travaux**, sélectionnez le texte en bleu **iris_sklearn.py \[n\] **(* n* représentant le numéro d’exécution) juste au-dessus de l’état **Terminé** et de l’heure de début. La fenêtre **Propriétés de l’exécution** s’ouvre et affiche les informations suivantes pour cette exécution particulière :

    -  Informations **Propriétés de l’exécution**

    -  **Sorties**

    -  **Métriques**

    -  **Visualisations**, le cas échéant

    -  **Journaux**

    Lorsque l’exécution est terminée, la fenêtre indépendante affiche les résultats suivants :

    > [!Note]  
    > Étant donné que nous avons introduit un degré de randomisation dans la formation définie précédemment, les résultats exacts peuvent varier des résultats présentés ici.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  Fermez l’onglet **Propriétés de l’exécution**, puis revenez à l’onglet **iris_sklearn.py**.

1.  Effectuez d’autres exécutions.

Dans le champ **Arguments**, entrez une série de valeurs comprises entre `0.001` et `10`. Sélectionnez **Exécuter** pour exécuter le code plusieurs fois encore. La valeur d’argument modifiée à chaque fois est transmise au modèle de régression logistique dans le code, ce qui entraîne systématiquement des résultats différents.

### <a name="review-the-run-history-in-detail"></a>Passer en revue l’historique des exécutions

Dans Azure Machine Learning Workbench, chaque exécution du script est capturée dans un enregistrement de l’historique des exécutions. Pour afficher l’historique des exécutions d’un script spécifique, ouvrez la vue **Exécutions**.

1.  Pour ouvrir la liste des **exécutions**, sélectionnez le bouton **Exécutions** (icône en forme d’horloge) dans la barre d’outils de gauche. Sélectionnez ensuite **iris_sklearn.py** pour afficher le **Tableau de bord des exécutions** de ofiris_sklearn.py.

    ![Vue de l’exécution](media\azure-stack-solution-machine-learning\image49.png)

1.  L’onglet **Tableau de bord des exécutions** s’ouvre.

    Passez en revue les statistiques capturées sur plusieurs exécutions. Les graphiques sont affichés en haut de l’onglet. Chaque exécution porte un numéro consécutif, et les détails de l’exécution sont répertoriés dans le tableau en bas de l’écran.

    ![Tableau de bord des exécutions](media\azure-stack-solution-machine-learning\image50.png)

1.  Filtrez la table, puis sélectionnez l’un des graphiques pour afficher l’état, la durée, la précision et le taux de régularisation de chaque exécution.

2.  Sélectionnez les cases à cocher en regard de deux ou plusieurs exécutions dans la table **Exécutions**. Sélectionnez le bouton **Comparer** pour ouvrir un volet de comparaison détaillée. Passez en revue la comparaison côte-à-côte.

3.  Pour revenir au **Tableau de bord des exécutions**, sélectionnez le bouton précédent **Liste des exécutions** dans le coin supérieur gauche du volet **Comparaison**.

    ![Retour à la liste des exécutions](media\azure-stack-solution-machine-learning\image51.png)

1.  Sélectionnez une exécution pour en afficher les détails. Notez que les statistiques de l’exécution sélectionnée sont répertoriées dans la section **Propriétés de l’exécution**. Les fichiers écrits dans le dossier de sortie sont répertoriés dans la section **Sorties** et vous pouvez télécharger les fichiers à partir de cette dernière.

    ![Détails de l’exécution](media\azure-stack-solution-machine-learning\image52.png)

Les deux tracés, la matrice de confusion et la courbe ROC multiclasses, apparaissent dans la section **Visualisations**. Tous les fichiers journaux se trouvent également dans la section **Journaux**.

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Exécuter des scripts dans la fenêtre CLI Azure Machine Learning (ML) Workbench

1.  Démarrez l’interface de ligne de commande Azure Machine Learning :

    1.  Lancez Azure Machine Learning Workbench.

    2.  Dans le menu Workbench, sélectionnez **Fichier** > **Ouvrir l’invite de commande**.

    L’invite de la CLI commence dans le dossier du projet `C:\\Temp\\myIris` sur Windows. Il s’agit du projet créé dans la première partie du didacticiel.

    > [!Important]  
    > Utilisez cette fenêtre d’interface CLI pour accomplir les étapes suivantes.

1.  Dans la fenêtre de l’interface de ligne de commande, connectez-vous à Azure. [En savoir plus sur az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    Ignorez cette étape si vous déjà connecté.

1.  À l’invite de commandes, tapez :

    ```CLI
        az login
    ```

    Cette commande renvoie un code à utiliser dans le navigateur à l’adresse [https://aka.ms/devicelogin](https://aka.ms/devicelogin).

1.  Accédez à [https://aka.ms/devicelogin](https://aka.ms/devicelogin) dans le navigateur. Entrez le code obtenu à partir de la réponse reçue dans l’interface CLI.

    L’application Workbench et l’interface CLI utilisent des caches d’informations d’identification indépendants pendant l’authentification auprès des ressources Azure. L’authentification n’est plus nécessaire jusqu’à l’expiration du jeton mis en cache.

1.  Si l’organisation a plusieurs abonnements Azure (environnement d’entreprise), définissez l’abonnement à utiliser. Recherchez l’abonnement, définissez-le à l’aide de l’ID d’abonnement, puis testez-le.

1.  Répertoriez chaque abonnement Azure accessible à l’aide de cette commande :

    ```CLI
        az account list -o table 
    ```

    La commande **az account list** retourne la liste des abonnements disponibles pour la connexion. S’il en existe plusieurs, identifiez la valeur d’ID de l’abonnement utilisé.

1.  Définissez l’abonnement Azure utilisé en tant que compte par défaut :

    ```CLI
        az account set -s <the-subscription-id
    ```

    où <the-subscription-id> désigne la valeur d’ID de l’abonnement utilisé. N’incluez pas les chevrons.

1.  Confirmez le nouveau paramètre d’abonnement en demandant les détails de l’abonnement actuel.

    ```CLI
        az account show
    ```

1.  Dans la fenêtre de l’interface de ligne de commande, envoyez le script **iris_sklearn.py** en tant qu’expérience.

    L’exécution du script iris_sklearn.py a lieu dans le contexte de calcul local.

1.  Sous Windows :

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  Sur macOS :

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    La sortie doit ressembler à ce qui suit : 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  Passez en revue la sortie. La sortie et les résultats doivent être les mêmes que lorsque Workbench exécute le script.

7.  Revenez à Workbench, et :

    Sélectionnez l’icône de dossier dans le volet gauche pour répertorier les fichiers projet.  Ouvrez le script Python nommé **run.py**. Ce script permet d’obtenir une boucle sur différents taux de régularisation. 

    ![Retourner à la liste des exécutions](media\azure-stack-solution-machine-learning\image53.png)

1.  Exécutez plusieurs fois l’expérience avec ces taux.

    Ce script démarre un travail ` aniris_sklearn.pyjob` avec un taux de régularisation de `10.0` (nombre extraordinairement élevé). Le script réduit alors le taux de moitié lors de l’exécution suivante, et ainsi de suite tant que le taux n’est pas inférieur à `0.005`. Le script contient le code suivant :

    ![Retour à la liste des exécutions](media\azure-stack-solution-machine-learning\image54.png)

1.  Exécutez le script **run.py** à partir de la ligne de commande, comme suit :

    ```CLI
        python run.py
    ```
Cette commande envoie `iris_sklearn.py` plusieurs fois avec des taux de régularisation différents

Lorsque `run.py` se termine, des graphiques apparaissent dans la vue de liste de l’historique des exécutions dans Workbench, avec plusieurs métriques.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Exécuter des scripts dans une machine DSVM (Data Science Virtual Machine) Ubuntu sur Azure

Pour exécuter le script dans un conteneur Docker sur une machine Linux distante, un accès SSH (nom d’utilisateur et mot de passe) à cette machine est requis. Le moteur Docker doit également être installé et en cours d’exécution sur cette machine.

1.  Modifiez le fichier généré <your DSVM Name>.runconfigfile sous aml_config et changez l’infrastructure en remplaçant la valeur par défaut PySpark par Python :

    ```yaml  
    Framework: Python
    ```
1.  Exécutez la même commande que celle exécutée dans la fenêtre d’interface CLI, mais cette fois en ciblant *<DSVM>* pour exécuter iris_sklearn.py dans un conteneur Docker distant. (Remplacez <DSVM> par le nom de la machine Data Science VM, sans les chevrons.)

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

La commande s’exécute comme dans un environnement docker-python, à la différence que l’exécution se produit sur la machine virtuelle Linux distante. La fenêtre CLI affiche les mêmes informations de sortie.

### <a name="download-the-model-pickle-file"></a>Télécharger le fichier pickle de modèle

Dans la partie précédente du didacticiel, le script **iris_sklearn.py** a été exécuté localement dans Machine Learning Workbench. Cette action a sérialisé le modèle de régression logistique à l’aide du package de sérialisation d’objets Python populaire [pickle](https://docs.python.org/3/library/pickle.html).

1.  Ouvrez l’application Machine Learning Workbench. Ouvrez ensuite le projet **myIris** créé dans les parties précédentes de la série de didacticiels.

2.  Une fois le projet ouvert, sélectionnez le bouton **Fichiers** (icône de dossier) dans le volet gauche pour ouvrir la liste des fichiers dans le dossier de projet.

3.  Sélectionnez le fichier **iris_sklearn.py**. Le code Python s’ouvre sous un nouvel onglet de l’éditeur de texte dans Workbench.

4.  Examinez le fichier **iris_sklearn.py** pour voir où le fichier pickle a été généré. Sélectionnez Ctrl+F pour ouvrir la boîte de dialogue **Rechercher**, puis recherchez le mot **pickle** dans le code Python.

Cet extrait de code montre comment le fichier de sortie pickle a été généré. Le fichier de sortie pickle est nommé **model.pkl** sur le disque.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  Recherchez le fichier pickle du modèle dans les fichiers de sortie d’une exécution antérieure.

    Lors de l’exécution du script **iris_sklearn.py**, le fichier de modèle est écrit dans le dossier **outputs** sous le nom **model.pkl**. Ce dossier se trouve dans l’environnement choisi pour exécuter le script et non dans le dossier de projet local. 

    1. Pour rechercher le fichier, sélectionnez le bouton **Exécutions** (icône d’horloge) sur le volet gauche afin d’ouvrir la liste **Toutes les exécutions**.  

    2. L’onglet **Toutes les exécutions** s’ouvre. Dans le tableau des exécutions, sélectionnez-en une récente qui avait pour cible **local** et pour nom de script **iris_sklearn.py**.  

    3. Le volet **Propriétés de l’exécution** s’ouvre. Dans la section supérieure droite du volet, notez la section **Sorties**. d\. Pour télécharger le fichier pickle, cochez la case en regard du fichier **model.pkl**, puis sélectionnez **Télécharger**. Enregistrez-le à la racine du dossier de projet. Le fichier est requis lors des étapes suivantes.  

    ![Télécharger le fichier pickle](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>Obtenir les fichiers de script de scoring et de schéma

Pour déployer le service web ainsi que le fichier de modèle, un script de scoring est requis. Il est possible qu’un schéma pour les données d’entrée du service web soit requis. Le script de notation charge le fichier **model.pkl** à partir du dossier actuel et l’utilise pour produire de nouvelles prédictions.

1.  Ouvrez l’application Machine Learning Workbench. Ouvrez ensuite le projet **myIris** créé dans la partie précédente de la série de didacticiels.

2.  Une fois le projet ouvert, sélectionnez le bouton **Fichiers** (icône de dossier) dans le volet gauche pour ouvrir la liste des fichiers dans le dossier de projet.

3.  Sélectionnez le fichier **score_iris.py**. Le script Python s’ouvre. Ce fichier est utilisé en guise de fichier de notation.

    ![Fichier de notation](media\azure-stack-solution-machine-learning\image56.png)

1.  Pour obtenir le fichier de schéma, exécutez le script. Sélectionnez l’environnement **local** et le script **score_iris.py** dans la barre de commandes, puis sélectionnez **Exécuter**.

    Ce script crée un fichier JSON dans la section **Sorties**, qui capture le schéma de données d’entrée requis par le modèle.

1.  Notez le volet **Travaux** situé à droite du volet **Tableau de bord du projet**. Attendez que le dernier travail **score_iris.py** affiche l’état vert **Terminé**. Sélectionnez ensuite le lien hypertexte **score_iris.py** de la dernière exécution du travail afin d’afficher les détails de l’exécution.

2.  Dans le volet **Propriétés de l’exécution**, dans la section **Sorties**, sélectionnez le fichier **service_schema.json** créé. Cochez la case en regard du nom de fichier, puis sélectionnez **Télécharger**. Enregistrez le fichier dans le dossier racine du projet.

3.  Revenez à l’onglet précédent et au script **score_iris.py**. Avec la collecte de données, il est possible de capturer des entrées de modèle et des prédictions à partir du service web. Les étapes suivantes sont intéressantes pour la collecte de données.

4.  Passez en revue le code en haut du fichier, qui importe la classe **ModelDataCollector**. Il contient la fonctionnalité de collecte de données de modèle :

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  Passez en revue les lignes de code suivantes dans la fonction **init()** qui instancie **ModelDataCollector** :

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  Passez en revue les lignes de code suivantes dans la fonction **run(input_df)** car elle collecte les données d’entrée et de prédiction :

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

À présent, préparez l’environnement pour opérationnaliser le modèle.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>Étape 5 : Déployer et utiliser Azure Container Registry

Déployer et utiliser Azure Container Registry.

Créez un registre de conteneurs Azure à l’aide de la commande **az acr create**. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Le groupe de ressources est identique.

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>Connexion au registre de conteneurs

Utilisez la commande **az acr login** pour vous connecter à l’instance ACR. Fournissez le nom unique qui a été donné au registre de conteneurs au moment de sa création.

    ```CLI
        az acr login --name <acrName>
    ```

Après son exécution, la commande retourne le message « Connexion réussie ».

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>Préparer l’opérationnalisation locale pour le développement et le test du service

Utilisez le déploiement *en mode local* pour effectuer une exécution dans des conteneurs Docker sur l’ordinateur local, ainsi que pour le développement et pour le test.

Le moteur Docker doit être en cours d’exécution localement pour que vous puissiez effectuer les étapes suivantes d’opérationnalisation du modèle. Utilisez l’indicateur `-h` à la fin de chaque commande pour afficher le message d’aide correspondant.

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  Ouvrez l’interface de ligne de commande (CLI). Dans l’application Machine Learning Workbench, dans le menu **Fichier**, sélectionnez **Ouvrir l’invite de commandes**.

    L’invite de ligne de commande s’ouvre dans l’emplacement actuel du dossier du projet **c:\\temp\\myIris**.

1.  Vérifiez que le fournisseur de ressources Azure **Microsoft.ContainerRegistry** est inscrit dans l’abonnement. Inscrivez ce fournisseur de ressources avant de créer un environnement à l’étape 3. Vérifiez s’il est déjà inscrit à l’aide de la commande suivante :

    ```CLI
        az provider list --query "\[\].{Provider:namespace, Status:registrationState}" --out table
    ```

    Affichez cette sortie :

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    Si **Microsoft.ContainerRegistry** n’est pas inscrit, utilisez la commande suivante :

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    L’inscription peut prendre quelques minutes. Vérifiez l’état d’inscription à l’aide de la commande **az provider list** précédente ou de la commande suivante :

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    La troisième ligne de la sortie affiche **"registrationState": "Registering"**. Attendez quelques instants et répétez la commande **show** jusqu’à ce que la sortie affiche **"registrationState": "Registered".**

1.  Créez l’environnement. Exécutez cette étape une fois par environnement.

    La commande de configuration suivante nécessite de disposer d’un accès Contributeur à l’abonnement. Un accès Contributeur au déploiement du groupe de ressources est requis. Spécifiez le nom du groupe de ressources dans la commande de configuration à l’aide de l’indicateur -g.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    Suivez les instructions à l’écran afin d’approvisionner un compte de stockage pour stocker les images Docker, un registre de conteneurs Azure qui répertorie les images Docker et un compte Azure Application Insights qui collecte les données de télémétrie. **Si vous utilisez l’élément -c, la commande crée automatiquement un cluster de service conteneur**.

    Le nom du cluster permet d’identifier l’environnement. L’emplacement doit être identique à celui du compte de Gestion des modèles créé à partir du portail Azure.

    Pour vous assurer que l’environnement est configuré correctement, utilisez la commande suivante pour vérifier l’état :

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    Vérifiez que la valeur du paramètre « État d’approvisionnement » est définie sur « Réussi » (comme indiqué) avant de définir l’environnement à l’étape 5 :

    ![État d’approvisionnement](media\azure-stack-solution-machine-learning\image57.png)

1.  Définissez l’environnement.

    Une fois la configuration terminée, utilisez la commande suivante pour définir les variables d’environnement nécessaires à l’opérationnalisation de l’environnement. Utilisez le même nom d’environnement que celui utilisé précédemment à l’étape 3. Utilisez le même nom de groupe de ressources généré dans la fenêtre de commande lorsque le processus d’installation s’est terminé.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  Pour vérifier la configuration de l’environnement mis en œuvre pour le déploiement du service web local, entrez la commande suivante :

    ```CLI
    az ml env show
    ```

    À présent, créez le service web en temps réel.

    > [!Note]  
    > Réutilisez le compte et l’environnement de Gestion des modèles pour les déploiements de service web suivants. Il n’est pas nécessaire de les créer pour chaque service web. Un compte ou un environnement peuvent disposer de plusieurs services web associés.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Créer un service web en temps réel à l’aide de plusieurs commandes

En guise d’alternative à la commande **az ml service create realtime** présentée précédemment, il est aussi possible d’effectuer ces étapes de manière séparée.

Tout d’abord, inscrivez le modèle. Générez ensuite le manifeste, générez l’image Docker, puis créez le service web. Cette approche étape par étape permet d’obtenir plus de flexibilité à chaque étape. En outre, les entités générées dans les étapes précédentes peuvent être réutilisées.

1. Inscrire le modèle en fournissant le nom de fichier pickle

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    Cette commande génère un ID de modèle.

2.  Créez un manifeste.

    Pour créer un manifeste, utilisez la commande suivante et indiquez l’ID de modèle issu de l’étape précédente : 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    Cette commande génère un ID de manifeste.

3.  Créez une image Docker.

    Pour créer une image Docker, utilisez la commande suivante et indiquez la valeur d’ID de manifeste issue de l’étape précédente. Les dépendances conda peuvent également être utilisées par le biais de l’élément `-c`. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    Cette commande génère un ID d’image Docker.

2.  Créez le service.

    Pour créer un service, utilisez la commande suivante et indiquez l’ID d’image issu de l’étape précédente : 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    Cette commande génère un ID de service web.
    
    Exécutez ensuite le service web.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>Étape 6 : Déployer un cluster Kubernetes sur Azure Stack

Déployer un cluster Kubernetes sur Azure Stack.

Il est possible d’installer Kubernetes à l’aide de modèles Azure Resource Manager générés par le moteur ACS (Azure Container Services) sur Azure Stack. [*Kubernetes*](https://kubernetes.io/) est un système open source permettant d’automatiser le déploiement, la mise à l’échelle et la gestion d’applications dans des conteneurs. Un [*conteneur*](https://www.docker.com/what-container) est contenu dans une image, tout comme une machine virtuelle. À la différence d’une machine virtuelle, l’image de conteneur inclut les ressources nécessaires pour exécuter une application, telles que le code, un runtime pour exécuter le code, des bibliothèques spécifiques et des paramètres.

Utilisez Kubernetes pour :

 -  Développer des applications hautement évolutives et extensibles, qui peuvent être déployées en quelques secondes.

 -  Simplifier la conception des applications et améliorer leur fiabilité à l’aide de différentes applications Helm. [*Helm*](https://github.com/kubernetes/helm) est un outil d’empaquetage open source permettant d’installer et de gérer le cycle de vie d’applications Kubernetes.

 -  Surveiller et diagnostiquer facilement l’intégrité des applications grâce à des fonctionnalités de mise à l’échelle et de mise à niveau.

> [!Note]  
> L’installation du cluster prend environ une heure. Planifiez donc l’opération de manière appropriée.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Conditions préalables pour le déploiement du cluster Kubernetes

Pour commencer, vérifiez les autorisations et la préparation d’Azure Stack :

1.  Vérifiez que l’article **Créer un cluster Kubernetes (préversion)** est disponible dans la Place de marché Azure Stack. Si cet article est manquant, faites appel à un opérateur Azure Stack pour l’ajouter dans l’environnement Azure Stack.

2.  Vérifiez que vous pouvez créer des applications dans le locataire Azure Active Directory (Azure AD). Des autorisations sont requises pour le déploiement de Kubernetes.

    Pour obtenir des instructions sur la vérification des autorisations, consultez [*Vérifier les autorisations Azure Active Directory*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

3.  Générez une paire de clés publique et privée SSH pour vous connecter à la machine virtuelle Linux sur Azure Stack. La clé publique est requise lors de la création du cluster. Pour obtenir des instructions, reportez-vous à [Générer une clé d’authentification pour le protocole SSH](#generate-an-authenticatio-key-for-ssh).

4.  Vérifiez que l’abonnement dans votre portail de client Azure Stack est valide et qu’un nombre suffisant d’adresses IP publiques est disponible pour l’ajouter d’applications.

    Vous ne pouvez pas déployer le cluster sur un abonnement **Administrateur** Azure Stack. Utilisez un abonnement **Utilisateur**.

###  <a name="generate-an-authentication-key-for-ssh"></a>Générer une clé d’authentification pour le protocole SSH

À partir du sous-système Windows pour la session Linux, utilisez les commandes suivantes afin de générer une clé d’authentification : 

1. Tapez :

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. Sélectionnez `id_rsa` lorsque vous y êtes invité. 
3. Créez un mot de passe lorsque vous y êtes invité. Il est important de noter ce mot de passe pour plus tard. 
    Le résultat ressemble à ce qui suit : 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Alt text](media\azure-stack-solution-machine-learning\image58.png)

4. Après avoir généré la clé, collez les informations de clé à l’aide des commandes suivantes : 
    ```Bash
    cat id_rsa.pub
    ```
    Le résultat ressemble à ce qui suit : 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. Copiez la clé générée dans le champ Clé publique SSH.

### <a name="create-a-service-principal-in-azure-ad"></a>Créer un principal de service dans Azure AD

1.  Connectez-vous au [*portail Azure*](http://www.poartal.azure.com/) global.

2.  Connectez-vous à l’aide du locataire Azure AD associé à l’instance Azure Stack.

3.  Créez une application Azure AD.

    1. Sélectionnez **Azure Active Directory** > **+ Inscriptions des applications**> **Nouvelle inscription d’application**.
    2. Entrez le **nom** de l’application. 
    3. Sélectionnez **Application web/API**. 
    4. Entrez l’**URL de connexion** `http://localhost`. 
    5. Sélectionnez **Créer**.

1.  Notez l’**ID d’application**. Cet ID est nécessaire lors de la création du cluster et il est référencé en tant qu’**ID client du principal du service**.

2.  Sélectionnez **Paramètres** > **Clés**.

    1. Entrez la **Description**. 
    2. Pour **Expire**, sélectionnez **N’expire jamais**. 
    3. Sélectionnez **Enregistrer**. Notez la chaîne de clé. Cette chaîne de clé est nécessaire lors de la création du cluster et elle est référencée en tant que **Clé secrète client du principal de service**.

### <a name="give-the-service-principal-access"></a>Accorder l’accès au principal de service

Accordez au principal du service l’accès à l’abonnement afin qu’il puisse créer des ressources.

1.  Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external/).

2.  Sélectionnez **Autres services** > **Abonnements utilisateur ** > **+ Ajouter**.

3.  Sélectionnez l’abonnement créé.

4.  Sélectionnez **Contrôle d’accès (IAM)** > **+ Ajouter**.

5.  Sélectionnez le rôle **Propriétaire**.

6.  Sélectionnez le nom de l’application créée pour le principal du service. Entrez le nom dans la zone de recherche si nécessaire.

7.  Sélectionnez **Enregistrer**.

8.  Ouvrez le [portail Azure Stack](https://portal.local.azurestack.external).

9.  Sélectionnez **+ Nouveau** > **Compute** > **cluster Kubernetes**. Sélectionnez **Créer**.

    ![Déployer un modèle de solution](media\azure-stack-solution-machine-learning\image59.png)

10\. Sélectionnez **Bases** dans le volet Créer un cluster Kubernetes.

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. Entrez le **nom de l’utilisateur administrateur de la machine virtuelle Linux**. Nom d’utilisateur pour les machines virtuelles Linux qui font partie du cluster Kubernetes et de DVM.

12. Entrez la **Clé publique SSH** utilisée pour l’autorisation sur toutes les machines Linux, créée en même temps que le cluster Kubernetes et DVM.

13. Entrez le **Préfixe DNS du profil principal** qui est unique pour la région. Il doit s’agir d’un nom unique pour la région, tel que `ask8s-12345`. Nous vous recommandons d’essayer de choisir le même préfixe que celui du nom du groupe de ressources.

    > [!Note]  
    > Pour chaque cluster, utilisez un préfixe DNS de profil principal nouveau et unique.

14. Entrez le **nombre de profils du pool d’agents**. Il s’agit du nombre d’agents dans le cluster. Il peut être compris entre 1 et 4.

15. Entrez **l’ID client du principal de service**. Celui-ci est utilisé par le fournisseur cloud d’Azure Kubernetes.

16. Entrez la **Clé secrète client du principal de service** créée en même temps que l’application du principal de service.

17. Entrez la **version du fournisseur cloud d’Azure Kubernetes**. Il s’agit de la version du fournisseur d’Azure Kubernetes. Azure Stack publie une build Kubernetes personnalisée pour chaque version d’Azure Stack.

18. Sélectionnez l’ID **Abonnement**.

19. Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules.

20. Sélectionnez **l’emplacement** du groupe de ressources. Il s’agit de la région choisie pour l’installation d’Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Spécifier les paramètres Azure Stack

1.  Sélectionnez les **Paramètres de tampon Azure Stack**.

    ![Déployer un modèle de solution](media\azure-stack-solution-machine-learning\image61.png)

2.  Entrez le **Point de terminaison Azure Resource Manager de locataire**. Il s’agit du point de terminaison Azure Resource Manager auquel se connecter pour créer le groupe de ressources pour le cluster Kubernetes. Le point de terminaison de l’opérateur Azure Stack est requis pour un système intégré. Pour le Kit de développement Azure Stack (ASDK), utilisez `https://management.local.azurestack.external`.

3.  Entrez **l’ID du locataire**. Consultez [*Obtenir l’ID de locataire*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) pour rechercher la valeur de l’ID de locataire.

### <a name="install-kubectl-on-windows-powershell-environment"></a>Installer kubectl sur l’environnement Windows PowerShell

À partir de l’environnement WSL, exécutez les commandes suivantes pour installer kubectl dans l’environnement WSL.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Installer kubectl sur le sous-système Windows pour l’environnement Linux

À partir de l’environnement WSL, exécutez les commandes suivantes pour installer kubectl dans l’environnement WSL.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>Configurer kubectl

Afin que kubectl recherche et accède à un cluster Kubernetes, un *fichier kubeconfig* est nécessaire. Il est créé automatiquement lors de la création d’un cluster à l’aide de kube-up.sh ou lors du déploiement d’un cluster Minikube. Consultez les [*guides de démarrage*](https://kubernetes.io/docs/setup/) pour en savoir plus sur la création des clusters. Pour accéder à un cluster créé par un autre utilisateur, consultez le [*document sur le partage de l’accès aux clusters*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). Par défaut, la configuration de kubectl se trouve sous **~.kube/config**.

### <a name="check-the-kubectl-configuration"></a>Vérifier la configuration de kubectl

Vérifiez que kubectl est correctement configuré en obtenant l’état du cluster :

```Bash  
kubectl cluster-info
```

kubectl est correctement configuré pour accéder au cluster lorsque la réponse d’URL s’affiche.

kubectl n’est pas correctement configuré ou pas en mesure de se connecter à un cluster Kubernetes si le message suivant s’affiche :

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

Par exemple, lorsque vous exécutez un cluster Kubernetes sur un ordinateur portable local, un outil peut être requis (minikube ou autre) pour réexécuter les commandes mentionnées ci-dessus.

Si kubectl cluster-info retourne la réponse d’URL mais si le cluster reste inaccessible, vérifiez la configuration à l’aide de :

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>Autoriser l’auto-complétion de l’interpréteur de commandes

kubectl inclut la prise en charge de l’auto-complétion, ce qui rend l’activation de l’interpréteur de commandes simple et rapide.

Le script de complétion lui-même est généré par kubectl et il est accessible à partir du profil.

### <a name="connect-to-the-kubernetes-cluster"></a>Se connecter au cluster Kubernetes

Pour se connecter au cluster, le client de ligne de commande Kubernetes (**kubectl**) est requis. Les instructions de connexion au cluster et de gestion de ce dernier se trouvent dans la [documentation Azure Container Service](https://docs.microsoft.com/azure/container-service/dcos-swarm/).

N’importe quel client SSH peut être utilisé pour la connexion du cluster Kubernetes. Le sous-système Windows pour Linux (WSL) est recommandé. L’ordinateur connecté au cluster Kubernetes se trouve dans le groupe de ressources créé pour le cluster et commence par le préfixe vmd-edge-ml-stack.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Une fois connecté à l’ordinateur Kubernetes, exécutez l’ordinateur suivant pour obtenir le fichier de configuration Kubernetes.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

Vous obtenez un résultat semblable à ce qui suit :

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Copiez ces informations de chemin d’accès de fichiers, puis exécutez la commande suivante, en collant dans le chemin d’accès du fichier kubeconfig copié ci-dessus :

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

La sortie prend la forme d’un grand bloc de texte qui correspond au contenu JSON brut. Copiez ce texte, puis collez le code dans un fichier Visual Studio, et enregistrez-le en tant que fichier JSON. Cela génère un fichier kubeconfig.json qui est stocké localement. (enregistrez-le dans/mnt/c/users/<current user>/documents/Kube directory sous le nom kubeconfig.json)

### <a name="configure-the-kubernetes-cluster"></a>Configurer le cluster Kubernetes

Une fois que le fichier JSON local est obtenu, dans une nouvelle session WSL, utilisez les commandes suivantes pour configurer le cluster.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

Les paramètres de configuration de Kubernetes seront définis (voir la sortie ci-dessous).

![Alt text](media\azure-stack-solution-machine-learning\image62.png)

Démarrez le service de proxy local :

```Bash  
kubectl proxy
```

Accédez à l’interface utilisateur du cluster kubernetes à l’adresse suivante : `https://localhost:8001`.

![Alt text](media\azure-stack-solution-machine-learning\image63.png)

Vous disposez maintenant d’un emplacement pour déployer le conteneur, et d’un conteneur qui se trouve dans le cloud visible en local.

![Alt text](media\azure-stack-solution-machine-learning\image64.png)

Personnalisez le fichier **iris_deployment.yaml** (situé dans le répertoire /*mnt/c/users/<current user>/documents/Kube*) afin que **webservicename** et les conteneurs **Image** et **Name** correspondent au déploiement, à l’aide de l’éditeur de code de votre choix.

![Alt text](media\azure-stack-solution-machine-learning\image65.png)

Définissez le port du conteneur **5001.**

![Alt text](media\azure-stack-solution-machine-learning\image66.png)

Puis créez **imagePullSecret** :

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>Créer un secret dans le cluster qui contient le jeton d’autorisation

Un cluster Kubernetes utilise le secret du type **docker-registry** pour l’authentification auprès d’un registre de conteneurs afin d’extraire une image privée.

Créez ce secret et nommez-le **azuremlcr** :

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

Recherchez :

- **<your-registry-server>** correspond au **serveur de connexion** Azure Container Registry.
- **<your-name>** correspond au **nom d’utilisateur** Azure Container Registry.
- **<your-pword>** correspond au **mot de passe** Azure Container Registry. Vérifiez que le mot de passe se trouve entre guillemets.
- **<your-email>** correspond à l’utilisateur qui a accès en lecture/écriture au conteneur.
- Recherchez ces informations sur**Azure Container** **Registry**, sous **Clés d’accès**.
- Les informations d’identification Docker sont maintenant définies dans le cluster dans le secret nommé **azuremlcr**.

Enregistrez le fichier **iris_deployment.yaml** (situé dans le répertoire /*mnt/c/users/<current user>/documents/Kube*).

### <a name="create-the-kubernetes-container"></a>Créer le conteneur Kubernetes

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

Vérifiez l’état du déploiement :

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

Le déploiement peut prendre un certain temps.

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>Configurer Visual Studio Team Services pour un déploiement automatique

#### <a name="create-a-team-project"></a>Créer un projet d’équipe

1.  Vérifiez [l’appartenance au groupe Administrateurs de la collection de projets.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) Pour créer des projets d’équipe, l’autorisation **Créer des projets** doit être définie sur **Autoriser**.

2.  Sur la page Projets, sélectionnez **Nouveau projet**.

    ![Alt text](media\azure-stack-solution-machine-learning\image69.png)

1.  Nommez le projet **HybridMLIris**.

2.  Sélectionnez le type de contrôle de code source initial **Git**

3.  Sélectionnez un processus, puis sélectionnez **Créer**.

    ![Alt text](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>Importer du code pour la création de référentiel

Un référentiel Git pour le code YAML est requis.

#### <a name="add-user-to-the-git-repo"></a>Ajouter un utilisateur dans le référentiel GIT

1.  Dans le tableau de bord de projet par défaut, sélectionnez Générer des informations d’identification Git.

    ![Alt text](media\azure-stack-solution-machine-learning\image71.png)

1.  Entrez le mot de passe requis et sélectionnez Enregistrer les informations d’identification Git.

    ![Alt text](media\azure-stack-solution-machine-learning\image72.png)

1.  Initialisez le référentiel en sélectionnant le bouton **Initialiser** et en créant un fichier **LISEZ-MOI**.

    ![Alt text](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Clonez le référentiel Git localement et chargez le code. 

1.  Créez un répertoire dans l’ordinateur à l’adresse `c:\\users\\<User>\\source\\repos\\hybridMLIris`, puis clonez le référentiel

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image74.png)

1.  Accédez au référentiel nouvellement cloné :

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  Copiez le fichier **iris_deployment.yaml** dans le référentiel.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  Validez la modification dans GIT

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>Préparer l’agent privé de build et de mise en production pour l’intégration de VSTS

#### <a name="prerequisites"></a>Prérequis

VSTS s’authentifie auprès d’Azure Resource Manager à l’aide d’un principal de service. Pour que VSTS puisse provisionner des ressources dans un abonnement Azure Stack, l’état Contributeur lui est nécessaire.\ **Voici les principales étapes de configuration qui permettent une telle authentification :**

1.  Vous devez créer un principal du service ou en utiliser un existant.

2.  Vous devez créer des clés d’authentification pour le principal du service.

3.  L’abonnement Azure Stack doit être validé par le biais du contrôle d’accès en fonction du rôle pour autoriser le SPN à faire partie du rôle Contributeur.

4.  Vous devez créer une nouvelle définition de service dans VSTS avec les points de terminaison Azure Stack ainsi que les informations du SPN.

#### <a name="service-principal-creation"></a>Création du principal du service

Reportez-vous aux [instructions de création du principal du service](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) pour créer un principal du service et choisissez le type d’application Application/API web.

**Création de la clé d’accès**

Un principal du service nécessite une clé pour l’authentification. Suivez les étapes de cette section pour générer une clé.

1.  Sous **Inscriptions d’applications** dans Azure Active Directory, sélectionnez l’application.

    ![sélectionner une application](media\azure-stack-solution-machine-learning\image77.png)

1.  Notez la valeur de l’**ID d’application. Cette valeur est utilisée lors de la configuration du point de terminaison de service dans VSTS.**

    ![Alt text](media\azure-stack-solution-machine-learning\image78.png)

1.  Pour générer une clé d’authentification, sélectionnez **Paramètres**.

    ![sélectionner paramètres](media\azure-stack-solution-machine-learning\image79.png)

1.  Sélectionnez **Clés**.

    ![sélectionner des clés](media\azure-stack-solution-machine-learning\image80.png)

1.  Fournissez une description de la clé et la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

    ![enregistrer une clé](media\azure-stack-solution-machine-learning\image81.png)

Après avoir enregistré la clé, la valeur de la clé s’affiche. Copiez cette valeur pour plus tard. La **valeur de la clé** avec l’ID d’application est requise pour une connexion en tant qu’application. Stockez la valeur de la clé à un emplacement où l’application peut la récupérer.

![Alt text](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>Obtenir l’ID de locataire

Dans le cadre de la configuration du point de terminaison de service, VSTS nécessite l’**ID de locataire** qui correspond à l’annuaire AAD sur lequel la pile Azure Stack a été déployée. Suivez les étapes de cette section pour récupérer l’ID de locataire.

1.  Sélectionnez **Azure Active Directory**.

    ![sélectionner azure active directory](media\azure-stack-solution-machine-learning\image83.png)

1.  Pour obtenir l’ID de locataire, sélectionnez l’option **Propriétés** correspondant au locataire Azure AD.

    ![Sélectionnez les propriétés Azure AD](media\azure-stack-solution-machine-learning\image84.png)

1.  Copiez l’**ID Directory**. Cette valeur est l’ID de locataire.

    ![ID client](media\azure-stack-solution-machine-learning\image85.png)

Accorder au principal du service les droits nécessaires pour déployer des ressources dans l’abonnement Azure Stack

Pour accéder aux ressources de l’abonnement, assignez un rôle à l’application. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Définissez l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue Par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources lui permet de lire le groupe de ressources et toutes les ressources qu’il contient.

1.  Accédez au niveau d’étendue souhaité pour assigner l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**.

    ![Alt text](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  Sélectionnez l’**abonnement** (groupe de ressources ou ressource) auquel assigner l’application.

    ![sélectionner l’abonnement pour l’affectation](media\azure-stack-solution-machine-learning\image87.png)

1.  Sélectionnez **Contrôle d’accès (IAM)**.

    ![sélectionner l’accès](media\azure-stack-solution-machine-learning\image88.png)

1.  Sélectionnez **Ajouter**.

    ![sélectionner ajouter](media\azure-stack-solution-machine-learning\image89.png)

1.  Sélectionnez le rôle pour assigner l’application. L’image suivante montre le rôle **Propriétaire**.

    ![Alt text](media\azure-stack-solution-machine-learning\image90.png)

1.  Par défaut, les applications Azure Active Directory ne figurent pas dans les options disponibles. Pour rechercher l’application, **indiquez son nom** dans le champ de recherche, puis sélectionnez-la.

    ![Alt text](media\azure-stack-solution-machine-learning\image91.png)

1.  Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle. L’application apparaît dans la liste des utilisateurs assignés à un rôle pour cette étendue.

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure et Azure Stack. Avec RBAC, seuls les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail peuvent être accordés. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Gérer l’accès aux ressources d’un abonnement Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

**Pools d’agents VSTS**

Au lieu de gérer chaque agent individuellement, les agents sont organisés en **pools d’agents**. Un pool d’agents définit la limite de partage pour tous les agents de ce pool. Dans VSTS, les pools d’agents sont délimités au niveau du compte VSTS : il est ainsi possible de partager un pool d’agents entre plusieurs projets d’équipe. Pour plus d’informations et un didacticiel sur la création de pools d’agents VSTS, consultez [Create Agent Pools and Queues](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts) (Créer des pools d’agents et des files d’attente).

**Ajouter un jeton d’accès personnel (PAT) pour Azure Stack**

 -  Connectez-vous à votre compte VSTS et sélectionnez le nom du **profil de compte**.

 -  Sélectionnez **Gérer la sécurité** pour accéder à la page de création d’un jeton d’accès.

![Alt text](media\azure-stack-solution-machine-learning\image92.png)

![Alt text](media\azure-stack-solution-machine-learning\image93.jpeg)

![Alt text](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> Récupérez les informations du jeton. Ces informations ne seront plus affichées une fois que vous aurez quitté cet écran.

1.  Copiez le **jeton**.

    ![Alt text](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installer l’agent de build VSTS sur le service Azure Stack hébergé par le serveur de build

1.  Connectez-vous au **serveur de build** déployé sur l’hôte Azure Stack.

    > [!Note]  
    > Vérifiez que le serveur de build hébergé Azure Stack est accessible à partir des réseaux Internet publics. Si ce n’est pas le cas, faites appel à un opérateur Azure Stack pour obtenir cet accès.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Mettez à niveau le serveur de build Ubuntu vers la version la plus récente (18.04) :

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > Cette opération prend un certain temps.

2.  Installez les applications prérequises pour le serveur de build. À partir de l’interpréteur de commandes Bash du serveur de build Ubuntu, exécutez les commandes suivantes :

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  Téléchargez et déployez l’agent de build en tant que service avec un **jeton d’accès personnel (PAT)**, puis exécutez-le en tant que compte d’administrateur de la machine virtuelle.

    ![Alt text](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  Accédez au dossier de l’agent de build extrait. Exécutez le code suivant.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image97.png)

2.  Une fois l’opération **./config.sh** terminée, exécutez le code suivant pour activer le service au démarrage du serveur, puis démarrez le service :

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

L’agent est désormais visible dans le dossier VSTS.

#### <a name="endpoint-creation-permissions"></a>Autorisations de création des points de terminaison

Les utilisateurs peuvent créer des points de terminaison pour que les builds VSTO puissent déployer des applications Azure App Service sur la pile. VSTS se connecte à l’agent de build, qui se connecte alors à Azure Stack.

![Alt text](media\azure-stack-solution-machine-learning\image98.png)

1.  Dans le menu **Paramètres**, sélectionnez **Sécurité**.

2.  Dans la liste **Groupes VSTS** sur la gauche, sélectionnez **Créateurs de points de terminaison**.

    ![Alt text](media\azure-stack-solution-machine-learning\image99.png)

3.  Sous **l’onglet Membres**, sélectionnez **+Ajouter**.

    ![Alt text](media\azure-stack-solution-machine-learning\image100.png)

1.  Entrez le **nom d’utilisateur** et sélectionnez le nom d’utilisateur dans la liste.

2.  Sélectionnez **Enregistrer les modifications**.

    ![Alt text](media\azure-stack-solution-machine-learning\image101.png)

3.  Dans la liste **Groupes VSTS** sur la gauche, sélectionnez **Administrateurs de points de terminaison**.

4.  Sous **l’onglet Membres**, sélectionnez **+Ajouter**.

5.  Entrez un **nom d’utilisateur** et sélectionnez cet utilisateur dans la liste.

6.  Sélectionnez **Enregistrer les modifications.**

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    L’agent de build dans Azure Stack reçoit des instructions de VSTS, qui transmet ensuite les informations des points de terminaison pour la communication avec Azure Stack.

    La connexion VSTS à Azure Stack est prête.

    ![Alt text](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>Configurer des définitions de build et de mise en production

Maintenant que les connexions sont établies, vous pouvez mapper manuellement le point de terminaison Azure créé, AKS et Azure Container Registry aux définitions de build et de mise en production.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>Créer la définition de build pour le code YAML

1.  Sélectionnez la section Builds sous le hub Build et Mise en production, puis créez une définition.

    ![Alt text](media\azure-stack-solution-machine-learning\image104.png)

1.  Sélectionnez VSTS Git, puis le référentiel créé précédemment.

    ![Alt text](media\azure-stack-solution-machine-learning\image105.png)

1.  Sélectionner un pipeline vide comme modèle.

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  Nommez la build **Copy Artifact** et sélectionnez le serveur de build Azure Stack pour la file d’attente d’agents.

    ![Alt text](media\azure-stack-solution-machine-learning\image107.png)

1.  Sélectionnez la Phase 1 dans les processus et renommez-la **Copy Artifact**, puis **ajoutez une tâche** à la phase :

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

1.  Sélectionnez **Publier les artefacts de build** dans la liste **Utilitaire**, puis sélectionnez **Ajouter**.

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  Sélectionnez le **chemin de publication**, puis le fichier **iris_deployment.yaml**.

    ![Alt text](media\azure-stack-solution-machine-learning\image110.png)

1.  Nommez l’artefact **iris_deployment** et sélectionnez l’emplacement de publication **Visual Studio Team Services/TFS**.

    ![Alt text](media\azure-stack-solution-machine-learning\image111.png)

1.  Sélectionnez **Enregistrer et mettre en file d’attente**.

    ![Alt text](media\azure-stack-solution-machine-learning\image112.png)

1.  Vérifiez l’état de la build en sélectionnant l’ID de build.

    ![Alt text](media\azure-stack-solution-machine-learning\image113.png)

Si l’opération a réussi, un écran du type suivant s’affiche :

![Alt text](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>Créer la définition de mise en production pour le code YAML

1.  Sélectionnez la section Mises en production sous le hub Build et Mise en production, puis créez une définition.

    ![Alt text](media\azure-stack-solution-machine-learning\image115.png)

1.  Sélectionnez un pipeline vide comme modèle.

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  Nommez l’environnement Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image116.png)

1.  Ajoutez un artefact en sélectionnant **Artefacts**, puis **+Ajouter**

2.  Sélectionnez Build comme type de source et **HybridMLIris** comme projet.

3.  Puis sélectionnez la définition de build créée précédemment pour la source.

4.  Sélectionnez ensuite **Ajouter**.

    ![Alt text](media\azure-stack-solution-machine-learning\image117.png)

1.  Sélectionnez Azure Stack dans les environnements, puis ajoutez une nouvelle tâche à Azure Stack

    ![Alt text](media\azure-stack-solution-machine-learning\image118.png)


1.  Sur la phase Agent, définissez la file d’attente d’agents sur le serveur de build hébergé Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image119.png)

1.  Ajoutez une tâche à cette phase, sélectionnez la tâche Déployer sur Kubernetes sous Déployer, puis sélectionnez Ajouter.

    ![Alt text](media\azure-stack-solution-machine-learning\image120.png)


1.  Nommez-la **Kubectl Apply** (nom par défaut) et sélectionnez la commande apply.

    ![Alt text](media\azure-stack-solution-machine-learning\image121.png)

    Créez à présent une connexion de service Kubernetes.

#### <a name="create-kubernetes-service-endpoint"></a>Créer un point de terminaison de service Kubernetes

1.  Sous Connexion de service Kubernetes, sélectionnez le bouton **+ Nouveau**, puis sélectionnez **Kubernetes** dans la liste. Vous pouvez utiliser ce point de terminaison pour connecter **VSTS** et **Azure Container Service (AKS)**.

2.  **Nom de la connexion** : indiquez le nom de la connexion.

3.  **URL du serveur** : indiquez l’adresse du service de conteneur au format http://{adresse de serveur API}

4.  **Kubeconfig** : pour obtenir la valeur Kubeconfig, exécutez les commandes Azure suivantes dans une invite de commandes lancée avec le privilège Administrateur.

    > [!Important]  
    > Utilisez cette fenêtre d’interface CLI pour accomplir les étapes suivantes.

6.  Dans la fenêtre de l’interface de ligne de commande, connectez-vous à Azure. [En savoir plus sur az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

7.  À l’invite de commandes, tapez :

    ```CLI
        az login
    ```

10. Cette commande renvoie un code à utiliser dans le navigateur à l’adresse <https://aka.ms/devicelogin>.

11. Accédez à <https://aka.ms/devicelogin> dans le navigateur. Lorsque vous y êtes invité, entrez dans votre navigateur le code que vous avez reçu dans l’interface CLI.

    ![Point de terminaison de service Kubernetes](media\azure-stack-solution-machine-learning\image122.png)

1.  Entrez la commande suivante dans l’invite de commandes pour obtenir les informations d’identification d’accès au cluster Kubernetes.

### <a name="azure-ml-workbench-cli"></a>Interface CLI Azure ML Workbench

az aks get-credentials resource-group <yourResourceGroup> name <yourazurecontainerservice>

![Point de terminaison de service Kubernetes](media\azure-stack-solution-machine-learning\image123.png)

1.  Accédez au dossier **.kube** sous le répertoire de base (par exemple : C:\\Users\\<user>\\Documents\\Kube)

2.  Copiez le contenu du fichier **config** et collez-le dans la fenêtre de connexion Kubernetes. Sélectionnez le bouton **OK**.

    ![Point de terminaison de service Kubernetes](media\azure-stack-solution-machine-learning\image124.png)
    

3.  Une fois que le point de terminaison Kubernetes est créé et sélectionné, sélectionnez la case à cocher Utiliser les fichiers config pour ajouter un fichier de configuration. Puis accédez au fichier iris_deployment.yaml dans Artefacts liés.

    ![Alt text](media\azure-stack-solution-machine-learning\image125.png)

    ![Alt text](media\azure-stack-solution-machine-learning\image126.png)

4.  Enregistrez la définition de version.

#### <a name="check-the-status-of-the-release-definition"></a>Vérifiez l’état de la définition de mise en production. 

Une fois la définition de mise en production enregistrée, VSTS doit lancer automatiquement une mise en production.

Vérifiez l’état du déploiement en exécutant une commande rapide dans l’invite de commandes WSL, puis en vérifiant l’IU Kubernetes.

```Bash  
kubectl get deployments
```

La sortie doit ressembler à ce qui suit lors du processus de déploiement.

![Alt text](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

Une fois que l’UI Kubernetes est en cours d’exécution, accédez au déploiement à l’adresse [**https://localhost:8001/**](https://localhost:8001/), puis accédez à **Charges de travail > Replica Sets (Jeux de réplicas)**.

![Alt text](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>Déployer le service YAML

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>Charger le fichier iris_service.yaml dans le référentiel et synchroniser les modifications

1.  Accédez au référentiel nouvellement cloné :

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  Copiez le fichier **iris_service.yaml** dans le référentiel.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  Validez la modification dans GIT

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Alt text](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>Mettre à jour la définition de build pour le code YAML

1.  Sélectionnez la section Builds sous le hub Build et Mise en production, puis sélectionnez la définition créée précédemment.

    ![Alt text](media\azure-stack-solution-machine-learning\image130.png)

2.  Sélectionnez le bouton Modifier pour modifier la définition.

    ![Alt text](media\azure-stack-solution-machine-learning\image131.png)

3.  **Ajoutez une tâche** à la phase. Sélectionnez **Publier les artefacts de build** dans la liste **Utilitaire**, puis sélectionnez **Ajouter**.

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

4.  Nommez-la **Kubectl Apply** (nom par défaut) et sélectionnez la commande apply.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>Mettre à jour la définition de mise en production pour le code YAML

1.  Sélectionnez la section Mises en production sous le hub Build et Mise en production, puis sélectionnez la définition de mise en production créée précédemment. Sélectionnez ensuite Modifier un lien.

    ![Alt text](media\azure-stack-solution-machine-learning\image132.png)

1.  Sélectionnez l’environnement **Azure Stack**, puis ajoutez une nouvelle tâche à Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image133.png)

1.  Ajoutez une **tâche** à cette phase, sélectionnez la tâche **Déployer sur Kubernetes** sous **Déployer**, puis sélectionnez **Ajouter**.

    ![Alt text](media\azure-stack-solution-machine-learning\image134.png)

1.  Nommez-la **Kubectl Apply** (nom par défaut) et sélectionnez la commande apply.

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  Définissez la connexion Kubernetes Service sur la connexion Azure Stack créée précédemment, puis cochez la case **Utiliser les fichiers config** pour ajouter un fichier de configuration. Accédez au fichier iris_service.yaml dans Artefacts liés.

    ![Alt text](media\azure-stack-solution-machine-learning\image135.png)


    ![Alt text](media\azure-stack-solution-machine-learning\image136.png)

1.  Enregistrez la définition de version.

#### <a name="check-the-status-of-the-release-definition"></a>Vérifier l’état de la définition de mise en production

Une fois la définition de mise en production enregistrée, VSTS doit lancer automatiquement une mise en production.

Vérifiez l’état du déploiement en exécutant une commande rapide dans l’invite de commandes WSL, puis en vérifiant l’IU Kubernetes.

```Bash  
kubectl get deployments
```

La sortie doit ressembler à ce qui suit lors du processus de déploiement.

![Alt text](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

Une fois que l’UI Kubernetes est en cours d’exécution, accédez au déploiement à l’adresse [**https://localhost:8001/**](https://localhost:8001/), puis accédez à **Charges de travail > Replica Sets (Jeux de réplicas)**.

![Alt text](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Scoring et validation Kubernetes

Lancez l’interface utilisateur Kubernetes.

```Bash  
kubectl proxy
```

Accédez à l’interface utilisateur Kubernetes, puis accédez à **Déploiements** -> **Iris-Deployment** -> **New Replica Set (Nouveau jeu de réplicas)**  ->  **Iris-Deployment-xxxxxxxxx** (où x représente l’ID de déploiement).

![Alt text](media\azure-stack-solution-machine-learning\image138.png)

Accédez ensuite à **Services** et sélectionnez le **point de terminaison externe** du service afin de valider son bon fonctionnement.

![Alt text](media\azure-stack-solution-machine-learning\image139.png)

Un message de validation semblable à ce qui suit doit s’afficher :

![Alt text](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Créer une application de fonction de scoring Azure Stack dans le portail Azure Stack

Une application de fonction est nécessaire pour héberger l’exécution de chaque fonction. Une application de fonction permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

1.  À partir du portail utilisateur Azure Stack, sélectionnez le bouton **+ Nouveau** en haut à gauche, puis sélectionnez **Web + Mobile** >**Application de fonction**.

    ![Alt text](media\azure-stack-solution-machine-learning\image141.png)

1.  Nommez la fonction **data-functions** et placez-la dans le même groupe de ressources que le contenu Machine Learning restant. Laissez l’outil créer automatiquement un plan App Service pour la consommation et l’utilisation du compte de stockage créé précédemment concernant le stockage de l’application.

    ![Définir de nouveaux paramètres d’application de fonction](media\azure-stack-solution-machine-learning\image142.png)

1.  Sélectionnez **Créer** pour configurer et déployer l’application de fonction.

2.  Cliquez sur l’icône Notification en haut à droite du portail pour voir le message **Le déploiement a été effectué**.

    ![Définir de nouveaux paramètres d’application de fonction](media\azure-stack-solution-machine-learning\image143.png)

1.  Sélectionnez **Accéder à la ressource** pour afficher la nouvelle application de fonction.

    ![Alt text](media\azure-stack-solution-machine-learning\image144.png)

1.  Créez une fonction en sélectionnant **Fonctions**, puis le bouton **+ Nouvelle fonction**.

    ![Alt text](media\azure-stack-solution-machine-learning\image145.png)

1.  Sélectionnez le déclencheur HTTP

    ![Alt text](media\azure-stack-solution-machine-learning\image146.png)

1.  Sélectionnez le langage **C\#** et nommez la fonction **clean-score-data**, puis définissez le niveau d’autorisation **Anonyme**.

    ![Alt text](media\azure-stack-solution-machine-learning\image147.png)

1.  Copiez et collez le contenu de l’exemple de code pour clean-score-data dans la fonction.

    ![Alt text](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Utiliser Postman pour valider les fonctions

Pour vous assurer que vous avez correctement configuré Kubernetes et Functions, vous pouvez utiliser l’application gratuite Postman pour tester et valider des schémas et des fonctions. Pour entamer ce processus, vous devez d’abord collecter des informations à partir de votre instance Kubernetes.

1.  Accédez à l’interface utilisateur Kubernetes, puis à **Déploiements** -> **Iris-Deployment** -> **New Replica Set (Nouveau jeu de réplicas)**  ->  **Iris-Deployment-xxxxxxxxx** (où x représente l’ID de déploiement)

    ![Alt text](media\azure-stack-solution-machine-learning\image138.png)

1.  Accédez ensuite à **Services** et copiez le **point de terminaison externe**.

    ![Alt text](media\azure-stack-solution-machine-learning\image149.png)

1.  Téléchargez et installez l’application Postman [ici](https://www.getpostman.com/apps) si nécessaire.

2.  Connectez-vous à l’application Postman et fermez la boîte de dialogue Nouveau fichier.

    ![Alt text](media\azure-stack-solution-machine-learning\image150.png)

1.  Dans l’application Postman, sélectionnez POST (Publier).

    ![Alt text](media\azure-stack-solution-machine-learning\image151.png)

1.  Collez l’URL du **point de terminaison externe** dans l’application Postman sous l’**URL de la demande**, en ajoutant  **\\score** à la fin de l’URL, comme indiqué ci-dessous.

    ![Alt text](media\azure-stack-solution-machine-learning\image152.png)

1.  Sélectionnez l’onglet **Body** (Corps), le type de données **raw** (brut), puis **JSON**.

    ![Alt text](media\azure-stack-solution-machine-learning\image153.png)

1.  Dans un navigateur web, accédez au **point de terminaison externe**. Ajoutez ce qui suit à l’URL **/swagger.json** afin d’accéder au fichier Services Swagger utilisé pour tester la configuration.

    ![Alt text](media\azure-stack-solution-machine-learning\image154.png)

1.  Copiez l’exemple répertorié dans le fichier **Swagger.JSON**.

2.  Dans l’application Postman, collez l’exemple dans le corps de la publication, puis sélectionnez **Send** (Envoyer). Elle doit retourner une valeur semblable à celle indiquée ci-dessous.

    ![Alt text](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>Étape 7 : Créer un compte Stockage Azure Stack et une file d’attente de stockage

Créer un compte Stockage Azure Stack et une file d’attente de stockage pour les données.

1.  Connectez-vous au portail utilisateur Azure Stack. (Chaque Azure Stack dispose d’une URL de portail unique)

2.  Dans le portail utilisateur Azure Stack, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Tous les services**. Faites défiler les options pour accéder à **Stockage**, puis sélectionnez **Comptes de stockage**. Dans la fenêtre **Comptes de stockage**, sélectionnez **Ajouter**.

3.  Nommez le compte de stockage.

4.  Sélectionnez l’option de réplication pour le compte de stockage **LRS**.

5.  Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant.

6.  Sélectionnez **Local** pour l’emplacement du compte de stockage.

7.  Sélectionnez **Créer** pour créer le compte de stockage.

    ![Alt text](media\azure-stack-solution-machine-learning\image156.png)

1.  Choisissez le compte de stockage récemment créé.

2.  Sélectionnez **Files d’attente**.

    ![Alt text](media\azure-stack-solution-machine-learning\image157.png)

1.  Sélectionnez **+ File d’attente**, nommez la file d’attente, puis sélectionnez **OK.**

    ![Alt text](media\azure-stack-solution-machine-learning\image158.png)

1.  Obtenez la **chaîne de connexion** de la file d’attente de stockage et copiez-la.

    ![Alt text](media\azure-stack-solution-machine-learning\image159.png)

1.  Accédez à Azure Function App, puis sélectionnez **Paramètres d’application**.

    ![Alt text](media\azure-stack-solution-machine-learning\image160.png)

1.  Dans l’écran Paramètres d’application Function App, faites défiler les données jusqu’à Paramètres d’application, puis sélectionnez **+ Ajouter un nouveau paramètre**.

    ![Alt text](media\azure-stack-solution-machine-learning\image161.png)

1.  Entrez le nom du compte de stockage dans le champ **Nom**, en ajoutant _STORAGE à la fin

Cela permet à l’application de comprendre qu’il s’agit d’un point de terminaison de compte de stockage.

1.  Collez ensuite la chaîne de connexion dans le champ **Valeur**.

    ![Alt text](media\azure-stack-solution-machine-learning\image162.png)

1.  Revenez en haut de l’écran Paramètres d’application et sélectionnez **Enregistrer**.

    ![Alt text](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>Mettre à jour de la fonction de scoring pour utiliser la file d’attente de stockage

1.  Sélectionnez **Intégrer** sur la fonction et désélectionnez l’option **GET**.

2.  Sélectionnez **Enregistrer**.

3.  Puis sélectionnez **+ Nouvelle sortie** sous Sorties.

    ![Alt text](media\azure-stack-solution-machine-learning\image164.png)

1.  Sélectionnez ensuite **Stockage File d’attente Azure** et choisissez **Sélectionner**.

    ![Alt text](media\azure-stack-solution-machine-learning\image165.png)

1.  Mettez à jour le **nom de la file d’attente** pour lui donner le nom de la file d’attente de stockage créée précédemment, puis définissez **Connexion au compte de stockage** sur la connexion de compte Stockage créée précédemment et sélectionnez **Enregistrer.**

    ![Alt text](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>Étape 8 : Créer une fonction pour gérer les données propres

Créez une fonction Azure Stack pour déplacer les données propres d’Azure Stack vers Azure.

1.  Créez une fonction en sélectionnant **Fonctions**, puis le bouton **+ Nouvelle fonction**.

    ![Alt text](media\azure-stack-solution-machine-learning\image167.png)

1.  Sélectionnez **Déclencheur de minuteur**.

    ![Alt text](media\azure-stack-solution-machine-learning\image168.png)

1.  Sélectionnez le langage **C\#** et nommez la fonction **upload-to-azure**, puis définissez la planification sur **0 0 \*/1 \* \* \*** qui, en notation CRON, correspond à une fois par heure.

    ![Alt text](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Obtenir la chaîne de connexion à un compte Stockage Azure hébergé

1.  Accédez à <https://portal.azure.com>, puis sélectionnez le **compte Stockage Azure** créé précédemment.

2.  Sélectionnez **Clés d’accès**, puis copiez la **chaîne de connexion** pour le compte Stockage.

    ![Alt text](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>Mettre à jour la fonction upload-to-azure pour utiliser le stockage hébergé Azure

1.  Accédez à Azure Function App, puis sélectionnez **Paramètres d’application**.

    ![Alt text](media\azure-stack-solution-machine-learning\image171.png)

1.  Dans l’écran Paramètres d’application Function App, faites défiler les données jusqu’à Paramètres d’application, puis sélectionnez **+ Ajouter un nouveau paramètre**.

    ![Alt text](media\azure-stack-solution-machine-learning\image172.png)

1.  Entrez le nom du compte de stockage dans le champ **Nom**, en ajoutant _STORAGE à la fin

Cela permet à l’application de comprendre qu’il s’agit d’un point de terminaison de compte de stockage.

1.  Puis collez la chaîne de connexion du compte Stockage Azure hébergé dans le champ **Valeur**.

    ![Alt text](media\azure-stack-solution-machine-learning\image173.png)

1.  Revenez en haut de l’écran Paramètres d’application et sélectionnez **Enregistrer**.

    ![Alt text](media\azure-stack-solution-machine-learning\image174.png)

1.  Revenez à la fonction **upload-to-azure**.

2.  Sélectionnez **Intégrer** sur la fonction.

3.  Puis sélectionnez **+ Nouvelle sortie** sous Sorties.

    ![Alt text](media\azure-stack-solution-machine-learning\image175.png)

1.  Sélectionnez ensuite **Stockage Blob Azure** et choisissez **Sélectionner**.

    ![Alt text](media\azure-stack-solution-machine-learning\image176.png)

1.  Mettez à jour le **chemin d’accès** vers le conteneur de stockage créé précédemment au format suivant : **uploadeddata/{rand-guid}.txt**, puis définissez la **connexion au compte Stockage** sur la connexion au compte Stockage Azure créée précédemment, et sélectionnez **Enregistrer.**

    ![Alt text](media\azure-stack-solution-machine-learning\image177.png)

1.  Copiez et collez le contenu de l’exemple de code de **upload-to-azure** dans la fonction.

2.  Procédez aux modifications requises pour pointer vers la chaîne de connexion des comptes Stockage.

3.  Enregistrez et exécutez le code.

    ![Alt text](media\azure-stack-solution-machine-learning\image178.png)

1.  Vérifiez dans le compte Stockage Azure hébergé que les données ont été analysées dans le cloud à partir d’Azure : si l’opération est réussie, un écran du type suivant doit s’afficher.

    ![Alt text](media\azure-stack-solution-machine-learning\image179.png)

Les données ont été purgées des données sensibles par Azure Stack Hosted Kubernetes Machine Learning et chargées vers le cloud public Azure à partir d’Azure Stack en local, par le biais d’Azure Stack Hosted Function Apps, et les données peuvent être stockées pour des chargements dans un scénario de périphérie/déconnexion.

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).