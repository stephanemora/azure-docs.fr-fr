---
title: Régénérer une application locale de Contoso sur Azure | Microsoft Docs
description: Découvrez comment Contoso régénère une application sur Azure à l’aide d’Azure App Services, de Kubernetes Service, de CosmosDB, d’Azure Functions et de Cognitive Services.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 3d835a7bd93426e57c5ab204d277faca22ae0638
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "42142293"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migration de Contoso : régénérer une application locale sur Azure

Cet article explique comment Contoso migre et régénère son application SmartHotel dans Azure. Ils migrent la machine virtuelle frontale de l’application vers Web Apps dans Azure App Services. Le backend de l'application est généré à l’aide de microservices déployés sur des conteneurs gérés par Azure Kubernetes Service (AKS). Le site interagit avec Azure Functions pour fournir des fonctionnalités de photos d’animaux. 

Ce document fait partie d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales et des scénarios qui illustrent comment configurer une infrastructure de migration, évaluer des ressources locales pour la migration et exécuter différents types de migration. Les scénarios croissant en complexité, nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md)  | Montre comment Contoso évalue une application à deux niveaux locale SmartHotel s’exécutant sur VMware. Contoso évalue les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : Réhéberger une application sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso exécute une migration lift-and-shift vers Azure pour l’application SmartHotel. Elle migre la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application vers une instance SQL Managed Instance à l’aide du [service Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre les machines virtuelles de l’application SmartHotel en utilisant uniquement Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles et un groupe de disponibilité AlwaysOn SQL Server (cet article)](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Elle utilise Site Recovery pour migrer la machine virtuelle de l’application, et Database Migration Service pour migrer la base de données de l’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso effectue une migration lift-and-shift de l’application osTicket Linux sur des machines virtuelles Azure à l’aide de Site Recovery. | Disponible
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et un serveur Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery, et migre la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application sur Azure Web Apps et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Montre comment Contoso migre l’application SmartHotel vers une application web Azure, et migre la base de données d’application vers une instance de serveur SQL Azure | Disponible
[Article 10 : Refactoriser une application Linux vers Azure Web Apps et Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Montre comment Contoso migre l’application Linux osTicket vers Azure Web Apps dans plusieurs sites intégrés avec GitHub pour assurer une livraison continue. Elle migre la base de données d’application vers une instance Azure MySQL. | Disponible
[Article 11 : Refactoriser TFS sur VSTS](contoso-migration-tfs-vsts.md) | Montre comment Contoso migre le déploiement de Team Foundation Server (TFS) local vers Visual Studio Team Services (VSTS) dans Azure. | Disponible
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et SQL Database](contoso-migration-rearchitect-container-sql.md) | Montre comment Contoso migre et réarchitecture son application SmartHotel vers Azure. Elle réarchitecture la couche web d’application en tant que conteneur Windows et la base de données d’application en une base de données Azure SQL Database. | Disponible
Article 13 : Régénérer une application dans Azure | Montre comment Contoso régénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, dont App Services, Azure Kubernetes, Azure Functions, Cognitive services et Cosmos DB. | Cet article.

Dans cet article, Contoso migre Windows à deux niveaux. Application NET SmartHotel s’exécutant sur des machines virtuelles VMware vers Azure. Si vous souhaitez utiliser cette application, elle est disponible en open source et vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir avec cette migration :

- **Répondre à la croissance de l’entreprise** : Contoso se développe. Ils veulent fournir des expériences différenciées à leurs clients sur leurs sites web.
- **Agilité** : Contoso doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale. 
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.
- **Coûts**  : Contoso souhaite réduire les coûts de licence.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les exigences d’application pour cette migration. Ces exigences ont été utilisées pour déterminer la meilleure méthode de migration :
 - L’application dans Azure restera aussi critique qu’aujourd'hui. Elle doit offrir de bonnes performances et se mettre à l’échelle facilement.
 - L’application ne doit pas utiliser de composants IaaS. Tout doit être créé pour utiliser des services PaaS ou sans serveur.
 - Les builds de l’application doivent s’exécuter dans des services cloud, et les conteneurs doivent résider dans un registre de conteneurs privé à l’échelle de l’entreprise dans le cloud.
 - Le service API utilisé pour les photos d’animaux doit être précis et fiable dans le monde réel, car les décisions prises par l’application doivent être respectées dans les hôtels. Tout animal auquel l’accès est accordé est autorisé à séjourner dans les hôtels.

## <a name="solution-design"></a>Conception de la solution

Après avoir défini précisément les objectifs et exigences, Contoso conçoit et examine une solution de déploiement, et identifie le processus de migration, notamment les services Azure à utiliser pour la migration.

### <a name="current-app"></a>Application actuelle

- L’application SmartHotel locale est hiérarchisée sur deux machines virtuelles (WEBVM et SQLVM).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.


### <a name="proposed-architecture"></a>Architecture proposée

- Le serveur frontal de l’application sera déployé en tant qu’application web Azure App Services, dans la région primaire.
- Une fonction Azure fournira les chargements de photos d’animaux, et le site interagira avec cette fonctionnalité.
- La fonction de photos d’animaux s’appuie sur l’API Vision de Cognitive Services et CosmosDB.
- Le serveur principal du site est créé à l’aide de microservices. Ceux-ci seront déployés sur des conteneurs gérés sur Azure Kubernetes Service (AKS).
- Les conteneurs seront générés à l’aide de VSTS, et envoyés à Azure Container Registry (ACR).
- Pour l’instant, Contoso déploie manuellement l’application web et le code de fonction à l’aide de Visual Studio.
- Les microservices sont déployés à l’aide d’un script PowerShell qui appelle des outils de ligne de commande de Kubernetes.

    ![Architecture du scénario](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Examen de la solution
Contoso évalue la conception proposée en dressant une liste des avantages et des inconvénients.

**Considération** | **Détails**
--- | ---
**Avantages** | L’utilisation de solutions PaaS et sans serveur pour le déploiement de bout en bout réduit considérablement le temps de gestion pour Contoso.<br/><br/> La migration vers une architecture de microservice permet à Contoso d’étendre facilement sa solution au fil du temps.<br/><br/> Les nouvelles fonctionnalités peuvent être mises en ligne sans interruption des bases de code de solutions existantes.<br/><br/> L’application web est configurée avec plusieurs instances sans point de défaillance unique.<br/><br/> La mise à l'échelle automatique sera activée afin que l’application puisse traiter les variations de volume du trafic.<br/><br/> Avec la migration vers des services PaaS, Contoso peut mettre hors service des solutions obsolètes s’exécutant sur le système d’exploitation Windows Server 2008 R2.<br/><br/> CosmosDB a une tolérance de panne intégrée qui ne nécessite aucune configuration par Contoso. Cela signifie que la couche Données n’est plus un point de basculement unique.
**Inconvénients** | Les conteneurs sont plus complexes que d’autres options de migration. La courbe d’apprentissage pourrait poser problème à Contoso.  Ils introduisent un nouveau niveau de complexité qui apporte beaucoup de valeur en dépit de la courbe.<br/><br/> L’équipe des opérations chez Contoso devra redoubler d’efforts pour comprendre et prendre en charge Azure, les conteneurs et les microservices pour l’application.<br/><br/> Contoso n’a pas encore totalement implémenté le DevOps pour la solution entière. Ils doivent y réfléchir pour le déploiement de services vers AKS, les fonctions et les App Services.



### <a name="migration-process"></a>Processus de migration

1. Contoso approvisionne l’ACR, l’AKS et CosmosDB.
2. Ils approvisionnent l’infrastructure pour le déploiement, notamment Azure Web App, le compte de stockage, la fonction et l’API. 
3. Une fois l’infrastructure en place, ils vont construire leurs images conteneurs de microservices à l’aide de VSTS qui les envoie à l’ACR.
4. Contoso déploiera ces microservices vers l’AKS à l’aide d’un script PowerShell.
5. Enfin, ils déploieront la fonction Azure et l’application web.

    ![Processus de migration](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Simplifie le déploiement, la gestion et les opérations de Kubernetes. Utilisez un service d'orchestration de conteneur Kubernetes entièrement géré.  | AKS est un service gratuit.  Vous payez uniquement pour les machines virtuelles, le stockage associé et la consommation des ressources réseau. [Plus d’informations](https://azure.microsoft.com/pricing/details/kubernetes-service/)
[Azure Functions](https://azure.microsoft.com/services/functions/) | Accélère le développement avec une expérience de calcul sans serveur pilotée par événements. Mettez à l’échelle à la demande.  | Payez uniquement pour les ressources consommées. L’offre est facturée sur la base des exécutions et de la consommation de ressources par seconde. [Plus d’informations](https://azure.microsoft.com/pricing/details/functions/)
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Stocke les images pour tous types de déploiements de conteneur. | Coût basé sur les fonctionnalités, le stockage et la durée d’utilisation. [Plus d’informations](https://azure.microsoft.com/pricing/details/container-registry/)
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Créez, déployez et mettez à l’échelle rapidement des applications web, mobiles et API de classe entreprise exécutables sur toute plateforme. | Les plans App Service sont facturés par seconde. [Plus d’informations](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="prerequisites"></a>Prérequis

Voici ce que vous (et Contoso) devez faire dans le cadre de ce scénario :

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Vous devez avoir déjà créé un abonnement lorsque vous avez effectué l’évaluation dans le premier article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.
**Infrastructure Azure** | [Découvrez comment](contoso-migration-infrastructure.md) Contoso configure une infrastructure Azure.
**Prérequis pour développeur** | Contoso a besoin des outils suivants sur une station de travail de développeur :<br/><br/> - [Visual Studio 2017 Community Edition version 15.5](https://www.visualstudio.com/)<br/><br/> Charge de travail .NET activée.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) ou Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/), configurés pour utiliser des conteneurs Windows.



## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso exécutera la migration :

> [!div class="checklist"]
> * **Étape 1 : Approvisionner AKS et ACR** : Contoso approvisionne le cluster AKS géré et le registre de conteneurs Azure à l’aide de PowerShell
> * **Étape 2 : Créer des conteneurs Docker** : ils configurent l’intégration continue pour les conteneurs Docker à l’aide de VSTS et les placent sur l’ACR.
> * **Étape 3 : Déployer les microservices principaux** : ils déploient le reste de l’infrastructure qu’utiliseront les microservices principaux.
> * **Étape 4 : Déployer l’infrastructure frontale** : ils déploient l’infrastructure frontale, dont le stockage blob pour les photos d’animaux, Cosmos DB et l’API Vision.
> * **Étape 5 : Migrer le serveur principal** : ils déploient les microservices et de les exécutent sur AKS pour migrer le serveur principal.
> * **Étape 6 : Publier le serveur frontal** : ils publient l’application SmartHotel sur Azure App service, ainsi que la Function App qui sera appelée par le service des animaux.



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>Étape 1 : Approvisionner un cluster AKS et ACR

Contoso exécute un script de déploiement pour créer le cluster Kubernetes géré à l’aide d’AKS et de l’Azure Container Registry.

- Les instructions de cette section utilisent le référentiel **SmartHotel360-Azure-backend**.
- Le dépôt GitHub **SmartHotel360-Azure-backend** contient tous les logiciels pour cette partie du déploiement.

Ils approvisionnent comme suit :

1. Avant de commencer, Contoso s’assure que tous les logiciels requis sont installés sur l’ordinateur de développement. 
2. Ils clonent le dépôt localement sur l’ordinateur de développement à l’aide de Git.

    **clonage git https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso ouvre le dossier à l’aide de Visual Studio Code, et accède au répertoire **/deploy/k8s** qui contient le script **gen-aks-env.ps1**.
4. Ils exécutent le script pour créer le cluster Kubernetes géré à l’aide d’AKS et du Container Registry.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  Après avoir ouvert le fichier, ils mettent à jour le paramètre $location en définissant **eastus2**, puis enregistrent le fichier.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. Ils cliquent sur **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré dans le Visual Studio Code.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. Dans le terminal intégré PowerShell, ils se connectent à Azure à l’aide de la commande Connect-AzureRmAccount. [En savoir plus](https://docs.microsoft.com/powershell/azure/get-started-azureps) sur la mise en route avec PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. Ils authentifient Azure CLI en exécutant la commande **az login**, et en suivant les instructions pour s’authentifier à l’aide de leur navigateur web. [En savoir plus](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) sur la connexion avec Azure CLI.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. Ils exécutent la commande suivante, en passant le nom de groupe de ressources ContosoRG, le nom du cluster AKS smarthotel-aks-eus2, et le nouveau nom de registre.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Azure crée un autre groupe de ressources contenant les ressources pour le cluster AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. Une fois le déploiement terminé, Contoso installe l’outil en ligne de commande **kubectl**. L’outil est déjà installé sur le CloudShell Azure.

    **az aks install-cli**

12. Ils vérifient la connexion au cluster en exécutant la commande **kubectl get nodes**. Le nœud porte le même nom que la machine virtuelle dans le groupe de ressources créé automatiquement.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. Ils exécutent la commande suivante pour démarrer le tableau de bord Kubernetes : 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

14. Un onglet de navigateur s’ouvre sur le tableau de bord. Il s’agit d’une connexion par tunnel utilisant Azure CLI. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>Étape 2 : Créer un conteneur Docker

### <a name="create-a-vsts-and-build"></a>Créer un VSTS et générer

Contoso crée un projet VSTS et configure une build CI pour créer le conteneur, puis transmet celui-ci à l’ACR. Les instructions de cette section utilisent le référentiel [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).

1. À partir de visualstudio.com, ils créent un compte (**contosodevops360.visualstudio.com**), puis le configurent pour utiliser Git.

2. Ils créent un nouveau projet (**smarthotelrefactor**) utilisant Git pour la gestion de version, et Agile pour le flux de travail.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. Ils importent le dépôt GitHub.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. Dans **Build and Release** (Générer et publier), ils créent une nouvelle définition utilisant VSTS Git en tant que source, à partir du référentiel importé **smarthotel**. 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. Ils sélectionnent l’option pour démarrer avec un pipeline vide.

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. Ils sélectionnent **Hosted Linux Preview** (Préversion Linux hébergée) pour la définition de build.

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. Dans **Phase 1**, ils ajoutent une tâche **Docker Compose**. Cette tâche génère le Docker compose.

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. Ils répètent l’opération pour ajouter une autre tâche **Docker Compose**. Celle-ci envoie les conteneurs à l’ACR.

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. Ils sélectionnent la première tâche (générer) et configurent la build avec l’abonnement, l’autorisation et l’ACR Azure. 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. Ils spécifient le chemin d’accès du fichier **docket-compose.yaml** dans le dossier **src** du référentiel. Ils sélectionnent l’option pour générer des images de service, et incluent la dernière balise. Lorsque l’action devient **Build service images** (Générer les images de service), le nom de la tâche VSTS devient **Build services automatically** (Générer les services automatiquement).

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. Maintenant, Contoso configure la deuxième tâche de Docker (envoyer). Ils sélectionnent l’abonnement et l’ACR **smarthotelacreus2**. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Là encore, ils définissent le fichier docker-compose.yaml, puis sélectionnent **Push service images** (Envoyer des images de service) et incluent la dernière balise. Lorsque l’action devient **Push service images** (Envoyer des images de service), le nom de la tâche VSTS devient **Push services automatically** (Envoyer les services automatiquement).

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Avec les tâches VSTS configurées, Contoso enregistre la définition de build et démarre le processus de génération.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. Ils cliquent sur la tâche de build pour vérifier la progression.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. Une fois la build terminée, l’ACR montre les nouveaux référentiels qui sont remplis des conteneurs utilisés par les microservices.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>Étape 3 : Déployer les microservices principaux

Avec le cluster AKS créé et la build d’images Docker, Contoso déploie maintenant le reste de l’infrastructure qu’exploiteront les microservices principaux.

- Les instructions de cette section utilisent le référentiel [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).
- Le dossier **/deploy/k8s/arm** contient un script unique pour créer tous les éléments. 

Ils déploient comme suit :

1. Contoso utilise le fichier deploy.cmd pour déployer les ressources Azure dans le groupe de ressources ContosoRG et la région EUS2, en tapant la commande suivante :

    **.\deploy azuredeploy ContosoRG -c eastus2**

    ![Déployer le serveur principal](./media/contoso-migration-rebuild/backend1.png)

2. Ils capturent la chaîne de connexion pour chaque base de données en vue d’une utilisation ultérieure.

    ![Déployer le serveur principal](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>Étape 4 : Déployer l’infrastructure frontale

Contoso doit déployer l’infrastructure qui sera utilisée par les applications frontales. Ils créent un conteneur de stockage d’objets blob pour stocker les images d’animaux, la base de données Cosmos pour stocker les documents contenant les informations sur les animaux, et l’API Vision pour le site web. 

Les instructions de cette section utilisent le référentiel [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web).

### <a name="create-storage-containers"></a>Créer des conteneurs de stockage

1.  Dans le portail Azure, Contoso ouvre le compte de stockage créé et clique sur **Objets blob**.
2.  Ils créent un conteneur (**Pets**) avec le niveau d’accès public défini sur conteneur. Les utilisateurs chargeront leurs photos d’animaux sur ce conteneur.

    ![Objet blob de stockage](./media/contoso-migration-rebuild/blob1.png)

3. Ils créent un deuxième conteneur nommé **Settings** (Paramètres). Un fichier contenant tous les paramètres de l’application frontale sera placé dans ce conteneur.

    ![Objet blob de stockage](./media/contoso-migration-rebuild/blob2.png)

4. Ils capturent les détails d’accès au compte de stockage dans un fichier texte pour référence ultérieure.

    ![Objet blob de stockage](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Approvisionner une base de données Cosmos

Contoso approvisionne une base de données Cosmos à utiliser pour les informations sur les animaux.

1. Ils créent une **Azure Cosmos DB** dans la Place de marché Azure.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Ils spécifient un nom (**contosomarthotel**), sélectionnent l’API SQL, et la placent dans le groupe de ressources de production ContosoRG, dans la région principale USA Est 2.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Ils ajoutent une nouvelle collection à la base de données, avec la capacité et le débit par défaut.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Ils notent les informations de connexion à la base de données pour référence ultérieure. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Approvisionner l’API Vision par ordinateur

Contoso approvisionne l’API Vision par ordinateur. L’API est appelée par la fonction pour évaluer les images chargées par les utilisateurs.

1. Ils créent une instance de l’**API Vision par ordinateur** dans la Place de marché Azure.

     ![Vision par ordinateur](./media/contoso-migration-rebuild/vision1.png)

2. Ils approvisionnent l’API (**smarthotelpets**) dans le groupe de ressources de production ContosoRG, dans la région principale USA Est 2.

    ![Vision par ordinateur](./media/contoso-migration-rebuild/vision2.png)

3. Ils enregistrent les paramètres de connexion de l’API dans un fichier texte pour référence ultérieure.

     ![Vision par ordinateur](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>Étape 5 : Déployer les services principaux dans Azure

Maintenant, Contoso doit déployer le contrôleur d’entrée NGINX pour autoriser le trafic entrant vers les services, puis déployer les microservices sur le cluster AKS.

Les instructions de cette section utilisent le référentiel [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).



1. Ils utilisent Visual Studio Code pour mettre à jour le fichier **/deploy/k8s/config_loal.yml**. Ils mettent à jour les connexions de base de données à partir des informations consignées précédemment.

     ![Déployer les microservices](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > Certains paramètres de configuration (par exemple Active Directory B2C) ne sont pas évoqués dans cet article. Le référentiel contient des informations supplémentaires sur ces paramètres.

2. Le fichier deploy.ps1 supprime tout le contenu du cluster (à l’exception de l’entrée et du contrôleur d’entrée) et déploie les microservices. Ils l’exécutent comme suit :

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. Ils exécutent la commande suivante pour vérifier l’état des services :

    ```
    kubectl get services
    ```
4. Ils ouvrent le tableau de bord Kubernetes pour examiner le déploiement :

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>Étape 6 : Publier le serveur frontal

En guise de dernière étape, Contoso publie l’application de SmartHotel sur Azure App Service et sur la Function App appelée par le Pet Service (Service des animaux).

Les instructions de cette section utilisent le [dépôt SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) .

### <a name="set-up-web-app-to-use-contoso-resources"></a>Configurer l’application web pour utiliser les ressources de Contoso

1. Contoso Clone le référentiel localement sur l’ordinateur de développement en utilisant Git :

    **clonage git https://github.com/Microsoft/SmartHotel360-public-web.git**

2. Dans Visual Studio, ils ouvrent le dossier pour afficher tous les fichiers contenus dans le référentiel.

    ![Publier le serveur frontal](./media/contoso-migration-rebuild/front-publish1.png)

3. Ils apportent les changements de configuration nécessaires au paramètre par défaut.

    - Lorsque l’application web démarre, elle recherche le paramètres d’application **SettingsUrl**.
    - Cette variable doit contenir une URL de fichier de configuration.
    - Par défaut, le paramètre utilisé est un point de terminaison public.

4. Ils mettent à jour le fichier **/config-sample.json/sample.json**. Il s’agit du fichier de configuration pour le web en cas d’utilisation d’un point de terminaison public.

    - Ils modifient les sections **urls** et **pets_config** avec les valeurs de points de terminaison, de comptes de stockage et de base de données Cosmos de l’API AKS. 
    - Les URL doivent correspondre au nom DNS de la nouvelle application web que Contoso va créer.
    - Pour Contoso, il s’agit de **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Publier le serveur frontal](./media/contoso-migration-rebuild/front-publish2.png)

5. Une fois le fichier est mis à jour, ils le renomment **smarthotelsettingsurl**, puis le chargent sur l’objet blob de stockage créé précédemment.

     ![Publier le serveur frontal](./media/contoso-migration-rebuild/front-publish3.png)

6. Ils cliquent sur le fichier pour obtenir l’URL. Cette URL est utilisée par l’application quand celle-ci commence à extraire le fichier de configuration.

    ![Publier le serveur frontal](./media/contoso-migration-rebuild/front-publish4.png)

7. Ils mettent à jour **SettingsUrl** dans les fichiers **appsettings.Production.JSON** sur l’URL du nouveau fichier.

    ![Publier le serveur frontal](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>Déployer le site web sur Azure App Service

Contoso peut à présent publier son site web.


1. Ils activent la surveillance d’Application Insights dans Visual Studio 2017. Pour ce faire, ils sélectionnent le projet **PublicWeb** dans l’Explorateur de solutions, puis recherchent **Ajouter Application Insights**. Ils inscrivent l’application auprès l’instance d’Application Insights créée lors du déploiement de l’infrastructure.

    ![Publier le site web](./media/contoso-migration-rebuild/deploy-website1.png)

2. Dans Visual Studio, ils connectent le projet PublicWeb à Application Insights, puis mettent à jour pour montrer qu’il est configuré.
 
    ![Publier le site web](./media/contoso-migration-rebuild/deploy-website2.png)

3. Dans Visual Studio, ils créent et publient leur application web.

    ![Publier le site web](./media/contoso-migration-rebuild/deploy-website3.png)

5. Ils spécifient un nom d’application, et le placent dans le groupe de ressources de production **ContosoRG**, dans la région principale USA Est 2.

    ![Publier le site web](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>Déployer la fonction sur Azure

1. Ils utilisent Visual Studio pour créer et publier la fonction. Pour ce faire, ils cliquent avec le bouton droit sur **PetCheckerFunction** > **Publier**. Ils créent ensuite une fonction App Service.

     ![Déployer la fonction](./media/contoso-migration-rebuild/function1.png)

2. Ils spécifient le nom **smarthotelpetchecker**, puis placent la fonction dans le groupe de ressources ContosoRG et un nouveau plan App Service.

     ![Déployer la fonction](./media/contoso-migration-rebuild/function2.png)

3. Ils sélectionnent le compte de stockage et créent la fonction.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function3.png)

4. Le déploiement commence par l’approvisionnement de l’application de fonction sur Azure. Dans **Publier**, Contoso ajoute les paramètres d’application pour le stockage, la base de données Cosmos et l’API Vision par ordinateur.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function4.png)

5. Pour exécuter la fonction d’abord localement, ils mettent à jour les paramètres dans **PetCheckerFunction/local.settings.json**.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function5.png)

6. Une fois déployée, la fonction apparaît dans le portail Azure, avec l’état **En cours d’exécution**.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function6.png)


7. Ils accèdent à l’application pour vérifier que l’intelligence artificielle de Pet Checker (Vérificateur d’animaux) fonctionne comme prévu sur [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets).
8. Ils cliquent sur l’avatar pour charger une image.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function7.png)

9. La première photo qu'ils souhaitent vérifier est celle d’un petit chien.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function8.png)

10. L’application renvoie un message d’acceptation.

    ![Déployer la fonction](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>Examiner le déploiement

Avec les ressources migrées dans Azure, Contoso doit rendre sa nouvelle infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

- Contoso doit s’assurer que ses nouvelles bases de données sont sécurisées. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- L’application doit être mise à jour pour utiliser le protocole SSL avec des certificats. L’instance de conteneur doit être redéployée pour répondre sur le port 443.
- Ils devraient envisager d’utiliser Key Vault pour protéger les secrets de leurs applications Service Fabric. [Plus d’informations](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)

### <a name="backups-and-disaster-recovery"></a>Sauvegardes et récupération d’urgence

- Contoso doit examiner les exigences de sauvegarde pour Azure SQL Database. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- Ils devraient envisager d’implémenter des groupes de basculement SQL afin de fournir un basculement régional pour la base de données. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)
- Ils peuvent tirer parti de la géoréplication pour la référence (SKU) premium d’ACR. [En savoir plus](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Une fois toutes les ressources déployées, Contoso doit affecter des balises Azure en fonction de la [planification de son infrastructure](contoso-migration-infrastructure.md#set-up-tagging).
- Toutes les licences sont intégrées dans le coût des services PaaS que Contoso consomme. Cela sera déduit de l’Accord Entreprise.
- Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management.

## <a name="conclusion"></a>Conclusion

Dans cet article, Contoso régénère l’application SmartHotel dans Azure. Ils ont régénéré les machines virtuelles frontales de l’application locale sur Web Apps dans Azure App Services. Ils ont créé le backend de l'application à l’aide de microservices déployés sur des conteneurs gérés par Azure Kubernetes Service (AKS). Ils ont amélioré la fonctionnalité de l’application avec une application de photo d’animaux.




