---
title: Déployer votre application sur Azure et Azure Stack | Microsoft Docs
description: Découvrez comment déployer des applications sur Azure et Azure Stack avec un pipeline CI/CD hybride.
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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 483122f5d2e39c8595d0f28a6b937772c4ea2e50
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258346"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Tutoriel : Déployer des applications sur Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Un pipeline CI/CD (intégration continue/livraison continue) hybride vous permet de générer, de tester et de déployer votre application sur plusieurs clouds.  Dans ce tutoriel, vous créez un exemple d’environnement pour :
 
> [!div class="checklist"]
> * Lancer une nouvelle build basée sur des validations de code dans votre dépôt VSTS (Visual Studio Team Services).
> * Déployer automatiquement votre code nouvellement généré sur le cloud mondial Azure pour le test d’acceptation des utilisateurs.
> * Une fois que votre code a réussi le test, effectuer automatiquement le déploiement sur Azure Stack.

### <a name="about-the-hybrid-delivery-build-pipe"></a>À propos du canal de build de livraison hybride

La continuité, la sécurité et la fiabilité du déploiement d’applications sont essentiels pour votre organisation et critiques pour votre équipe de développement. Avec un pipeline CI/CD hybride, vous pouvez consolider vos pipelines dans votre environnement local et dans le cloud public. Vous pouvez changer d’emplacement sans déplacer votre application.

Cette approche vous permet aussi de conserver un ensemble cohérent d’outils de développement. Des outils cohérents entre le cloud public Azure et votre environnement Azure Stack local vous permettent d’implémenter beaucoup plus facilement des pratiques de développement CI/CD. Les applications et les services déployés dans Azure ou Azure Stack sont interchangeables, et le même code peut s’exécuter dans l’un ou l’autre des emplacements, en tirant parti des fonctionnalités locales et de celles du cloud public.

Pour en savoir plus :
 - [Qu’est-ce que l’intégration continue ?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [Qu’est-ce que la livraison continue ?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>Prérequis


Quelques composants doivent être en place pour pouvoir créer un pipeline CI/CD hybride. Un certain temps peut être nécessaire pour les préparer.
 
 - Un partenaire OEM/matériel Azure peut déployer un service Azure Stack de production et tous les utilisateurs peuvent déployer un Kit de développement Azure Stack. 
 - Un opérateur Azure Stack doit également déployer le service App Service, créer des plans et des offres, créer un abonnement de locataire et ajouter l’image Windows Server 2016.

Si vous disposez déjà de certains de ces composants, vérifiez qu’ils répondent aux conditions requises avant de commencer.

Cette rubrique suppose également que vous connaissez déjà Azure et Azure Stack. Si vous voulez en savoir plus avant de continuer, veillez à commencer par les rubriques suivantes :


Ce tutoriel suppose également que vous connaissez déjà Azure et Azure Stack. 

Si vous voulez en savoir plus avant de continuer, vous pouvez commencer par les rubriques suivantes :
 - [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concepts clés d’Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

 - Créez une [application web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) dans Azure. Notez la nouvelle URL de l’application web, car nous l’utiliserons ultérieurement.

Azure Stack
 - Utilisez un système intégré Azure Stack intégré ou déployez un Kit de développement Azure Stack via le lien ci-dessous :
    - Vous trouverez des instructions détaillées sur le déploiement du Kit de développement Azure Stack dans « [Tutoriel : Déployer le Kit de développement Azure Stack à l’aide du programme d’installation](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) »
    - Vous pouvez automatiser un grand nombre des étapes de post-déploiement de votre Kit de développement Azure Stack avec le script PowerShell suivant : [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!Note]  
    > Comme l’installation du Kit de développement Azure Stack prend environ 7 heures, adaptez votre planification en conséquence.

 - Déployez les services PaaS [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) sur Azure Stack. 
 - Créez [un plan/des offres](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dans l’environnement Azure Stack. 
 - Créez un [abonnement de locataire](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dans l’environnement Azure Stack. 
 - Créez une application web au sein de l’abonnement du locataire. Notez l’URL de la nouvelle application web, car elle sera utilisée plus tard.
 - Déployez la machine virtuelle VSTS, toujours au sein de l’abonnement du locataire.
 - Une machine virtuelle Windows Server 2016 avec .NET 3.5 est nécessaire. Cette machine virtuelle est créée sur votre service Azure Stack en tant qu’agent de build privé. 

### <a name="developer-tools"></a>Outils de développeur

 - Créez un [espace de travail VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Le processus d’inscription crée un projet nommé **MyFirstProject**.
 - [Installez Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio), puis [connectez-vous à VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - Connectez-vous au projet et [créez un clone local](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!Note]  
 > Vous avez besoin d’Azure Stack avec les images appropriées syndiquées pour s’exécuter (Windows Server et SQL), et App Service doit être déployé.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Préparer l’agent de build et de mise en production privée pour l’intégration de Visual Studio Team Services

### <a name="prerequisites"></a>Prérequis


Visual Studio Team Services (VSTS) s’authentifie sur Azure Resource Manager avec un principal du service. Pour que VSTS puisse provisionner des ressources dans un abonnement Azure Stack, l’état Contributeur lui est nécessaire.

Voici les principales étapes de configuration qui permettent une telle authentification :

1. Vous devez créer un principal du service ou en utiliser un existant.
2. Vous devez créer des clés d’authentification pour le principal du service.
3. L’abonnement Azure Stack doit être validé via le contrôle d’accès en fonction du rôle (RBAC) pour autoriser le SPN à faire partie du rôle Contributeur.
4. Vous devez créer une nouvelle définition de service dans VSTS avec les points de terminaison Azure Stack ainsi que les informations du SPN.

### <a name="service-principal-creation"></a>Création du principal du service

Reportez-vous aux instructions de [Création du principal du service](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) pour créer un principal du service et choisissez le type d’application Application/API web.

### <a name="access-key-creation"></a>Création de la clé d’accès

Un principal du service nécessite une clé pour l’authentification. Suivez les étapes de cette section pour générer une clé.


1. Dans **Inscriptions d’applications** dans Azure Active Directory, sélectionnez votre application.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Notez la valeur de **l’ID d’application**. Vous devez utiliser cette valeur lors de la configuration du point de terminaison du service dans VSTS.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Pour générer une clé d’authentification, sélectionnez **Paramètres**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Pour générer une clé d’authentification, sélectionnez **Clés**.
 
    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Fournissez une description de la clé et la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.
 
    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Après avoir enregistré la clé, la valeur de la clé s’affiche. Copiez cette valeur car vous ne pourrez pas récupérer la clé ultérieurement. Vous fournissez la **valeur de la clé** avec l’ID d’application pour vous connecter en tant qu’application. Stockez la valeur de la clé à un emplacement où votre application peut la récupérer.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>Obtenir l’ID de locataire

Dans le cadre de la configuration du point de terminaison de service, VSTS nécessite **l’ID de locataire** qui correspond à l’annuaire AAD sur lequel votre pile Azure Stack a été déployée. Suivez les étapes de cette section pour récupérer l’ID de locataire.

1. Sélectionnez **Azure Active Directory**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Pour obtenir l’ID de locataire, sélectionnez **Propriétés** pour votre client Azure AD.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Copiez l’**ID Directory**. Cette valeur est votre ID de locataire.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Accorder au principal du service les droits nécessaires pour déployer des ressources dans l’abonnement Azure Stack 

Pour accéder aux ressources de votre abonnement, vous devez affecter un rôle à l’application. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Accédez au niveau d’étendue que vous souhaitez affecter à l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**. Vous pouvez également sélectionner un groupe de ressources ou une ressource.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Sélectionnez **l’abonnement** (groupe de ressources ou ressource) auquel l’application doit être affectée.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Sélectionnez **Contrôle d’accès (IAM)**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Sélectionnez **Ajouter**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Sélectionnez le rôle que vous souhaitez affecter à l’application. L’image suivante montre le rôle **Propriétaire**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Par défaut, les applications Azure Active Directory ne figurent pas dans les options disponibles. Pour trouver votre application, vous devez **fournir le nom** dans le champ de recherche. Sélectionnez-le.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle. Votre application apparaît dans la liste des utilisateurs affectés à un rôle pour cette étendue.

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès à Azure. L’utilisation de RBAC vous permet de n’accorder que les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Gérer l’accès aux ressources d’un abonnement Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Pools d’agents VSTS

Au lieu de gérer chaque agent individuellement, vous organisez les agents en pools d’agents. Un pool d’agents définit la limite de partage pour tous les agents de ce pool. Dans VSTS, les pools d’agents sont délimités au niveau du compte VSTS : vous pouvez ainsi partager un pool d’agents entre des projets d’équipe. Pour plus d’informations et un tutoriel sur la création de pools d’agents VSTS, consultez [Creating agent pools and queues](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Ajouter un jeton d’accès personnel pour Azure Stack

1. Connectez-vous à votre compte VSTS et sélectionnez le nom de votre profil de compte.
2. Sélectionnez **Gérer la sécurité** pour accéder à la page de création d’un jeton d’accès.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Copiez le jeton.
    
    > [!Note]  
    > Récupérez les informations du jeton. Ces informations ne seront plus affichées une fois que vous aurez quitté cet écran. 
    
    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installer l’agent de build VSTS sur le service Azure Stack hébergé par le serveur de build

1.  Connectez-vous au serveur de build que vous avez déployé sur l’hôte Azure Stack.

2.  Téléchargez et déployez l’agent de build en tant que service avec votre jeton d’accès personnel, et exécutez-le en tant que compte d’administrateur de la machine virtuelle.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Accédez au dossier de l’agent de build extrait. Exécutez le fichier **run.cmd** à partir d’une invite de commandes avec élévation de privilèges. 

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  Une fois l’exécution de run.cmd terminée, le dossier avec le contenu extrait doit se présenter comme suit :

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Vous pouvez maintenant voir l’agent dans le dossier VSTS.

## <a name="endpoint-creation-permissions"></a>Autorisations de création des points de terminaison

Les utilisateurs peuvent créer des points de terminaison pour que les builds VSTO puissent déployer des applications Azure App Service sur la pile. VSTS se connecte à l’agent de build, qui se connecte alors à Azure Stack. 

![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Dans le menu **Paramètres**, sélectionnez **Sécurité**.
2. Dans la liste **Groupes VSTS** sur la gauche, sélectionnez **Créateurs de points de terminaison**. 

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. Sous l’onglet **Membres**, sélectionnez **+Ajouter**. 

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Tapez un nom d’utilisateur et sélectionnez cet utilisateur dans la liste.
5. Cliquez sur **Save changes**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. Dans la liste **Groupes VSTS** sur la gauche, sélectionnez **Administrateurs de points de terminaison**.
7. Sous l’onglet **Membres**, sélectionnez **+Ajouter**.
8. Tapez un nom d’utilisateur et sélectionnez cet utilisateur dans la liste.
9. Cliquez sur **Enregistrer les modifications**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    L’agent de build dans Azure Stack reçoit des instructions de VSTS, qui transmet ensuite les informations des points de terminaison pour la communication avec Azure Stack. 
    
    La connexion VSTS à Azure Stack est prête.

## <a name="develop-your-application"></a>Développer votre application

Configurez la CI/CD hybride pour déployer Web App sur Azure et Azure Stack, et pour envoyer (push) les modifications sur les deux clouds.

> [!Note]  
> Vous avez besoin d’Azure Stack avec les images appropriées syndiquées pour s’exécuter (Windows Server et SQL), et App Service doit être déployé. Lisez la section « Prérequis » de la documentation d’App Service pour connaître la configuration requise de l’opérateur Azure Stack.

### <a name="add-code-to-vsts-project"></a>Ajouter du code au projet VSTS

1. Connectez-vous à Visual Studio avec un compte disposant des droits de création de projet sur Azure Stack.

    La CI/CD hybride peut s’appliquer au code d’application et au code d’infrastructure. Utilisez des [modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/) comme code d’application web de VSTS pour les deux clouds.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Clonez le référentiel** en créant et en ouvrant l’application web par défaut.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Créer un déploiement d’applications web autonomes pour App Services dans les deux clouds

1. Éditez le fichier **WebApplication.csproj**. Sélectionnez **Runtimeidentifier** et ajoutez `win10-x64.` Pour plus d’informations, consultez la documentation [Déploiements autonomes](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Archivez le code dans VSTS avec Team Explorer.

3. Vérifiez que le code d’application a été archivé dans Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>Créer la définition de build

1. Connectez-vous à VSTS pour vérifier la possibilité de créer des définitions de build.

2. Ajoutez le code **-r win10-x64**. Cette action est nécessaire pour déclencher un déploiement autonome avec .NET Core. 

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Exécutez la build. Le processus de [build de déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publie des artefacts qui peuvent s’exécuter sur Azure et Azure Stack.

### <a name="using-an-azure-hosted-agent"></a>Utilisation d’un agent hébergé sur Azure

L’utilisation d’un agent hébergé dans VSTS est une option pratique pour créer et déployer des applications web. La maintenance et les mises à niveau sont effectuées automatiquement par Microsoft Azure, ce qui permet le développement, le test et le déploiement continus et sans interruption.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Gérer et configurer le processus de déploiement continu (CD)

Visual Studio Team Services (VSTS) et Team Foundation Server (TFS) fournissent un pipeline hautement configurable et gérable pour des mises en production sur plusieurs environnements, comme des environnements de développement, de préproduction, de contrôle qualité et de production, avec des demandes d’approbation à des étapes spécifiques.

### <a name="create-release-definition"></a>Créer une définition de mise en production

![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Sélectionnez **\[ + ]** pour ajouter une nouvelle mise en production sous l’onglet **Mises en production** dans la page Build et mise en production de VSO.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Appliquez le modèle **Déploiement Azure App Service**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\103.png)

3. Sous le menu déroulant Ajouter un artefact, **ajoutez l’artefact** pour l’application de la build Azure Cloud.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\104.png)

4. Sous l’onglet Pipeline, sélectionnez le lien **Phase**, **Tâche** de l’environnement et définissez les valeurs de l’environnement cloud Azure.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Définissez le **nom de l’environnement** et sélectionnez **Abonnement** Azure pour le point de terminaison Azure Cloud.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\106.png)

6. Sous le nom de l’environnement, définissez le **nom de l’App Service Azure**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Entrez **VS2017 hébergé** sous la file d’attente de l’agent pour l’environnement hébergé dans le cloud Azure.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\108.png)

8. Dans le menu Déployer Azure App Service, sélectionnez **le package ou le dossier** valide pour l’environnement. Sélectionnez **OK** pour **l’emplacement du dossier**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Enregistrez toutes les modifications et revenez au **pipeline de mises en production**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Ajoutez un **nouvel artefact** en sélectionnant la build pour l’application Azure Stack.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Ajoutez un autre environnement en appliquant **Déploiement Azure App Service**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Nommez le nouvel environnement **Azure Stack**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Recherchez l’environnement Azure Stack sous l’onglet **Tâche**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Sélectionnez **l’abonnement** pour le point de terminaison Azure Stack.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Définissez le nom de l’application web Azure Stack comme **nom de l’App Service**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Sélectionnez **l’agent Azure Stack**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\118.png)

17. Sous la section Déployer Azure App Service, sélectionnez **le package ou le dossier** valide pour l’environnement. Sélectionnez OK pour **l’emplacement du dossier**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\120.png)

18. Sous l’onglet Variable, ajoutez une variable nommée **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, définissez sa valeur sur **true**et son étendue sur **Azure Stack**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Sélectionnez l’icône du déclencheur de déploiement **Continu** dans les deux artefacts et activez le déclencheur de déploiement Continu.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Sélectionnez l’icône des conditions **Prédéploiement** dans l’environnement Azure Stack et définissez le déclencheur sur **Après la mise en production**.

21. Enregistrez toutes les modifications.

> [!Note]  
> Certains paramètres pour les tâches peuvent avoir été automatiquement définis en tant que [variables d’environnement](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) quand vous avez créé une définition de mise en production à partir d’un modèle. Ces paramètres ne peuvent pas être modifiés dans les paramètres de la tâche ; au lieu de cela, vous devez sélectionner l’élément de l’environnement parent pour modifier ces paramètres.

## <a name="create-a-release"></a>Créer une mise en production

Maintenant que vous avez terminé les modifications de la définition de mise en production, il est temps de commencer le déploiement. Pour cela, vous créez une mise en production à partir de la définition de mise en production. Une mise en production peut être créée automatiquement ; par exemple, le déclencheur de déploiement continu est défini dans la définition de mise en production. Cela signifie que la modification du code source démarre une nouvelle build et, à partir de celle-ci, une nouvelle mise en production. Dans cette section, vous créez cependant une nouvelle mise en production manuellement.

1. Ouvrez la liste déroulante **Mise en production** et choisissez **Créer une mise en production**.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Entrez une description pour la mise en production, vérifiez que les artefacts corrects sont sélectionnés, puis choisissez **Créer**. Après quelques instants, une bannière s’affiche, indiquant que la nouvelle mise en production a été créée. Cliquez sur le lien (le nom de la mise en production).

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. La page récapitulative de la mise en production s’ouvre et montre les détails de la mise en production. Dans la section **Environnements**, vous voyez l’état du déploiement pour l’environnement « AQ » passer de « EN COURS » à « RÉUSSI ». À ce stade, une bannière apparaît, indiquant que la mise en production est maintenant en attente d’approbation. Quand un déploiement sur un environnement est en attente ou a échoué, une icône d’information (i) bleue est affichée. Pointez sur cette icône pour voir une fenêtre contextuelle qui en indique la raison.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\202.png)

D’autres vues, comme la liste des mises en production, affichent aussi une icône indiquant qu’une approbation est en attente. Quand vous pointez sur l’icône, elle affiche une fenêtre contextuelle qui contient le nom de l’environnement et des informations supplémentaires. Ceci permet à un administrateur de voir facilement quelles mises en production sont en attente d’approbation, ainsi que la progression globale de toutes les mises en production.

### <a name="monitor-and-track-deployments"></a>Surveiller et suivre les déploiements

Dans cette section, vous allez découvrir comment surveiller et suivre les déploiements (dans cet exemple, sur deux sites web Azure App Services) à partir de la mise en production que vous avez créée dans la section précédente.

1. Dans la page récapitulative de la mise en production, choisissez le lien **Journaux**. Pendant que le déploiement est en cours, cette page montre le journal en direct provenant de l’agent et, dans le volet gauche, une indication de l’état de chaque opération dans le processus de déploiement pour chaque environnement.

    Cliquez sur l’icône dans la colonne **Action** pour une approbation de prédéploiement ou de postdéploiement afin de voir les détails de la personne qui a approuvé (ou rejeté) le déploiement ainsi que le message entré par l’utilisateur.

2. Une fois le déploiement terminé, l’intégralité du fichier journal s’affiche dans le volet droit. Sélectionnez une des **étape du processus** dans le volet gauche pour voir seulement le contenu du fichier journal correspondant à cette étape. Ceci facilite le suivi et le débogage de parties individuelles du déploiement global. Vous pouvez aussi télécharger les fichiers journaux individuels ou un fichier zip de tous les fichiers journaux à partir des icônes et des liens de la page.

    ![Texte de remplacement](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Ouvrez l’onglet **Résumé** pour voir les détails de la mise en production. Il montre les détails de la build et les environnements où elle a été déployée, ainsi que l’état du déploiement et d’autres informations sur la mise en production.

4. Sélectionnez chacun des **liens d’environnement** pour voir plus d’informations sur les déploiements existants et en attente sur cet environnement spécifique. Vous pouvez utiliser ces pages pour vérifier que la même build a été déployée sur les deux environnements.

5. Ouvrez **l’application de production déployée** dans votre navigateur. Par exemple, pour un site web Azure App Services, utilisez l’URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
