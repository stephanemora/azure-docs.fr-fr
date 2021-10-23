---
title: Conteneurisation d’applications ASP.NET et migration vers App Service
description: Ce tutoriel montre comment conteneuriser des applications ASP.NET et les migrer vers Azure App Service.
services: ''
author: rahug1190
manager: bsiva
ms.topic: tutorial
ms.date: 07/02/2021
ms.author: rahugup
ms.openlocfilehash: 14af442ff9a25178da2f8c6289bd988cae3bafd7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046940"
---
# <a name="aspnet-app-containerization-and-migration-to-azure-app-service"></a>Conteneurisation d’applications ASP.NET et leur migration vers Azure App Service

Cet article explique comment conteneuriser des applications ASP.NET et les migrer vers [Azure App Service](https://azure.microsoft.com/services/app-service/) à l’aide de l’outil Conteneurisation d’applications d’Azure Migrate. Le processus de conteneurisation ne requiert pas d’accès à votre codebase et constitue un moyen simple de conteneuriser des applications existantes. L’outil fonctionne en utilisant l’état en cours d’exécution des applications sur un serveur pour déterminer les composants d’application. Il vous aide ensuite à les empaqueter dans une image conteneur. Vous pouvez ensuite déployer l’application conteneurisée sur Azure App Service.

L’outil Conteneurisation d’applications d’Azure Migrate prend actuellement en charge les opérations suivantes :

- Conteneurisation d’applications ASP.NET et déploiement de celles-ci vers des conteneurs Windows sur App Service.
- Conteneurisation d’applications ASP.NET et déploiement de celles-ci vers des conteneurs Windows sur AKS (Azure Kubernetes Service). [Apprenez-en davantage sur ce scénario de conteneurisation.](./tutorial-app-containerization-aspnet-kubernetes.md)
- Conteneurisation d’applications web Java sur Apache Tomcat (sur serveurs Linux) et déploiement de celles-ci vers des conteneurs Linux sur AKS. [Apprenez-en davantage sur ce scénario de conteneurisation.](./tutorial-app-containerization-java-kubernetes.md)
- Conteneurisation d’applications web Java sur Apache Tomcat (sur serveurs Linux) et déploiement de celles-ci vers des conteneurs Linux sur App Service. [Apprenez-en davantage sur ce scénario de conteneurisation.](./tutorial-app-containerization-java-app-service.md)

L’outil Conteneurisation d’applications vous aide à :

- **Découvrir les composants de votre application.** L’outil se connecte à distance aux serveurs d’applications qui exécutent votre application ASP.NET, et découvre les composants d’application. Il crée un fichier Dockerfile que vous pouvez utilisez pour créer une image conteneur pour l’application.
- **Générez l’image de conteneur.** Vous pouvez inspecter et personnaliser le fichier Dockerfile en fonction des besoins de votre application, et l’utiliser pour créer votre image conteneur d’application. L’image conteneur d’application est envoyée (push) à un registre de conteneurs Azure que vous spécifiez.
- **Déployer sur Azure App Service.**  L’outil génère ensuite les fichiers de déploiement nécessaires au déploiement de l’application conteneurisée sur Azure App Service.

> [!NOTE]
> L’outil Conteneurisation d’applications d’Azure Migrate vous aide à découvrir des types d’applications spécifiques (ASP.NET et applications web Java sur Apache Tomcat) et leurs composants sur un serveur d’applications. Pour découvrir les serveurs et l’inventaire des applications, rôles et fonctionnalités s’exécutant sur des ordinateurs locaux, utilisez l’[Outil de découverte et d’évaluation d’Azure Migrate](./tutorial-discover-vmware.md).

Toutes les applications ne tireront pas bénéfice d’un déplacement direct vers des conteneurs sans une réarchitecture conséquente, mais le déplacement d’applications existantes vers des conteneurs sans réécriture présente les avantages suivants :

- **Amélioration de l’utilisation de l’infrastructure.** Lorsque vous utilisez des conteneurs, plusieurs applications peuvent partager des ressources et être hébergées sur la même infrastructure. Cela peut vous aider à consolider l’infrastructure et à améliorer l’utilisation.
- **Gestion simplifiée.** En hébergeant vos applications sur une plateforme managée moderne comme AKS ou App Service, vous pouvez simplifier vos pratiques de gestion. Pour ce faire, vous pouvez supprimer ou réduire les processus de maintenance et de gestion de l’infrastructure que vous suivriez traditionnellement avec l’infrastructure dont vous disposez.
- **Portabilité des applications.** Avec une adoption et une normalisation accrues des formats de spécification de conteneur et des plateformes, la portabilité des applications n’est plus une préoccupation.
- **Adoption d’une gestion moderne avec le DevOps.** L’utilisation de conteneurs vous aide à adopter et à normaliser des pratiques modernes pour la gestion et la sécurité ainsi que la transition vers le DevOps.


Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un compte Azure
> * Installer l’outil Conteneurisation d’applications d’Azure Migrate
> * Découvrir votre application ASP .NET.
> * Générez l’image de conteneur.
> * Déployer l’application conteneurisée sur App Service.

> [!NOTE]
> Les tutoriels constituent le chemin de déploiement le plus simple pour un scénario donné, afin que vous puissiez configurer rapidement une preuve de concept. Les tutoriels utilisent les options par défaut dans la mesure du possible, et n’affichent pas tous les paramètres et chemins.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez :

**Prérequis** | **Détails**
--- | ---
**Identifier une machine où installer l’outil** | Vous avez besoin d’une machine Windows sur laquelle installer et exécuter l’outil Conteneurisation d’applications d’Azure Migrate. La machine Windows peut exécuter un serveur (Windows Server 2016 ou version ultérieure) ou un système d’exploitation client (Windows 10). (L’outil peut s’exécuter sur votre bureau.) <br/><br/> La machine Windows exécutant l’outil doit disposer d’une connectivité réseau aux serveurs ou aux machines virtuelles hébergeant les applications ASP.NET que vous allez conteneuriser.<br/><br/> Vérifiez que 6 Go sont disponibles sur la machine Windows exécutant l’outil Conteneurisation d’applications d’Azure Migrate. Cet espace est destiné au stockage des artefacts d’application. <br/><br/> La machine Windows doit avoir accès à Internet, directement ou via un proxy. <br/> <br/>Si l’outil Microsoft Web Deployment n’est pas installé sur la machine exécutant l’outil Conteneurisation d’applications et le serveur d’applications installez-le. Vous pouvez [télécharger l’outil](https://aka.ms/webdeploy3.6).
**Serveurs d’applications** | Activez la communication à distance PowerShell sur les serveurs d’applications : connectez-vous au serveur d’applications et suivez [ces instructions pour activer la communication à distance PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting). <br/><br/> Si le serveur d’applications exécute Windows Server 2008 R2, assurez-vous que PowerShell 5.1 est installé sur le serveur d’applications. Suivez les instructions [ici pour télécharger et installer PowerShell 5.1](/powershell/scripting/windows-powershell/wmf/setup/install-configure) sur le serveur d’applications. <br/><br/> Si l’outil Microsoft Web Deployment n’est pas installé sur la machine exécutant l’outil Conteneurisation d’applications et le serveur d’applications installez-le. Vous pouvez [télécharger l’outil](https://aka.ms/webdeploy3.6).
**Application ASP.NET** | Actuellement, l’outil prend en charge : <br> <ul><li> Les applications ASP.NET qui utilisent .NET Framework 3.5 ou version ultérieure<br/> <li>Les serveurs d’applications qui exécutent Windows Server 2008 R2 ou version ultérieure (Les serveurs d’applications doivent exécuter PowerShell 5.1.) <br/><li> Les applications qui s’exécutent sur IIS (Internet Information Services) 7.5 ou version ultérieure</ul> <br/><br/> Actuellement, l’outil ne prend pas en charge : <br/> <ul><li>Les applications qui requièrent l’authentification Windows (AKS ne prend pas en charge gMSA à l’heure actuelle.) <br/> <li> Les applications qui dépendent d’autres services Windows hébergés en dehors d’Internet Information Services


## <a name="prepare-an-azure-user-account"></a>Préparer un compte de stockage Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

Une fois votre abonnement configuré, vous aurez besoin d’un compte d’utilisateur Azure disposant des autorisations suivantes :
- Autorisations de type Propriétaire sur l’abonnement Azure
- Des autorisations permettant d’inscrire des applications Azure Active Directory

Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement. Si vous n’êtes pas le propriétaire de l’abonnement, demandez à celui-ci de vous attribuer les autorisations de la façon suivante :

1. Dans le portail Azure, recherchez « abonnements ». Sous **Services**, sélectionnez **Abonnements** :

    ![Capture d’écran montrant la zone de recherche pour la recherche d’un abonnement Azure.](./media/tutorial-discover-vmware/search-subscription.png)

2. Dans la page **Abonnements**, sélectionnez l’abonnement dans lequel vous souhaitez créer un projet Azure Migrate.
3. Dans l’abonnement, dans le volet gauche, sélectionnez **Contrôle d’accès (IAM)** .
4. Sous l’onglet **Vérifier l’accès**, recherchez le compte d’utilisateur correspondant.
5. Sous **Ajouter une attribution de rôle**, sélectionnez **Ajouter** :

    ![Capture d’écran montrant comment rechercher un compte d’utilisateur pour vérifier l’accès et attribuer un rôle.](./media/tutorial-discover-vmware/azure-account-access.png)

6. Dans la page **Ajouter une attribution de rôle**, sélectionnez le rôle **Propriétaire**, puis le compte (**azmigrateuser** dans notre exemple). Ensuite, sélectionnez **Enregistrer**.

    ![Capture d’écran représentant la page Ajouter une attribution de rôle.](./media/tutorial-discover-vmware/assign-role.png)

   Votre compte Azure a également besoin d’autorisations pour inscrire des applications Azure Active Directory.
8. Dans le portail Azure, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
9. Dans **Paramètres utilisateur**, vérifiez que les utilisateurs Azure AD peuvent inscrire des applications. (Cette option a la valeur **Oui** par défaut.)

      ![Capture d’écran montrant la page Paramètres utilisateur.](./media/tutorial-discover-vmware/register-apps.png)

10. Si l’option **Inscriptions d’applications** a la valeur **Non**, demandez au locataire ou à l’administrateur général d’affecter l’autorisation nécessaire. L’administrateur général ou le locataire peuvent également attribuer le rôle Développeur d’applications à un compte pour permettre l’inscription d’applications Azure Active Directory. Pour plus d’informations, consultez [Attribuer des rôles aux utilisateurs](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="download-and-install-the-azure-migrate-app-containerization-tool"></a>Télécharger et installer l’outil Conteneurisation d’applications d’Azure Migrate

1. [Téléchargez le programme d’installation de l’outil Conteneurisation d’applications d’Azure Migrate](https://go.microsoft.com/fwlink/?linkid=2134571) sur une machine Windows.
2. Ouvrez PowerShell en mode administrateur et remplacez le répertoire PowerShell par le dossier qui contient le programme d’installation.
3. Exécutez le script d’installation à l’aide de cette commande :

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="open-the-app-containerization-tool"></a>Ouvrir l’outil Conteneurisation d’applications

1. Ouvrez un navigateur sur une machine capable de se connecter à la machine Windows exécutant l’outil Conteneurisation d’applications. Accédez à l’URL de l’outil : **https://*nom de machine ou adresse IP*: 44369**.

   Vous pouvez aussi ouvrir l’application à partir du bureau en utilisant le raccourci de l’application.

2. Si vous voyez un avertissement indiquant que votre connexion n’est pas privée, sélectionnez **Avancé** et accédez au site web. Cet avertissement s’affiche lorsque l’interface web utilise un certificat TLS/SSL auto-signé.
3. Dans l’écran de connexion, utilisez le compte d’administrateur local de la machine pour vous connecter.
4. Sélectionnez **Applications web ASP.NET** comme type d’application à conteneuriser.
5. Dans la liste **Service Azure cible**, sélectionnez **Conteneurs sur Azure App Service** :

   ![Capture d’écran montrant le type d’application et les listes de cibles.](./media/tutorial-containerize-apps-aks/tool-home.png)

### <a name="complete-the-tool-prerequisites"></a>Satisfaire les prérequis de l’outil
1. Acceptez les termes du contrat de licence et lisez les informations relatives aux tiers.
6. Dans l’application web de l’outil, dans **Configurer les prérequis**, effectuez ces étapes :
   - **Connectivité.** L’outil vérifie si la machine Windows a accès à Internet. Si la machine utilise un proxy :
     1. Sélectionnez **Configurer le proxy** pour spécifier l’adresse du proxy (sous la forme Adresse IP ou FQDN) et le port d’écoute.
     1. Spécifiez les informations d’identification si le proxy nécessite une authentification.
       
     1. Si vous avez ajouté les détails du proxy ou désactivé le proxy ou l’authentification, sélectionnez **Enregistrer** pour relancer la vérification de la connectivité.
     
     Seuls les proxys HTTP sont pris en charge.
   - **Installer les mises à jour.** L’outil recherche automatiquement les dernières mises à jour et il les installe. Vous pouvez également [installer manuellement la dernière version de l’outil](https://go.microsoft.com/fwlink/?linkid=2134571).
   - **Installer l’outil Microsoft Web Deploy.** L’outil vérifie si l’outil Microsoft Web Deploy est installé sur la machine Windows exécutant l’outil Conteneurisation d’applications d’Azure Migrate.
   - **Activer la communication à distance PowerShell.** L’outil vous invite à vérifier que la communication à distance PowerShell est activée sur les serveurs d’applications exécutant les applications ASP.NET que vous souhaitez conteneuriser.


## <a name="sign-in-to-azure"></a>Connexion à Azure

1. Sélectionnez **Se connecter** pour vous connecter à votre compte Azure.

   Vous avez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. La sélection de **Se connecter** doit faire apparaître une fenêtre qui contient le code de l’appareil. Si la fenêtre ne s’affiche pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
2. Sélectionnez **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur :

    ![Capture d’écran montrant la fenêtre Code de l’appareil pour la connexion à Azure.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Sous le nouvel onglet, collez le code de l’appareil, puis connectez-vous en utilisant vos informations d’identification Azure. Une fois connecté, vous pouvez fermer l’onglet du navigateur et revenir à l’interface web de l’outil Conteneurisation d’applications.
4. Sélectionnez le **locataire Azure** que vous souhaitez utiliser.
5. Spécifiez l’**Abonnement Azure** que vous souhaitez utiliser.

## <a name="discover-aspnet-applications"></a>Découvrir les applications ASP.NET

L’outil Conteneurisation d’applications se connecte à distance aux serveurs d’applications à l’aide des informations d’identification fournies, et tente de découvrir les applications ASP.NET hébergées sur les serveurs d’applications.

1. Spécifiez l’**adresse IP ou le FQDN du serveur** et les informations d’identification du serveur exécutant l’application ASP.NET à utiliser pour se connecter à distance au serveur pour la découverte d’applications.
    - Les informations d’identification fournies doivent celles d’un administrateur local (Windows) sur le serveur d’applications.
    - Pour les comptes de domaine (l’utilisateur doit être un administrateur sur le serveur d’applications), préfixez le nom d’utilisateur avec le nom de domaine au format suivant : *<domaine\nom_utilisateur>* .
    - Pour les comptes locaux (l’utilisateur doit être un administrateur sur le serveur d’applications), préfixez le nom d’utilisateur avec le nom d’hôte au format suivant : *<nom d’hôte\nom_utilisateur>* .
    - Vous pouvez exécuter une découverte d’applications pour cinq serveurs à la fois.

2. Sélectionnez **Valider** pour vérifier que le serveur d’applications est accessible à partir de la machine exécutant l’outil et que les informations d’identification sont valides. Une fois la validation réussie, la colonne **État** indique l’état **Mappé** :

    ![Capture d’écran montrant que l’état du serveur est Mappé.](./media/tutorial-containerize-apps-aks/discovery-credentials-asp.png)

3. Sélectionnez **Continuer** pour démarrer la découverte d’applications sur les serveurs d’applications sélectionnés.

4. Lorsque la découverte d’application est terminée, sélectionnez les applications que vous souhaitez conteneuriser :

    ![Capture d’écran montrant l’application ASP.NET découverte.](./media/tutorial-containerize-apps-aks/discovered-app-asp.png)

6. Spécifiez un nom pour le conteneur cible pour chaque application sélectionnée. Spécifiez le nom du conteneur au format <*nom:étiquette*>, où *étiquette* correspond à l’image conteneur. Par exemple, vous pouvez spécifier le nom du conteneur cible comme *nom_app:v1*.   

### <a name="parameterize-application-configurations"></a>Paramétrer les configurations de l’application
Paramétriser la configuration a pour effet de rendre celle-ci disponible en tant que paramètre au moment du déploiement. La paramétrisation vous permet de configurer un paramètre lors du déploiement de l’application, plutôt que de devoir coder en dur une valeur spécifique dans l’image conteneur. Par exemple, cette option est utile pour des paramètres tels que des chaînes de connexion de base de données.
1. Sélectionnez **Configurations de l’application** pour passer en revue les configurations détectées.
2. Sélectionnez les paramètres que vous souhaitez paramétriser, puis sélectionnez **Appliquer** :

   ![Capture d’écran montrant la liste des configurations détectées.](./media/tutorial-containerize-apps-aks/discovered-app-configs-asp.png)

### <a name="externalize-file-system-dependencies"></a>Externaliser les dépendances du système de fichiers

 Vous pouvez ajouter d’autres dossiers que votre application utilise. Spécifiez s’ils doivent faire partie de l’image conteneur ou s’ils doivent être externalisés vers un stockage persistant via un partage de fichiers Azure. L’utilisation d’un stockage persistant externe fonctionne parfaitement pour des applications avec état qui stockent l’état en dehors du conteneur ou ont un autre contenu statique stocké sur le système de fichiers.

1. Sous **Dossiers de l’application**, sélectionnez **Modifier** pour passer en revue les dossiers d’application détectés. Ces dossiers ont été identifiés comme des artefacts obligatoires requis par l’application. Ils seront copiés dans l’image conteneur.

2. Sélectionnez **Ajouter un dossier** et spécifiez les chemins de dossiers que vous souhaitez ajouter.
3. Pour ajouter plusieurs dossiers au même volume, séparez les valeurs par des virgules.
4. Sélectionnez **Partage de fichiers Azure** comme option de stockage si vous souhaitez que les dossiers soient stockés en dehors du conteneur sur un stockage persistant.
5. Sélectionnez **Enregistrer** après avoir passé en revue les dossiers d’applications :

   ![Capture d’écran montrant la fenêtre Modifier les paramètres de l’application.](./media/tutorial-containerize-apps-aks/discovered-app-volumes-asp.png)

6. Sélectionnez **Continuer** pour passer à la phase de génération de l’image conteneur.

## <a name="build-container-image"></a>Générer l’image conteneur


1. Dans la liste déroulante, sélectionnez un [registre de conteneurs Azure](../container-registry/index.yml) qui sera utilisé pour générer et stocker les images conteneurs pour les applications. Vous pouvez utiliser un registre de conteneurs Azure existant ou en créer un en sélectionnant **Créer un registre** :

    ![Capture d’écran montrant la fenêtre Générer des images.](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)

   > [!NOTE]
   > Seuls les registres de conteneurs Azure avec le compte d’utilisateur administrateur activé sont affichés. Le compte d’utilisateur administrateur est actuellement requis pour le déploiement d’une image à partir d’un registre de conteneurs Azure dans Azure App Service. Pour plus d’informations, consultez [Authentification avec un registre de conteneurs Azure](../container-registry/container-registry-authentication.md#admin-account).

2. Les fichiers Dockerfile nécessaires pour générer l’image conteneur pour chaque application sélectionnée sont générés au début de l’étape de génération. Sélectionnez **Réviser** pour examiner le fichier Dockerfile. Vous pouvez également ajouter les personnalisations nécessaires au fichier Dockerfile à l’étape de révision, et enregistrer les modifications avant de démarrer le processus de génération.

3. Sélectionnez les applications pour lesquelles vous souhaitez générer des images, puis sélectionnez **Générer**. La sélection de **Générer** a pour effet de démarrer la génération de l’image conteneur pour chaque application. L’outil supervise l’état de la build et vous permet de passer à l’étape suivante une fois la build terminée.

4.  Vous pouvez suivre la progression de la génération en sélectionnant **Build en cours de génération** sous la colonne d’état. Le lien devient actif quelques minutes après le déclenchement du processus de génération.  

5. Une fois la génération terminée, sélectionnez **Continuer** pour spécifier les paramètres de déploiement :

    ![Capture d’écran montrant le lien Réviser, l’état de l’image conteneur et les boutons Générer et Continuer.](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-azure-app-service"></a>Déployer l’application conteneurisée sur Azure App Service

Une fois l’image conteneur générée, l’étape suivante consiste à déployer l’application en tant que conteneur sur [Azure App Service](https://azure.microsoft.com/services/app-service/).

1. Sélectionnez le plan Azure App Service que l’application doit utiliser.

   Si vous n’avez pas de plan App Service ou si vous souhaitez en créer un nouveau à utiliser, vous pouvez en créer un à partir de l’outil en sélectionnant **Créer un plan App service**.      
1. Sélectionnez **Continuer** après avoir sélectionné le plan App Service.

2. Si vous avez paramétrisé des configurations d’application, spécifiez le magasin de secrets à utiliser pour l’application. Vous pouvez choisir les paramètres d’application App Service ou Azure Key Vault pour gérer vos secrets d’application. Pour plus d’informations, consultez [Configurer des chaînes de connexion](../app-service/configure-common.md#configure-connection-strings).

     - Si vous avez sélectionné des paramètres d’application App Service pour gérer vos secrets, sélectionnez **Continuer**.
     - Si vous souhaitez utiliser un coffre de clés Azure pour gérer vos secrets d’application, spécifiez le coffre de clés que vous souhaitez utiliser.     
         - Si vous n’avez pas de coffre de clés Azure ou si vous souhaitez en créer un, vous pouvez le faire en sélectionnant **Créer un coffre de clés Azure**.
         - L’outil attribue automatiquement les autorisations nécessaires pour la gestion des secrets par le biais du coffre de clés.

3. Si vous avez ajouté des dossiers et sélectionné l’option Partage de fichiers Azure pour le stockage persistant, spécifiez le partage de fichiers Azure que l’outil Conteneurisation d’applications doit utiliser pendant le déploiement. L’outil copiera les dossiers d’application que vous avez configurés pour Azure Files, et les montera sur le conteneur d’application durant le déploiement. 

   Si vous n’avez pas de partage de fichiers Azure ou si vous souhaitez en créer un, vous pouvez le faire en sélectionnant **Créer un compte de stockage et un partage de fichiers**.  

4. Vous devez maintenant spécifier la configuration de déploiement pour l’application. Sélectionnez **Configurer** pour personnaliser le déploiement de l’application. Lors de l’étape de configuration, vous pouvez fournir les personnalisations suivantes :
     - **Nom.** Spécifiez un nom d’application unique pour l’application. Ce nom sera utilisé pour générer l’URL d’application. Il sera également utilisé comme préfixe pour d’autres ressources créées dans le cadre du déploiement.
     - **Configuration d’applications.** Pour toutes les configurations d’application paramétrisées, fournissez les valeurs à utiliser pour le déploiement en cours.
     - **Configuration du stockage.** Passez en revue les informations relatives aux dossiers d’application qui sont configurés pour le stockage persistant.

    ![Capture d’écran montrant la configuration du déploiement.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

5. Une fois que vous avez enregistré la configuration du déploiement de l’application, l’outil génère le YAML de déploiement Kubernetes pour l’application.
     - Sélectionnez **Vérifier** pour passer en revue la configuration du déploiement des applications.
     - Sélectionnez les applications à déployer.
     - Sélectionnez **Déployer** pour démarrer le déploiement des applications sélectionnées.

         ![Capture d’écran montrant le bouton Déployer.](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - Une fois l’application déployée, vous pouvez sélectionner la colonne **État du déploiement** pour suivre les ressources déployées pour l’application.


## <a name="troubleshoot-problems"></a>Résoudre les problèmes

Pour résoudre tout problème lié à l’outil Conteneurisation d’applications, vous pouvez consulter les fichiers journaux sur la machine Windows qui exécute l’outil. Les fichiers journaux de l’outil se trouvent dans *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs*.

## <a name="next-steps"></a>Étapes suivantes

- [Conteneurisation d’applications ASP.NET et déploiement sur des conteneurs Windows sur AKS](./tutorial-app-containerization-aspnet-kubernetes.md)
- [Conteneurisation d’applications web Java sur Apache Tomcat (sur serveurs Linux) et déploiement sur des conteneurs Linux sur AKS](./tutorial-app-containerization-java-kubernetes.md)
- [Conteneurisation d’applications web Java sur Apache Tomcat (sur serveurs Linux) et déploiement sur des conteneurs Linux sur App Service](./tutorial-app-containerization-java-app-service.md)
