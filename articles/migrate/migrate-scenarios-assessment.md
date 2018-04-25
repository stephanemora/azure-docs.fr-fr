---
title: Évaluer des charges de travail locales pour la migration vers Azure avec le DMA et Azure Migrate | Microsoft Docs
description: Apprenez à préparer Azure pour la migration des machines locales à l’aide de l’Assistant Migration de données (DMA) et du service Azure Migrate.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1ac6c3e428148a6609b264b0b8f8cff416b0fa4d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>Scénario 1 : Évaluer les charges de travail locales pour la migration vers Azure

Lorsqu’elle envisage d’effectuer une migration vers Azure, la société Contoso souhaite réaliser une évaluation technique et financière pour déterminer si ses charges de travail locales peuvent être migrées vers le cloud. Elle souhaite notamment évaluer la compatibilité des machines et des bases de données par rapport à la migration, ainsi qu’estimer la capacité et les coûts associés à l’exécution de ses ressources dans Azure.

Pour se lancer et mieux comprendre les technologies nécessaires, elle évalue et migre une petite application de voyage locale. Il s’agit d’une application à deux niveaux, avec une application web s’exécutant sur la première machine virtuelle et une base de données SQL Server sur la deuxième machine virtuelle. L’application est déployée dans VMware, et l’environnement est géré par un serveur vCenter Server. Elle réalisera l’évaluation à l’aide de l’Assistant Migration de données (DMA) et du service Azure Migrate.

**Technology** | **Description** | **Coût**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Le DMA évalue et détecte les problèmes de compatibilité qui peuvent avoir un impact sur les fonctionnalités de la base de données dans Azure. En outre, il évalue et offre la parité des fonctionnalités entre votre source et votre cible SQL Server. Il vous suggère également des améliorations en matière de performances et de fiabilité pour votre environnement cible. | Cet outil est téléchargeable gratuitement. 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Le service vous permet d’évaluer des machines locales pour la migration vers Azure. Il évalue si les machines peuvent être migrées et propose des estimations de coûts et de dimensionnement pour leur exécution dans Azure. Actuellement, le service Azure Migrate peut évaluer des machines virtuelles VMware locales pour la migration vers Azure. | À la date de rédaction de cet article (avril 2018), l’utilisation de ce service est gratuite.
[Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate utilise Service Map pour afficher des dépendances entre les machines que vous souhaitez migrer. |  Service Map fait partie d’Azure Log Analytics. Actuellement, il peut être utilisé gratuitement pendant 180 jours. 

Dans ce scénario, nous allons télécharger et exécuter le DMA pour évaluer la base de données SQL Server locale de notre application de voyage. Nous allons utiliser Azure Migrate avec le mappage des dépendances pour évaluer les machines virtuelles de l’application avant de les migrer vers Azure.

> [!NOTE]
> Pour ce scénario, notre cible d’évaluation pour la base de données est « SQL Server sur une machine virtuelle Azure ». Toutefois, dans notre article suivant sur les scénarios, nous allons effectuer la migration vers une instance Azure SQL Managed Instance. Nous utilisons cette approche car le DMA ne prend pas actuellement en charge l’évaluation pour une cible Azure SQL Managed Instance.

## <a name="architecture"></a>Architecture

Dans ce scénario, nous allons procéder à une configuration 

 ![Architecture pour l’évaluation de la migration](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

Dans ce scénario :
- Contoso dispose d’un centre de données local (**contoso-datacenter**), avec un contrôleur de domaine local (**contosodc1**).
- L’application de voyage interne repose sur deux machines virtuelles, **WEBVM** et **SQLVM**, et se trouve sur l’hôte VMware ESXi **contosohost1.contoso.com**.
- L’environnement VMware est géré par le serveur vCenter Server (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.




## <a name="prerequisites"></a>Prérequis


Voici les éléments requis pour vous permettre de déployer ce scénario :

- Un serveur vCenter Server local qui exécute la version 5.5, 6.0 ou 6.5.
- Un compte en lecture seule dans le serveur vCenter Server, ou des autorisations pour en créer un. 
- Des autorisations pour créer une machine virtuelle sur le serveur vCenter Server, à l’aide d’un modèle .OVA.
- Au moins un hôte ESXi exécutant la version 5.0 ou une version ultérieure.
- Au moins deux machines virtuelles VMware locales, dont l’une exécutant une base de données SQL Server.
- Vous devez avoir des autorisations pour installer des agents Azure Migrate sur chaque machine virtuelle.
- Les machines virtuelles doivent avoir une connectivité Internet directe.
        - Vous pouvez limiter l’accès Internet aux [URL requises](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - Si certaines de vos machines n’ont pas de connectivité Internet, il vous faudra télécharger et installer la [passerelle OMS](../log-analytics/log-analytics-oms-gateway.md) sur celles-ci.
- Le nom de domaine complet de la machine virtuelle qui exécute l’instance SQL Server, pour l’évaluation de la base de données.
- Le pare-feu Windows s’exécutant sur la machine virtuelle SQL Server doit autoriser les connexions externes sur le port TCP 1433 (par défaut), afin que le DMA puisse se connecter.


## <a name="scenario-overview"></a>Présentation du scénario

Voici ce que nous allons faire :


> [!div class="checklist"]
> * **Étape 1 : préparer Azure**. Tout ce dont nous avons besoin, c’est d’un abonnement Azure.
> * **Étape 2 : télécharger et installer le DMA** : préparez le DMA pour l’évaluation de la base de données SQL Server locale.
> * **Étape 3 : évaluer la base de données** : exécutez et analysez l’évaluation de la base de données.
> * **Étape 4 : se préparer pour l’évaluation de la machine virtuelle avec Azure Migrate** : configurez les comptes locaux et ajustez les paramètres VMware.
> * **Étape 5 : détecter les machines virtuelles locales** : créez une machine virtuelle de collecteur Azure Migrate. Ensuite, exécutez le collecteur pour détecter les machines virtuelles pour l’évaluation.
> * **Étape 6 : se préparer pour l’analyse des dépendances** : installez les agents Azure Migrate sur les machines virtuelles, afin que nous puissions voir le mappage des dépendances entre les machines virtuelles.
> * **Étape 7 : évaluer les machines virtuelles**: vérifiez les dépendances, regroupez les machines virtuelles et effectuez l’évaluation. Une fois l’évaluation prête, analysez-la pour préparer la migration.


## <a name="step-1-prepare-azure"></a>Étape 1 : préparer Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.
- Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur pour l’abonnement, ou pour le groupe de ressources que vous utilisez dans ce scénario.


## <a name="step-2-download-and-install-the-dma"></a>Étape 2 : télécharger et installer le DMA

1. Téléchargez le DMA à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - Vous pouvez installer l’Assistant sur n’importe quelle machine pouvant se connecter à l’instance SQL. Vous n’avez pas besoin de l’exécuter sur la machine SQL Server.
    - Vous ne devez pas l’exécuter sur la machine de l’hôte SQL Server.
2. Double-cliquez sur le fichier d’installation téléchargé (DownloadMigrationAssistant.msi) pour démarrer l’installation.
3. Sur la page **Terminer**, vérifiez que **Launch Microsoft Data Migration Assistant** (Lancer l’Assistant Migration de données) est sélectionné et cliquez sur **Terminer**.

## <a name="step-3-run-and-analyze-the-database-assessment"></a>Étape 3 : exécuter et analyser l’évaluation de la base de données

Exécutez une évaluation pour analyser votre instance SQL Server source par rapport à une cible spécifiée.

1. Dans **Nouvelle**, sélectionnez **Évaluation** et donnez un nom de projet à l’évaluation.
2. Dans **Type du serveur source**, sélectionnez **SQL Server**. Dans **Type du serveur cible**, sélectionnez **SQL Server sur les machines virtuelles Azure**.

    ![Sélectionner une source](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      Actuellement, le DMA ne prend pas en charge l’évaluation pour la migration d’une instance SQL Managed Instance. Pour résoudre ce problème, nous utilisons SQL Server sur une machine virtuelle Azure en tant que cible supposée pour l’évaluation.

1.  Dans **Sélectionner une version cible**, spécifiez la version cible de SQL Server que vous souhaitez exécuter dans Azure, et ce que vous souhaitez détecter dans l’évaluation :
    - La section **Problèmes de compatibilité** vous indique les modifications qui peuvent interrompre la migration, ou qui nécessitent un ajustement mineur avant la migration. Elle vous indique également les fonctionnalités qui vous utilisez actuellement alors qu’elles sont déconseillées. Les problèmes sont classés par niveau de compatibilité. 
    - La section **Recommandation de nouvelles fonctionnalités** vous permet de découvrir les nouvelles fonctionnalités dans la plateforme SQL Server cible qui peuvent être utilisées pour votre base de données après la migration. Elles sont présentées dans des catégories : Performances, Sécurité et Stockage. 

    ![Sélectionner la cible](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. Dans **Connect to a server** (Se connecter à un serveur), spécifiez le nom de la machine qui exécute l’instance SQL Server, le type d’authentification et les détails de la connexion. Puis, cliquez sur **Se connecter**.

    ![Sélectionner la cible](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. Dans **Ajouter une source**, sélectionnez la base de données que vous souhaitez évaluer, puis cliquez sur **Ajouter**.
4. Une évaluation avec le nom spécifié est créée.

    ![Créer une évaluation](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  Cliquez sur **Suivant** pour démarrer l’évaluation.
6. Dans **Examiner les résultats**, vous verrez les résultats des tests de l’évaluation que vous avez lancés.


### <a name="analyze-the-database-assessment"></a>Analyser l’évaluation de la base de données

Les résultats s’affichent dans l’Assistant dès qu’ils sont disponibles. 

1. Dans le rapport **Problèmes de compatibilité**, vérifiez si votre base de données présente des problèmes pour chaque niveau de compatibilité et, le cas échéant, découvrez comment les résoudre. Vous trouverez ci-dessous le mappage des niveaux de compatibilité aux versions SQL Server :
    - 100 : SQL Server 2008/Azure SQL Database
    - 110 : SQL Server 2012/Azure SQL Database
    - 120 : SQL Server 2014/Azure SQL Database
    - 130 : SQL Server 2016/Azure SQL Database
    - 140 : SQL Server 2017/Azure SQL Database

    ![Problèmes de compatibilité](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. Dans le rapport **Recommandation de nouvelles fonctionnalités**, affichez les fonctionnalités de performances, de sécurité et de stockage que l’évaluation vous recommande de configurer après la migration. Un large éventail de fonctionnalités sont recommandées, y compris OLTP en mémoire et Columnstore, Stretch Database, Always Encrypted, Dynamic Data Masking et Transparent Data Encryption.

    ![Recommandations de nouvelles fonctionnalités](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Si vous avez corrigé des problèmes, cliquez sur **Redémarrer l’évaluation** pour relancer une évaluation. 
4. Cliquez sur **Exporter le rapport** pour obtenir le rapport d’évaluation au format JSON ou CSV.

Si vous exécutez une évaluation à une échelle plus importante :

- Vous pouvez exécuter plusieurs évaluations simultanément et afficher l’état des évaluations en ouvrant la page **All Assessments** (Toutes les évaluations).
- Vous pouvez [centraliser des évaluations dans une base de données SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Vous pouvez [centraliser des évaluations dans un rapport PowerBI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>Étape 4 : se préparer pour l’évaluation de la machine virtuelle avec Azure Migrate

Créez un compte VMware qu’Azure Migrate pourra utiliser pour détecter automatiquement les machines virtuelles à évaluer. Vérifiez que vous avez les autorisations pour créer une machine virtuelle, notez les ports qui doivent être ouverts et définissez le niveau des paramètres de statistiques.

### <a name="set-up-a-vmware-account"></a>Configurer un compte VMware

 Vous avez besoin d’un compte en lecture seule dans vCenter. Si vous n’en avez pas, créez un compte VMware avec les propriétés suivantes :

- Type d’utilisateur : au moins un utilisateur en lecture seule.
- Autorisations : objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule.
- Détails : l’utilisateur est affecté au niveau du centre de données et a accès à tous les objets du centre de données.
- Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

### <a name="verify-permissions-to-create-a-vm"></a>Vérifier les autorisations pour créer une machine virtuelle

Vérifiez que vous disposez des autorisations nécessaires pour créer une machine virtuelle en important un fichier au format .OVA. [Plus d’informations](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)

### <a name="verify-ports"></a>Vérifier les ports

Dans ce scénario, nous allons configurer le mappage des dépendances. Cette fonctionnalité nécessite qu’un agent soit installé sur les machines virtuelles que vous évaluez. Cet agent doit être en mesure de se connecter à Azure via le port TCP 443 sur chaque machine virtuelle. [En savoir plus](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) sur les exigences relatives à la connexion.


### <a name="set-statistics-settings"></a>Définir les paramètres de statistiques

Vous devez définir les paramètres de statistiques du serveur vCenter Server sur le niveau 3 avant de démarrer le déploiement. Notez les points suivants :
- Après avoir défini le niveau, vous devez attendre au moins un jour avant d’exécuter l’évaluation. Sinon, elle peut ne pas fonctionner comme prévu.
- Si le niveau est supérieur à 3, l’évaluation fonctionnera, mais :
    - Les données de performance des disques et du réseau ne seront pas collectées.
    - Pour le stockage, Azure Migrate recommande un disque standard dans Azure, de la même taille que le disque local.
    - Pour le réseau, pour chaque carte réseau locale, une carte réseau sera recommandée dans Azure.
    - Pour le calcul, Azure Migrate examine le nombre de cœurs et la taille de la mémoire de la machine virtuelle, et recommande une machine virtuelle Azure avec la même configuration. S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.
   
    
[En savoir plus](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) sur le dimensionnement avec le niveau 3.

Définissez le niveau comme suit :

1. Dans le client web vSphere, ouvrez l’instance de serveur vCenter Server.
2. Sélectionnez l’onglet **Gérer** et sous **Paramètres**, cliquez sur **Général**.
3. Cliquez sur **Modifier** et dans **Statistiques**, définissez le niveau des paramètres de statistiques sur **Niveau 3**.

    ![Niveau de statistiques vCenter](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>Étape 5 : détecter des machines virtuelles

Créez un projet Azure Migrate, téléchargez et configurez la machine virtuelle de collecteur. Ensuite, exécutez le collecteur pour détecter vos machines virtuelles.

### <a name="create-a-project"></a>Création d’un projet

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Créer une ressource**.
2. Recherchez **Azure Migrate**. Sélectionnez **Azure Migrate** dans les résultats de la recherche, puis cliquez sur **Créer**.
3. Spécifiez un nom pour le projet, ainsi que l’abonnement Azure.
4. Créez un groupe de ressources.
5. Spécifiez l’emplacement du projet, puis cliquez sur **Créer**.

    - Vous ne pouvez créer un projet Azure Migrate que dans la région Centre-Ouest ou Est des États-Unis.
    - Vous pouvez planifier une migration pour n’importe quel emplacement cible.
    - L’emplacement du projet est utilisé uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>Télécharger l’appliance collecteur

Azure Migrate crée une machine virtuelle locale connue en tant qu’appliance collecteur. Cette machine virtuelle découvre les machines virtuelles VMware sur site et envoie les métadonnées les concernant au service Azure Migrate. Pour configurer l’appliance collecteur, vous téléchargez un fichier .OVA, puis vous l’importez dans le serveur vCenter local pour créer la machine virtuelle.

1. Dans le projet Azure Migrate, cliquez sur **Démarrage** > **Découvrir et évaluer** > **Découvrir des machines**.
2. Dans **Découvrir des machines**, cliquez sur **Télécharger** pour télécharger le fichier .OVA.
3. Dans **Copier les informations d’identification du projet**, copiez l’ID de projet et la clé. Vous en aurez besoin pour la configuration du collecteur.

    ![Télécharger le fichier .ova](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>Vérifier l’appliance collecteur

Vérifiez que le fichier .OVA est sécurisé, avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Le code de hachage généré doit correspondre aux paramètres ci-après (version 1.0.9.7).
    
    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>Créer l’appliance de collecteur

Importez le fichier téléchargé sur le serveur vCenter.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).

    ![Déployer le modèle OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .OVA, puis cliquez sur **Suivant**.
3. Dans **OVF Template Details** (Détails du modèle OVF), cliquez sur **Suivant**. Dans **Contrat de licence utilisateur final**, cliquez sur **Accepter** pour accepter le contrat de licence, puis cliquez sur **Suivant**.
4. Dans **Name and Location** (Nom et emplacement), spécifiez un nom convivial pour la machine virtuelle du collecteur et l’emplacement d’inventaire dans lequel la machine virtuelle est hébergée. Cliquez ensuite sur **Suivant**. Spécifiez l’hôte ou le cluster sur lequel l’appliance de collecteur va s’exécuter.
5. Dans **Stockage**, spécifiez où vous souhaitez stocker les fichiers de l’appliance, puis cliquez sur **Suivant**.
6. Dans **Format disque**, spécifiez la méthode d’approvisionnement du stockage.
7. Dans **Network Mapping** (Mappage réseau), spécifiez le réseau auquel se connectera la machine virtuelle collector. Le réseau requiert une connexion à Internet pour envoyer des métadonnées vers Azure. 
8. Dans **Ready to Complete** (Prêt à terminer), passez en revue les paramètres, sélectionnez **Power on after deployment** (Mettre sous tension après le déploiement), puis cliquez sur **Terminer**.

Un message confirmant la réussite est émis après que l’appliance est créée.

### <a name="run-the-collector-to-discover-vms"></a>Exécutez le collecteur pour découvrir les machines virtuelles

Avant de commencer, veuillez noter qu’actuellement le collecteur prend uniquement en charge « Anglais (États-Unis) » comme langue du système d’exploitation et langue de l’interface du collecteur.

1. Dans la console du client vSphere, cliquez avec le bouton droit sur la machine virtuelle, puis choisissez **Open Console** (Ouvrir la console).
2. Indiquez les préférences de langue, de fuseau horaire et de mot de passe de l’appliance.
3. Sur le bureau, cliquez sur le raccourci **Run collector** (Exécuter le collecteur).

    ![Raccourci du collecteur](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. Dans Azure Migrate Collector, ouvrez **Set Up Prerequisites** (Configurer les prérequis).
    - Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - Le collecteur vérifie que la machine virtuelle a accès à Internet, que l’heure est synchronisée, que le service de collecteur s’exécute (il est installé par défaut sur la machine virtuelle). Il installe également VMWare PowerCLI. 
    
    > [!NOTE]
    > Nous partons du principe que la machine virtuelle a un accès direct à Internet, sans proxy.

    ![Vérifier la configuration requise](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. Dans **Specify vCenter Server details** (Spécifier les informations vCenter Server), procédez aux étapes suivantes :
    - Spécifiez le nom (FQDN) ou l’adresse IP du serveur vCenter.
    - Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte en lecture seule que le collecteur utilisera pour détecter les machines virtuelles sur le serveur vCenter Server.
    - Dans **Select scope** (Sélectionner une étendue), sélectionnez une étendue pour la découverte des machines virtuelles. Le collecteur peut uniquement découvrir les machines virtuelles situées dans l’étendue spécifiée. L’étendue peut être définie sur un dossier, un centre de données ou un cluster spécifique. Elle ne doit pas contenir plus de 1 000 machines virtuelles. 

    ![Se connecter à vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. Dans **Specify migration project** (Spécifier un projet de migration), spécifiez l’ID et la clé du projet Azure Migrate que vous avez copiés à partir du portail. Si vous ne les avez pas copiés, ouvrez le portail Azure à partir de la machine virtuelle collector. Dans la page **Vue d’ensemble** du projet, cliquez sur **Découvrir des machines**, puis copiez les valeurs.  

    ![Connexion à Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. Dans **View collection progress** (Afficher la progression de la collecte), surveillez le processus de détection et vérifiez que les métadonnées collectées à partir des machines virtuelles appartiennent à l’étendue spécifiée. Le collecteur fournit une durée approximative de la découverte.

    ![Collecte en cours](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

Une fois la collecte terminée, vérifiez que les machines virtuelles s’affichent dans le portail.

1. Dans le projet Azure Migrate, cliquez sur **Gérer** > **Machines**.
2. Vérifiez que les machines virtuelles que vous souhaitez détecter s’affichent dans le portail.

    ![Machines détectées](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Notez que les agents Azure Migrate ne sont pas encore installés sur les machines. Nous devons les installer pour pouvoir afficher les dépendances.
    
    ![Machines détectées](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>Étape 6 : se préparer pour l’analyse des dépendances

Pour afficher les dépendances entre les machines virtuelles que vous souhaitez évaluer, nous téléchargeons et installons des agents sur les machines virtuelles de l’application web : WEBVM et SQLVM.

### <a name="take-a-snapshot"></a>Prendre un instantané

Si vous souhaitez avoir une copie de votre machine virtuelle avant de la modifier, prenez un instantané avant d’installer les agents.

![Instantané de la machine virtuelle](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles

1.  Sur la page **Machines** du projet Azure Migrate, sélectionnez la machine et cliquez sur **Installation requise** dans la colonne **Dépendances**.
2.  Sur la page **Détecter des machines**, téléchargez et installez Microsoft Monitoring Agent (MMA) et l’agent de dépendances pour chacune des machines virtuelles.
3.  Copiez l’ID et la clé de l’espace de travail. Ces éléments sont nécessaires lorsque vous installez le MMA.

    ![Téléchargement de l’agent](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>Installer MMA

1. Double-cliquez sur l’agent téléchargé.
2. Sur la page d’**accueil**, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
3. Dans **Dossier de destination**, conservez le dossier d’installation par défaut > **Suivant**. 
4. Dans **Options d’installation de l’agent**, sélectionnez **Connect the agent to Azure Log Analytics**(Connecter l’agent à Azure Log Analytics) > **Suivant**. 

    ![Installation de MMA](./media/migrate-scenarios-assessment/mma-install.png) 
5. Dans **Azure Log Analytics**, collez l’ID et la clé de l’espace de travail que vous avez copiés sur le portail. Cliquez sur **Suivant**.
    ![Installation de MMA](./media/migrate-scenarios-assessment/mma-install2.png) 

6. Dans **Prêt pour l’installation**, installez le MMA.



#### <a name="install-the-dependency-agent"></a>Installer l’agent de dépendances

1.  Double-cliquez sur l’agent de dépendances téléchargé.
2.  Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
3.  Dans **Installation**, attendez que l’installation se termine. Cliquez ensuite sur **Suivant**.

    ![Agent de dépendances](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>Étape 7 : exécuter et analyser l’évaluation de la machine virtuelle

Vérifiez les dépendances de la machine et créez un groupe. Ensuite, exécutez l’évaluation.

### <a name="verify-dependencies-and-create-a-group"></a>Vérifiez les dépendances et créez un groupe.

1.  Sur la page **Machines**, pour les machines virtuelles que vous souhaitez analyser, cliquez sur **Afficher les dépendances**.

    ![Afficher les dépendances de machine](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. Pour la machine virtuelle SQLVM, la carte des dépendances affiche les informations suivantes :

    - Groupes de processus/processus avec des connexions réseau actives s’exécutant sur la machine virtuelle SQLVM au cours d’une période spécifiée (une heure par défaut).
    - Connexion TCP entrantes (client) et sortantes (serveur) vers et depuis toutes les machines dépendantes.
    - Les machines dépendantes avec les agents Azure Migrate installés sont affichées dans des zones distinctes.
    - Les machines sans agents installés affichent des informations de port et d’adresse IP.
    
 3. Pour les machines avec l’agent installé (WEBVM), cliquez sur la zone de la machine pour voir plus d’informations, notamment le nom de domaine complet, le système d’exploitation, l’adresse MAC. 

    ![Afficher les dépendances de groupe](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. À présent, sélectionnez les machines virtuelles que vous souhaitez ajouter au groupe (SQLVM et WEBVM).  Utilisez CTRL + clic pour sélectionner plusieurs machines virtuelles.
5. Cliquez sur **Créer un groupe**et spécifiez un nom (smarthotelapp).

> [!NOTE]
    > Pour afficher des dépendances plus précises, vous pouvez étendre l’intervalle de temps. Vous pouvez sélectionner une durée spécifique ou des dates de début et de fin. 


### <a name="run-an-assessment"></a>Exécuter une évaluation


1. Sur la page **Groupes** , ouvrez le groupe (smarthotelapp)
2. Cliquez sur **Créer une évaluation**.

    ![Créer une évaluation](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. L’évaluation s’affiche sur la page **Gérer** > **Évaluations**.


### <a name="modify-assessment-settings"></a>Modifier les paramètres d’évaluation

Dans ce didacticiel, nous avons utilisé les paramètres d’évaluation par défaut, mais vous pouvez les personnaliser, comme suit :

1. Dans la page **Évaluations** du projet de migration, sélectionnez l’évaluation et cliquez sur **Modifier des propriétés**.
2. Modifiez les propriétés conformément au tableau suivant :

    **Paramètre** | **Détails** | **Par défaut**
    --- | --- | ---
    **Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer | Aucune valeur par défaut.
    **Redondance du stockage** | Type de redondance du stockage que les machines virtuelles Azure utiliseront après la migration. | La valeur par défaut est [Stockage localement redondant (LRS)](../storage/common/storage-redundancy-lrs.md). Azure Migrate prend uniquement en charge les évaluations basées sur des disques managés, et les disques managés prennent uniquement en charge le stockage LRS. Par conséquent, seule l’option LRS est disponible. 
    **Critère de dimensionnement** | Critère utilisé par Azure Migrate pour dimensionner correctement les machines virtuelles pour Azure. Vous pouvez effectuer un dimensionnement *en fonction des performances* ou dimensionner les machines virtuelles *comme localement*, sans tenir compte de l’historique des performances. | Le dimensionnement en fonction des performances est l’option par défaut.
    **Historique des performances** | Durée à prendre en compte pour évaluer les performances des machines virtuelles. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*. | La valeur par défaut est une journée.
    **Utilisation en centile** | Valeur de centile du jeu d’échantillons de performances devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*.  | La valeur par défaut est le 95e centile.
    **Niveau tarifaire** | Vous pouvez spécifier le [niveau tarifaire (de base/standard)](../virtual-machines/windows/sizes-general.md) des machines virtuelles Azure cibles. Par exemple, si vous envisagez de migrer un environnement de production, vous pouvez prendre en compte le niveau Standard, qui fournit des machines virtuelles avec une faible latence, mais est sans doute plus coûteux. En revanche, dans un environnement de développement et de test, vous pouvez prendre en compte le niveau de base, qui fournit des machines virtuelles avec une latence plus élevée, moins coûteuses. | Par défaut le niveau [Standard](../virtual-machines/windows/sizes-general.md) est utilisé.
    **Facteur de confort** | Azure Migrate considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs. | Le paramètre par défaut est 1.3x.
    **Offer** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) pour laquelle vous êtes inscrit. | [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) est la valeur par défaut.
    **Devise** | Devise de facturation. | La valeur par défaut est le dollar américain.
    **Remise (%)** | Remise propre à un abonnement cumulable avec l’offre Azure. | Le paramètre par défaut est 0 %.
    **Azure Hybrid Benefit** | Spécifiez si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. | La valeur par défaut est Oui.

3. Cliquez sur **Enregistrer** pour mettre à jour les paramètres de l’évaluation.


### <a name="analyze-the-vm-assessment"></a>Analyser l’évaluation de la machine virtuelle

Une évaluation Azure Migrate inclut des informations sur la compatibilité des machines virtuelles locales pour Azure, la taille idéale suggérée pour la machine virtuelle Azure et l’estimation des coûts Azure mensuels. 

![Rapport d’évaluation](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

![Affichage de l’évaluation](./media/migrate-scenarios-assessment/assessment-display.png)

Votre évaluation obtient un niveau de confiance compris entre 1 étoile et 5 étoiles (1 étoile étant le niveau le plus bas et 5 étoiles le niveau le plus élevé).
- Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation.
- Il vous permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate.
- Le niveau de confiance est utile quand vous effectuez un *dimensionnement basé sur les performances*, car Azure Migrate peut ne pas disposer de suffisamment de points de données pour effectuer un dimensionnement basé sur l’utilisation. Pour le *dimensionnement local*, le niveau de confiance s’élève toujours à 5 étoiles, car Azure Migrate dispose de tous les points de données nécessaires au dimensionnement de la machine virtuelle.
- Selon le pourcentage de points de données disponibles, le niveau de confiance pour l’évaluation est fourni :

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles

#### <a name="verify-readiness"></a>Vérifier l’état de préparation

![Préparation de l’évaluation](./media/migrate-scenarios-assessment/azure-readiness.png)  

Le rapport d’évaluation affiche les informations résumées dans le tableau. Notez que pour afficher le dimensionnement basé sur les performances, Azure Migrate a besoin des informations suivantes. Si ces informations ne peuvent pas être collectées, l’évaluation du dimensionnement risque de ne pas être suffisamment précise.

- Données d’utilisation pour le processeur et la mémoire.
- E/S par seconde en lecture et en écriture de chaque disque attaché à la machine virtuelle.
- Informations entrantes/sortantes du réseau pour chaque carte réseau attachée à la machine virtuelle.


**Paramètre** | **Indication** | **Détails**
--- | --- | ---
**Préparation des machines virtuelles pour Azure** | Indique si la machine virtuelle est prête pour la migration | États possibles :</br><br/>- Prête pour Azure<br/><br/>- Prête sous conditions <br/><br/>- Pas prête pour Azure<br/><br/>- État de préparation inconnue<br/><br/> Si une machine virtuelle n’est pas prête, nous allons vous présenter une procédure de correction.
**Taille de la machine virtuelle Azure** | Pour les machines virtuelles prêtes, nous vous recommandons une taille de machine virtuelle Azure. | La recommandation de taille dépend des propriétés de l’évaluation :<br/><br/>- Si vous avez utilisé un dimensionnement basé sur les performances, le dimensionnement prend en compte l’historique des performances des machines virtuelles.<br/><br/>- Si vous avez utilisé un dimensionnement local, le dimensionnement est basé sur la taille de la machine virtuelle locale, et les données d’utilisation ne sont pas utilisées.
**Outil suggéré** | Puisque nos machines exécutent les agents, Azure Migrate examine les processus exécutés à l’intérieur de la machine et indique si celle-ci est une machine de base de données ou non.
**VM information** (Informations de machine virtuelle) | Le rapport affiche les paramètres de la machine virtuelle locale, y compris des informations sur le système d’exploitation, le type de démarrage, le disque et le stockage.




#### <a name="review-monthly-cost-estimates"></a>Examiner les estimations des coûts mensuels

Cette vue affiche le coût total du calcul et du stockage de l’exécution des machines virtuelles dans Azure avec les détails pour chaque machine. 

![Préparation de l’évaluation](./media/migrate-scenarios-assessment/azure-costs.png) 

- Les estimations de coûts sont calculées à l’aide des recommandations de taille pour une machine.
- L’estimation des coûts mensuels de calcul et de stockage est agrégée pour toutes les machines virtuelles dans le groupe. 


## <a name="conclusion"></a>Conclusion

Dans ce scénario, nous avons :

> [!div class="checklist"]
> * Évalué notre base de données locale avec l’outil DMA.
> * Évalué nos machines virtuelles locales à l’aide du mappage des dépendances avec le service Azure Migrate.
> * Examiné les évaluations pour nous assurer que nos ressources locales sont prêtes pour la migration vers Azure.

## <a name="next-steps"></a>Étapes suivantes

Nous allons continuer avec le scénario suivant, pour effectuer une migration lift-and-shift des machines virtuelles locales vers Azure.



