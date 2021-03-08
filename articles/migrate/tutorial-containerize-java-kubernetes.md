---
title: Conteneuriser des applications Web Apps Java et migrer vers Azure Kubernetes Service
description: Découvrez comment conteneuriser des applications web Java et les migrer vers Azure Kubernetes Service.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: d85edf71ba78e2370a85e470c502d3f648af1f9b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742813"
---
# <a name="containerize-java-web-applications-and-migrate-to-azure-kubernetes-service"></a>Conteneuriser des applications web Java et migrer vers Azure Kubernetes Service

Cet article explique comment conteneuriser des applications web Java (s’exécutant sur Apache Tomcat) et les migrer vers [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) à l’aide de l’outil Conteneurisation d’applications d’Azure Migrate. Le processus de conteneurisation ne requiert pas d’accès à votre codebase et constitue un moyen simple de conteneuriser des applications existantes. L’outil fonctionne en utilisant l’état en cours d’exécution des applications sur un serveur pour déterminer les composants d’application, et vous aide à les empaqueter dans une image conteneur. Vous pouvez ensuite déployer l’application conteneurisée sur Azure Kubernetes service (AKS). 

L’outil Conteneurisation d’applications d’Azure Migrate prend actuellement en charge les opérations suivantes : 

- conteneurisation d’applications ASP.NET et déploiement de celles-ci vers des conteneurs Windows sur AKS ; [En savoir plus](./tutorial-containerize-aspnet-kubernetes.md) 
- conteneurisation d’applications web Java sur Apache Tomcat (sur serveurs Linux) et déploiement de celles-ci vers des conteneurs Linux sur AKS. 


L’outil Conteneurisation d’applications d’Azure Migrate vous aide à effectuer les opérations suivantes : 

- **Découvrir votre application :** l’outil se connecte à distance aux serveurs d’applications exécutant votre application web Java (s’exécutant sur Apache Tomcat) et découvre les composants d’application. L’outil crée un fichier Dockerfile utilisable pour créer une image conteneur pour l’application.
- **Créer l’image conteneur :** vous pouvez inspecter et personnaliser le fichier Dockerfile en fonction des besoins de votre application, et l’utiliser pour créer votre image conteneur d’application. L’image conteneur d’application est envoyée (push) à un Azure Container Registry que vous spécifiez.
- **Déployer vers Azure Kubernetes Service :** l’outil génère ensuite les fichiers YAML de définition de ressource Kubernetes nécessaires pour déployer l’application conteneurisée vers votre cluster Azure Kubernetes Service. Vous pouvez personnaliser les fichiers YAML et les utiliser pour déployer l’application sur AKS. 

> [!NOTE]
> L’outil Conteneurisation d’applications d’Azure Migrate vous aide à découvrir des types d’applications spécifiques (ASP.NET et applications web Java sur Apache Tomcat) et leurs composants sur un serveur d’applications. Pour découvrir les serveurs et l’inventaire des applications, rôles et fonctionnalités s’exécutant sur des machines locaux, utilisez la fonctionnalité Découverte et évaluation d’Azure Migrate. [En savoir plus](./tutorial-discover-vmware.md) 

Si toutes les applications ne tirent pas bénéfice d’un déplacement direct vers des conteneurs sans une réarchitecture conséquente, le déplacement d’applications existantes vers des conteneurs sans réécriture présente les avantages suivants :

- **Amélioration de l’utilisation de l’infrastructure :** avec des conteneurs, plusieurs applications peuvent partager des ressources et être hébergées sur la même infrastructure. Cela peut vous aider à consolider l’infrastructure et à améliorer l’utilisation. 
- **Simplification de la gestion :** en hébergeant vos applications sur une plateforme d’infrastructure managée moderne telle que AKS, vous pouvez simplifier vos pratiques de gestion tout en conservant le contrôle de votre infrastructure. Pour ce faire, vous pouvez supprimer ou réduire les processus de maintenance et de gestion de l’infrastructure que vous suivriez traditionnellement avec l’infrastructure dont vous disposez.
- **Portabilité des applications :** avec une adoption et une normalisation accrues des formats de spécification de conteneur et des plateformes d’orchestration, la portabilité des applications n’est plus une préoccupation. 
- **Adoption d’une gestion moderne avec le DevOps :** vous aide à adopter et à normaliser des pratiques modernes pour la gestion et la sécurité avec l’infrastructure en tant que code et la transition vers le DevOps.


Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un compte Azure
> * Installer l’outil Conteneurisation d’applications d’Azure Migrate.
> * Découvrir votre application web Java.
> * Générez l’image de conteneur.
> * Déployer l’application conteneurisée sur AKS.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez :

**Prérequis** | **Détails**
--- | ---
**Identifier une machine pour installer l’outil** | Il s’agit de trouver une machine Windows pour installer et exécuter l’outil Conteneurisation d’applications d’Azure Migrate. La machine Windows peut être un serveur (Windows Server 2016 ou version ultérieure) ou un système d’exploitation client (Windows 10), ce qui signifie que l’outil peut également s’exécuter sur votre ordinateur de bureau. <br/><br/> La machine Windows exécutant l’outil doit disposer d’une connectivité réseau aux serveurs/machines virtuelles hébergeant les applications ASP.NET à conteneuriser.<br/><br/> Assurez-vous qu’un espace de 6 Go est disponible sur la machine Windows exécutant l’outil Conteneurisation d’applications d’Azure Migrate pour le stockage des artefacts d’application. <br/><br/> La machine Windows doit avoir accès à Internet, directement ou via un proxy. <br/> <br/>Installez l’outil Microsoft Web Deploy sur la machine exécutant l’outil d’assistance Conteneurisation d’applications et le serveur d’applications s’il n’est pas déjà installé. Vous pouvez télécharger l’outil à partir d’[ici](https://aka.ms/webdeploy3.6). 
**Serveurs d’applications** | - Activez une connexion Secure Shell (SSH) sur le port 22 des serveurs exécutant les applications Java à conteneuriser. <br/>
**Application web Java** | Actuellement, l’outil prend en charge : <br/><br/> - applications s’exécutant sur Tomcat 8 ou version ultérieure ;<br/> - serveurs d’applications sur Ubuntu Linux 16.04/18.04/20.04, Debian 7/8, CentOS 6/7, Red Hat Enterprise Linux 5/6/7 ; <br/> - applications utilisant Java version 7 ou ultérieure.  <br/><br/> Actuellement, l’outil ne prend pas en charge : <br/><br/> - serveurs d’applications exécutant plusieurs instances Tomcat. <br/>  


## <a name="prepare-an-azure-user-account"></a>Préparer un compte de stockage Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

Une fois votre abonnement configuré, vous aurez besoin d’un compte d’utilisateur Azure disposant des autorisations suivantes :
- autorisations de type Propriétaire sur l’abonnement Azure ;
- autorisations d’inscrire des applications Azure Active Directory.

Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement. Si vous n’êtes pas le propriétaire de l’abonnement, demandez à celui-ci de vous attribuer les autorisations de la façon suivante :

1. Dans le portail Azure, recherchez « abonnements », puis sous **Services**, sélectionnez **Abonnements**.

    ![Zone de recherche pour rechercher l’abonnement Azure.](./media/tutorial-discover-vmware/search-subscription.png)

2. Dans la page **Abonnements**, sélectionnez l’abonnement dans lequel vous souhaitez créer un projet Azure Migrate. 
3. Dans l’abonnement, sélectionnez **Contrôle d’accès (IAM)**  > **Vérifier l’accès**.
4. Dans **Vérifier l’accès**, recherchez le compte d’utilisateur correspondant.
5. Dans **Ajouter une attribution de rôle**, cliquez sur **Ajouter**.

    ![Recherchez un compte d’utilisateur pour vérifier l’accès et attribuer un rôle.](./media/tutorial-discover-vmware/azure-account-access.png)

6. Dans **Ajouter une attribution de rôle**, sélectionnez le rôle Propriétaire, puis le compte (azmigrateuser dans notre exemple). Ensuite, cliquez sur **Enregistrer**.

    ![Ouvre la page Ajouter une attribution de rôle pour attribuer un rôle au compte.](./media/tutorial-discover-vmware/assign-role.png)

7. Votre compte Azure a également besoin d’**autorisations pour inscrire des applications Azure Active Directory**.
8.  Dans le portail Azure, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
9.  Dans **Paramètres utilisateur**, vérifiez que les utilisateurs Azure AD peuvent inscrire des applications (défini sur **Oui** par défaut).

      ![Vérifiez dans les paramètres utilisateur que les utilisateurs peuvent inscrire des applications Active Directory.](./media/tutorial-discover-vmware/register-apps.png)

10.  Si les paramètres « Inscriptions d’applications » ont la valeur « Non », demandez au locataire ou à l’administrateur général d’affecter l’autorisation nécessaire. L’administrateur général ou le locataire peuvent également attribuer le rôle **Développeur d’applications** à un compte pour permettre l’inscription d’une application Azure Active Directory. [Plus d’informations](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Télécharger et installer l’outil Conteneurisation d’applications d’Azure Migrate

1. [Téléchargez](https://go.microsoft.com/fwlink/?linkid=2134571) le programme d’installation de l’outil Conteneurisation d’applications d’Azure Migrate sur une machine Windows.
2. Lancez PowerShell en mode administrateur et remplacez le répertoire PowerShell par le dossier contenant le programme d’installation. 
3. Exécutez le script d’installation à l’aide de la commande ci-dessous :

   ```powershell
   .\App ContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Lancer l’outil Conteneurisation d’applications 

1. Ouvrez un navigateur sur une machine capable de se connecter à la machine Windows exécutant l’outil Conteneurisation d’applications, puis ouvrez l’URL de l’outil : **https://*nom de machine ou adresse IP*: 44368**.

   Vous pouvez aussi ouvrir l’application à partir de l’ordinateur de bureau en sélectionnant le raccourci de l’application.

2. Si vous voyez un avertissement indiquant que votre connexion n’est pas privée, cliquez sur Avancé, puis choisissez de passer au site web. Cet avertissement s’affiche lorsque l’interface web utilise un certificat TLS/SSL auto-signé.
3. Dans l’écran de connexion, utilisez le compte d’administrateur local sur l’ordinateur pour vous connecter.
4. Pour spécifier le type d’application, sélectionnez **Applications web Java sur Tomcat**. 

    ![Charge par défaut pour l’outil Conteneurisation d’applications.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Remplir les prérequis pour l’outil
1. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
6. Dans l’application web de l’outil > **Configurer les prérequis**, procédez comme suit :
   - **Connectivité** : l’outil vérifie que la machine Windows a accès à Internet. Si la machine utilise un proxy :
     - Cliquez sur **Configurer le proxy** pour spécifier l’adresse du proxy (sous la forme Adresse IP ou FQDN) et le port d’écoute.
     - Spécifiez les informations d’identification si le proxy nécessite une authentification.
     - Seuls les proxys HTTP sont pris en charge.
     - Si vous avez ajouté les détails du proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
   - **Installer les mises à jour** : l’outil recherche automatiquement les dernières mises à jour et les installe. Vous pouvez également installer manuellement la dernière version de l’outil à partir d’[ici](https://go.microsoft.com/fwlink/?linkid=2134571).
   - **Activer Secure Shell (SSH)**  : l’outil vous invite à vérifier que Secure Shell (SSH) est activé sur les serveurs d’applications qui exécutent les applications web Java à conteneuriser. 
   

## <a name="login-to-azure"></a>Connexion à Azure

Cliquez sur **Se connecter** pour vous connecter à votre compte Azure. 

1. Vous aurez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. Cliquer sur Se connecter a pour effet d’ouvrir une boîte de dialogue modale affichant le code de l’appareil. 
2. Cliquez sur **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.

    ![Boîte de dialogue modale affichant le code de l’appareil.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Sous le nouvel onglet, collez le code de l’appareil, puis connectez-vous en utilisant vos informations d’identification Azure. Une fois connecté, vous pouvez fermer l’onglet du navigateur et revenir à l’interface web de l’outil Conteneurisation d’applications.
4. Sélectionnez le **locataire Azure** que vous souhaitez utiliser.
5. Spécifiez l’**Abonnement Azure** que vous souhaitez utiliser. 

## <a name="discover-java-web-applications"></a>Découvrir les applications web Java

L’outil d’assistance Conteneurisation d’applications se connecte à distance aux serveurs d’applications à l’aide des informations d’identification fournies, et tente de découvrir les applications web Java (s’exécutant sur Apache Tomcat) hébergées sur les serveurs d’applications.

1. Spécifiez l’**adresse IP ou le FQDN et les informations d’identification** du serveur exécutant l’application web Java à utiliser pour se connecter à distance au serveur pour la découverte d’applications. 
    - Les informations d’identification fournies doivent être pour un compte racine (Linux) sur le serveur d’applications. 
    - Pour les comptes de domaine (l’utilisateur doit être un administrateur sur le serveur d’applications), préfixez le nom d’utilisateur avec le nom de domaine au format *<domaine\nom_utilisateur>* . 
    - Vous pouvez exécuter une découverte d’applications pour cinq serveurs à la fois. 

2. Cliquez sur **Valider** pour vérifier que le serveur d’applications est accessible à partir de la machine exécutant l’outil et que les informations d’identification sont valides. Une fois la validation réussie, la colonne d’état indique l’état **Mappé**.  

    ![Capture d’écran pour l’adresse IP et les informations d’identification du serveur.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Cliquez sur **Continuer** pour démarrer la découverte d’applications sur les serveurs d’applications sélectionnés.   

4. Une fois la découverte d’applications terminée, vous pouvez sélectionner la liste d’applications à conteneuriser.

    ![Capture d’écran pour l’application web Java détectée.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Utilisez la case à cocher pour sélectionner les applications à conteneuriser.
5. **Spécifier le nom du conteneur** : spécifiez un nom pour le conteneur cible pour chaque application sélectionnée. Vous devez spécifier le nom du conteneur sous la forme <*nom:étiquette*> où l’étiquette est utilisée pour l’image conteneur. Par exemple, vous pouvez spécifier le nom du conteneur cible comme *nom_app:v1*.   

### <a name="parameterize-application-configurations"></a>Paramétrer les configurations de l’application 
Paramétrer la configuration a pour effet de rendre celle-ci disponible en tant que paramètre d’heure de déploiement. Vous pouvez ainsi configurer ce paramètre lors du déploiement de l’application, par opposition au codage en dur d’une valeur spécifique dans l’image conteneur. Par exemple, cette option est utile pour des paramètres tels que des chaînes de connexion de base de données.
1. Cliquez sur **Configurations de l’application** pour passer en revue les configurations détectées. 
2. Activez la case à cocher pour paramétrer les configurations de l’application détectées. 
3. Cliquez sur **Appliquer** après avoir sélectionné les configurations à paramétrer.

   ![Capture d’écran de l’application ASP.NET de paramétrage de la configuration de l’application.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Externaliser les dépendances du système de fichiers

 Vous pouvez ajouter d’autres dossiers que votre application utilise. Spécifiez s’ils doivent faire partie de l’image conteneur ou s’ils doivent être externalisés via des volumes persistants sur un partage de fichiers Azure. L’utilisation de volumes persistants fonctionne parfaitement pour des applications avec état qui stockent l’état en dehors du conteneur ou ont un autre contenu statique stocké sur le système de fichiers. [En savoir plus](https://docs.microsoft.com/azure/aks/concepts-storage)
 
1. Sous Dossiers de l’application, cliquez sur **Modifier** pour passer en revue les dossiers d’application détectés. Les dossiers d’application détectés ont été identifiés comme des artefacts obligatoires requis par l’application, et seront copiés dans l’image conteneur. 
    
2. Cliquez sur **Ajouter des dossiers**, puis spécifiez les chemins d’accès de dossiers à ajouter. 
3. Pour ajouter plusieurs dossiers au même volume, fournissez des valeurs séparées par des virgules (`,`). 
4. Sélectionnez **Volume persistant** comme option de stockage si vous souhaitez que les dossiers soient stockés en dehors du conteneur sur un volume persistant. 
5. Cliquez sur **Enregistrer** après avoir révisé les dossiers d’applications. 
   ![Capture d’écran de la sélection du stockage des volumes d’applications.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Cliquez sur **Continuer** pour passer à la phase de génération de l’image conteneur.


## <a name="build-container-image"></a>Générer l’image conteneur


1. **Sélectionner Azure Container Registry** : utilisez la liste déroulante pour sélectionner un [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) qui sera utilisé pour générer et stocker les images conteneurs pour les applications. Vous pouvez utiliser un Azure Container Registry existant ou en créer un à l’aide de l’option Créer un registre.

    ![Capture d’écran de la sélection d’un Azure Container Registry d’application.](./media/tutorial-containerize-apps-aks/build-java-app.png)


2. **Réviser le fichier Dockerfile** : le fichier Dockerfile nécessaire pour générer l’image conteneur pour chaque application sélectionnée est généré au début de l’étape de génération. Cliquez sur **Réviser** pour examiner le fichier Dockerfile. Vous pouvez également ajouter les personnalisations nécessaires au fichier Dockerfile à l’étape de révision, et enregistrer les modifications avant de démarrer le processus de génération.

3. **Déclencher le processus de génération** : sélectionnez les applications pour lesquelles générer des images, puis cliquez sur **Générer**. Cliquer sur Générer a pour effet de démarrer la génération de l’image conteneur pour chaque application. L’outil continue de surveiller l’état de la build et vous permet de passer à l’étape suivante une fois la build terminée.

4. **Suivre l’état de la build** : vous pouvez également surveiller la progression de l’étape de génération de la build en cliquant sur le lien **Build en cours de génération** sous la colonne d’état. Une fois que vous avez déclenché le processus de génération, l’activation du lien prend quelques minutes.  

5. Une fois la génération terminée, cliquez sur **Continuer** pour spécifier les paramètres de déploiement. 

    ![Capture d’écran de la fin de la génération d’image conteneur d’application.](./media/tutorial-containerize-apps-aks/build-java-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Déployer l’application conteneurisée sur AKS

Une fois l’image conteneur générée, l’étape suivante consiste à déployer l’application en tant que conteneur sur [Azure Kubernetes service (AKS)](https://azure.microsoft.com/services/kubernetes-service/). 

1. **Sélectionner le cluster Azure Kubernetes Service** : spécifiez le cluster AKS sur lequel l’application doit être déployée. 

     - Le cluster AKS sélectionné doit avoir un pool de nœuds Linux. 
     - Le cluster doit être configuré de façon à autoriser l’extraction d’images à partir de l’Azure Container Registry sélectionné pour stocker les images. 
         - Exécutez la commande suivante dans Azure CLI pour attacher le cluster AKS à l’Azure Container Service.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Si vous ne disposez pas d’un cluster AKS ou si vous souhaitez créer un cluster AKS sur lequel déployer l’application, vous pouvez choisir de créer à partir de l’outil en cliquant sur **Créer un cluster AKS**.      
          - Le cluster AKS créé à l’aide de l’outil sera créé avec un pool de nœuds Linux. Le cluster sera configuré de manière à ce qu’il puisse extraire des images de l’Azure Container Registry créé précédemment (si l’option Créer un registre a été choisie). 
     - Cliquez sur **Continuer** après avoir sélectionné le cluster AKS.

2. **Spécifier un partage de fichiers Azure** : si vous avez ajouté des dossiers et sélectionné l’option Volume persistant, spécifiez le partage de fichiers Azure que l’outil Conteneurisation d’applications d’Azure Migrate doit utiliser pendant le processus de déploiement. L’outil crée des répertoires dans ce partage de fichiers Azure pour copier les dossiers d’application configurés pour le stockage du Volume persistant. Une fois le déploiement de l’application terminé, l’outil nettoie le partage de fichiers Azure en supprimant les répertoires qu’il a créés. 

     - Si vous n’avez pas de partage de fichiers Azure ou si vous souhaitez créer un partage de fichiers Azure, vous pouvez choisir de continuer à le créer à partir de l’outil en cliquant sur **Créer un compte de stockage et un partage de fichiers**.  

3. **Configurer le déploiement de l’application** : une fois les étapes ci-dessus accomplies, vous devez spécifier la configuration du déploiement pour l’application. Cliquez sur **Configurer** pour personnaliser le déploiement de l’application. Dans l’étape de configuration, vous pouvez fournir les personnalisations suivantes :
     - **Chaîne de préfixe** : spécifiez une chaîne de préfixe à utiliser dans le nom de toutes les ressources créées pour l’application conteneurisée dans le cluster AKS.
     - **Jeux de réplicas** : spécifiez le nombre d’instances d’application (pods) qui doivent s’exécuter dans les conteneurs.
     - **Type d’équilibreur de charge** : sélectionnez *Externe* si l’application conteneurisée doit être accessible à partir de réseaux publics. 
     - **Configuration de l’application** : pour toutes les configurations d’application paramétrées, fournissez les valeurs à utiliser pour le déploiement en cours.
     - **Stockage** : pour tous les dossiers d’application configurés pour le stockage du Volume persistant, spécifiez si le volume doit être partagé entre les instances d’application ou s’il doit être initialisé individuellement avec chaque instance dans le conteneur. Par défaut, tous les dossiers d’application sur des volumes persistants sont configurés comme partagés. 
     - Cliquez sur **Appliquer** pour enregistrer la configuration de déploiement.
     - Cliquez sur **Continuer** pour déployer l’application.

    ![Capture d’écran de la configuration de l’application de déploiement.](./media/tutorial-containerize-apps-aks/deploy-java-app-config.png)

4. **Déployer l’application** : une fois la configuration de déploiement de l’application enregistrée, l’outil génère le YAML de déploiement Kubernetes pour l’application. 
     - Cliquez sur **Modifier** pour réviser et personnaliser le YAML de déploiement Kubernetes pour les applications. 
     - Sélectionnez l’application à déployer.
     - Cliquez sur **Déployer** pour démarrer les déploiements des applications sélectionnées

         ![Capture d’écran de la configuration du déploiement d’application.](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - Une fois l’application déployée, vous pouvez cliquer sur la colonne *État du déploiement* pour suivre les ressources déployées pour l’application. 

## <a name="download-generated-artifacts"></a>Télécharger les artefacts générés

Tous les artefacts utilisés pour générer et déployer l’application dans AKS, y compris les fichiers de spécification YAML Dockerfile et Kubernetes, sont stockés sur la machine exécutant l’outil. Les artefacts se trouvent dans *C:\ProgramData\Microsoft Azure Migrate App Containerization*. 

Un dossier unique est créé pour chaque serveur d’applications. Vous pouvez afficher et télécharger tous les artefacts intermédiaires utilisés dans le processus de conteneurisation en accédant à ce dossier. Le dossier, qui correspond au serveur d’applications, sera nettoyé au début de chaque exécution de l’outil pour un serveur particulier.

## <a name="troubleshoot-issues"></a>Problèmes de dépannage

Pour résoudre tout problème lié à l’outil, vous pouvez consulter les fichiers journaux sur la machine Windows exécutant l’outil Conteneurisation d’applications. Les fichiers journaux des outils se trouvent dans le dossier *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs*. 

## <a name="next-steps"></a>Étapes suivantes

- conteneurisation d’applications ASP.NET et déploiement de celles-ci vers des conteneurs Windows sur AKS ; [En savoir plus](./tutorial-containerize-aspnet-kubernetes.md) 

