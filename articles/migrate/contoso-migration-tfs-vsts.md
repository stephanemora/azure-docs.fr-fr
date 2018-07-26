---
title: Refactoriser un déploiement de Team Foundation Server vers Visual Studio Team Services (VSTS) dans Azure | Microsoft Docs
description: Découvrez comment Contoso refactorise son déploiement local de Team Foundation Server (TFS) en le migrant vers Visual Studio Team Services (VSTS) dans Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 05340c8504150ed568e0d5ce5c8250127e59bca0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003235"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-visual-studio-team-services-vsts"></a>Migration de Contoso : refactoriser un déploiement de Team Foundation Server vers Visual Studio Team Services (VSTS) dans Azure

Cet article explique comment Contoso refactorise son déploiement local de Team Foundation Server (TFS) en le migrant vers Visual Studio Team Services (VSTS) dans Azure. L’équipe de développement de Contoso a utilisé Team Foundation Server pour la collaboration d’équipe et le contrôle de code source pendant les cinq dernières années. À présent, ils souhaitent passer à une solution cloud pour le travail de développement et de test, ainsi que pour le contrôle de code source. VSTS jouera un rôle comme lors du passage à un modèle de DevOps, et développeront de nouvelles applications cloud natives.

Ce document est le onzième d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations d’arrière-plan et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios croissant en complexité, nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les scénarios de migration de Contoso. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md)  | Montre comment Contoso évalue son application à deux niveaux locale SmartHotel s’exécutant sur VMware. Ils évaluent les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : ré-héberger sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso migre l’application SmartHotel vers Azure. Ils migrent la machine virtuelle web de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application à l’aide du service [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) pour opérer la migration vers une instance SQL Managed Instance. | Disponible
[Article 5 : Réhéberger sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre son application SmartHotel vers des machines virtuelles Azure en utilisant le service Site Recovery.
[Article 6 : ré-héberger sur des machines virtuelles Azure et des groupes de disponibilité SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Ils utilisent Site Recovery pour migrer les machines virtuelles de l’application, et Database Migration Service pour migrer la base de données de l’application vers un groupe de disponibilité SQL Server. | Disponible
[Article 7 : Réhéberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso migre son application osTicket Linux vers des machines virtuelles Azure IaaS à l’aide d’Azure Site Recovery.
[Article 8 : ré-héberger une application Linux sur des machines virtuelles Azure et Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux. Ils utilisent Site Recovery pour la migration de machine virtuelle et MySQL Workbench pour migrer vers une instance de serveur MySQL Azure. | Disponible
[Article 9 : Refactoriser une application vers une application web Azure et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Montre comment Contoso migre l’application SmartHotel vers une application web basée sur un conteneur Azure, et migre la base de données de l’application vers Azure SQL Server. | Disponible
[Article 10 : Refactoriser une application Linux vers Azure App Service et un serveur MySQL sur Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Montre comment Contoso migre l’application Linux osTicket vers Azure App Service à l’aide d’un conteneur Docker PHP 7.0. La base de code pour le déploiement est migrée vers GitHub. La base de données d’application est migrée vers MySQL sur Azure. | Disponible
Article 11 : Refactoriser un déploiement de Team Foundation Server dans VSTS | Migrer le Team Foundation Server de l’application de développement vers VSTS dans Azure | Cet article
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Montre comment Contoso migre et réarchitecture son application SmartHotel vers Azure. Il réarchitecturent la couche web d’application en tant que conteneur Windows, et la base de données d’application en une base de données Azure SQL Database. | Disponible
[Article 13 : Régénérer une application dans Azure](contoso-migration-rebuild.md) | Montre comment Contoso régénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, dont App Services, Azure Kubernetes, Azure Functions, Cognitive services et Cosmos DB. | Disponible


## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec les commerciaux pour identifier des objectifs futurs. Les partenaires commerciaux ne se sentent pas trop concernés par les outils et technologies de développement, mais ils ont saisi les points suivants :

- **Logiciels** : quel que soit le cœur de métier, toutes les entreprises, y compris Contoso, sont désormais des éditeurs de logiciels. La direction commerciale s’intéresse à la manière dont l’informatique peut aider à diriger l’entreprise avec nouvelles pratiques de travail pour les utilisateurs, et de nouvelles expériences pour les clients.
- **Efficacité** : Contoso a besoin de simplifier les processus et de supprimer des procédures inutiles pour les développeurs et les utilisateurs. Cela leur permettra de répondre plus efficacement aux exigences des clients. L’entreprise a besoin que l’informatique accélère les processus, sans perte de temps ou argent.
- **Agilité** : l’informatique de Contoso doit répondre aux besoins métier et réagir plus rapidement que le marché pour permettre à l’entreprise de prospérer dans une économie mondiale. L’informatique ne doit pas entraver l’activité.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de la migration vers VSTS :

- Ils ont besoin d’un outil pour migrer les données vers le cloud. Les processus manuels requis devraient être peu nombreux.
- Les données d’élément de travail et l’historique de l’année dernière doivent être migrés.
- Ils ne souhaitent pas configurer de nouveaux noms et mots de passe d’utilisateur. Toutes les attributions système actuelles doivent être conservées.
- Ils souhaitent passer de Team Foundation Version Control (TFVC) à Git pour le contrôle de code source.
- Le basculement vers Git sera une migration constituant à importer uniquement la dernière version du code source. Elle aura lieu durant un temps d’arrêt, quand tout travail sera interrompu lors du déplacement de la base de code. Ils comprennent que seul l’historique de la branche maître en cours sera disponible après la migration.
- Ils sont préoccupés par la modification et souhaitent le tester avant de procéder à une migration complète. Ils souhaitent conserver l’accès à TFS, même après la migration vers VSTS.
- Ils ont plusieurs collections et souhaitent commencer avec l’une d’elles ne contenant quelques projets afin de mieux comprendre le processus.
- Ils comprennent que les collections TFS étant une relation de un à un avec les comptes VSTS, elles ont plusieurs URL. Toutefois, cela correspond à leur modèle actuel de séparation pour les bases de code et les projets.


## <a name="proposed-architecture"></a>Architecture proposée

- Contoso migre ses projets TFS vers le cloud, et n’héberge plus ses projets ou son contrôle de code source en local.
- TFS sera migré vers VSTS.
- Contoso possède actuellement une collection TFS nommée **ContosoDev**, qui seront migrée vers un compte VSTS appelé **contosodevmigration.visualstudio.com**.
- Les projets, les éléments de travail, les bogues et les itérations de l’année dernière seront migrés vers VSTS.
- Contoso exploitera son Azure Active Directory configuré lors du [déploiement de son infrastructure Azure](contoso-migration-infrastructure.md) au début de la planification de sa migration. 


![Architecture du scénario](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Processus de migration

Contoso va effectuer le processus de migration comme suit :

1. Cela implique beaucoup de préparation. Dans un premier temps, Contoso doit mettre à niveau son implémentation de TFS vers un niveau pris en charge. Ils exécutent actuellement TFS 2017 Update 3 mais, pour utiliser la migration de base de données, ils ont besoin exécuter une version 2018 prise en charge avec les dernières mises à jour.
2. Après la mise à niveau, ils exécutent l’outil de migration de TFS et valident leur collection.
3. Ils génèrent un ensemble de fichiers de préparation et effectuent un test de migration.
4. Ils exécutent ensuite une autre migration, cette fois complète, qui inclut les éléments de travail, les bogues, les sprints et le code.
5. Après la migration, ils migrent leur code de TFVC vers Git.

![Processus de migration](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Azure va effectuer la migration :

> [!div class="checklist"]
> * **Étape 1 : créer un compte de stockage Azure** : ce compte de stockage sera utilisé pendant le processus de migration.
> * **Étape 2 : mettre à niveau TFS** : ils vont mettre à niveau à leur déploiement vers TFS 2018 Update 2. 
> * **Étape 3 : valider la collection** : ils vont valider leur collection TFS en préparation de la migration.
> * **Étape 4 : créer les fichiers de migration** : ils vont créer des fichiers de migration à l’aide de l’outil de migration de TFS. 


## <a name="step-1-create-a-storage-account"></a>Étape 1 : créer un compte de stockage

1. Dans le portail Azure, Contoso crée un compte de stockage (**contosodevmigration**).
2. Ils placent le compte dans leur région secondaire qu’ils utilisent pour le basculement, USA Centre. Ils utilisent un compte standard à usage général avec un stockage localement redondant.

    ![Compte de stockage](./media/contoso-migration-tfs-vsts/storage1.png) 


**Besoin de plus d’aide ?**

- [Présentation du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Étape 2 : mettre à niveau TFS

Contoso met à niveau sont serveur TFS vers TFS 2018 Update 2. Avant de commencer :

- Ils téléchargent [TFS 2018 Update 2](https://visualstudio.microsoft.com/downloads/).
- Ils vérifient la [configuration matérielle requise](https://docs.microsoft.com/tfs/server/requirements), et lisent les [notes de publication](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) et les [astuces de mise à niveau](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

Ils mettent à niveau comme suit :

1. Pour commencer, ils sauvegardent leur serveur TFS (s’exécutant sur une machine virtuelle VMware) et prennent une capture instantanée VMware.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. Le programme d’installation de TFS démarre, puis ils choisissent l’emplacement d’installation. Le programme d’installation requiert un accès Internet.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. Une fois l’installation terminée, l’Assistant Configuration du serveur démarre.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Après vérification, l’Assistant accomplit la mise à niveau.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. Contoso vérifie l’installation de TFS en examinant les projets, les éléments de travail et le code.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Certaines mises à niveau de TFS nécessitent l’exécution de l’Assistant Configuration des fonctionnalités une fois la mise à niveau terminée. [Plus d’informations](https://docs.microsoft.com/vsts/work/customize/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts)

**Besoin de plus d’aide ?**

Apprenez-en davantage sur la [mise à niveau de TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Étape 3 : valider la collection de TFS

Contoso exécute l’outil de migration de TFS par rapport à la base de données de collection ContosoDev afin de la valider avant la migration.

1. Contoso télécharge et décompresse l’[outil de migration de TFS](https://www.microsoft.com/download/details.aspx?id=54274). Il est important de télécharger la version correspondant à la mise à jour de TFS en cours d’exécution. La version peut être vérifiée dans la console d’administration.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. Ils exécutent l’outil pour effectuer la validation, en spécifiant l’URL de la collection de projets d’équipe :

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. L’outil affiche une erreur.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Ils ont localisé les fichiers journaux situés dans le dossier **Journaux**, juste avant l’emplacement de l’outil. Un fichier journal est généré pour chaque validation majeure. **TfsMigration.log** contient les informations principales.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Contoso recherche cette entrée liée à une identité.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Contoso exécute **TfsMigration validate /help** dans la ligne de commande, et constate que la commande **/tenantDomainName** semble être requise pour valider les identités.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Ils réexécutent la commande de validation, et incluent cette valeur, ainsi que leur nom Azure AD : **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Un écran de connexion AD Azure s’affiche, et ils entrent les informations d’identification d’un utilisateur administrateur général.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. La validation réussit et est confirmée par l’outil.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Étape 4 : créer les fichiers de migration

Une fois la validation terminé, Contoso peut utiliser l’outil de migration de TFS pour générer les fichiers de migration.

1. Ils exécutent l’étape de préparation dans l’outil.

    **TfsMigrator prepare /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Préparation](./media/contoso-migration-tfs-vsts/prep1.png)

    La préparation inclut les opérations suivantes :
    - Analyse la collection trouver la liste de tous les utilisateurs et, remplit le journal de mappage des identités (**IdentityMapLog.csv**]).
    - Prépare la connexion à Azure Active Directory afin de trouver une correspondance pour chaque identité.
    - Contoso a déjà déployé Azure AD et l’a synchronisé à l’aide d’AD Connect, de façon à ce que la préparation puisse trouver les identités correspondantes et les marquer comme actives.

2. Un écran de connexion à AD Azure s’affiche, et Contoso entre les informations d’identification d’un administrateur général.

    ![Préparation](./media/contoso-migration-tfs-vsts/prep2.png)

3. La préparation s’achève, et l’outil signale que les fichiers d’importation ont été correctement générés.

    ![Préparation](./media/contoso-migration-tfs-vsts/prep3.png)

4. Contoso peut à présent voir que les fichiers IdentityMapLog.csv et import.json ont été créés dans un nouveau dossier.

    ![Préparation](./media/contoso-migration-tfs-vsts/prep4.png)

5. Le fichier import.json fournit les paramètres d’importation. Il inclut des informations telles que le nom de compte souhaité et les informations du compte de stockage. La plupart des champs sont complétés automatiquement. Certains champs requis une entrée utilisateur. Contoso ouvre le fichier et ajoute le nom du compte VSTS à créer : **contosodevmigration**. Avec ce nom, leur URL de VSTS sera **contosodevmigration.visualstudio.com**.

    ![Préparation](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > Le compte doit être créé avant la migration. Il est toujours possible de le modifier par la suite.

6. Ils examinent le fichier de mappage du journal des identités, qui montre les comptes qui seront introduits dans VSTS lors de l’importation. 

    - Les identités actives font référence aux identités qui deviendront des utilisateurs dans VSTS après l’importation.
    - Dans VSTS, ces identités seront sous licence et apparaîtront en tant qu’utilisateurs dans le compte après la migration.
    - Ces identités sont marquées comme **Active** (Actives) dans la colonne **Expected Import Status** (État d’importation attendu) dans le fichier.

    ![Préparation](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-vsts"></a>Étape 5 : migrer vers VSTS

Une fois la préparation en place, Contoso peut se concentrer sur la migration. Après avoir exécuté la migration, ils passent de TFVC à Git pour la gestion de version.

Avant de commencer, Contoso planifie un temps d’arrêt avec l’équipe de développement pour mettre la collection hors connexion en vue de la migration. Les étapes du processus de migration sont les suivantes :

1. **Détacher la collection** : les données d’identité pour la collection résident dans la base de données de configuration de serveur de TFS, tandis que la collection est attachée et en ligne. Quand une collection est détachée du serveur TFS, celui-ci dispose d’une copie de ces données d’identité et les empaquète avec la collection pour le transport. Sans ces données, la partie identité de l’importation ne peut pas être exécutée. Il est recommandé que la collection restent détachée jusqu'à ce que l’importation soit terminée, car il n’existe aucun moyen d’importer les modifications qui se produisent lors de l’importation.
2. **Générer une sauvegarde** : l’étape suivante du processus de migration consiste à générer une sauvegarde qui peut être importée dans VSTS. Le package d'application de la couche Données (DACPAC) est une fonctionnalité de SQL Server qui permet d’empaqueter les modifications de base de données dans un seul fichier, et de les déployer vers d’autres instances de SQL. Il peut également être directement restauré dans VSTS et est donc utilisé comme méthode d’empaquetage pour obtenir des données de collection dans le cloud. Contoso va utiliser l’outil SqlPackage.exe pour générer le DACPAC. Cet outil est inclus dans SQL Server Data Tools.
3. **Charger vers le stockage** : une fois le DACPAC créé, ils le chargent vers le Stockage Azure. Une fois le DACPAC chargé, ils obtiennent une signature d’accès partagé (SAP) pour autoriser l’accès de l’outil de migration de TFS au stockage.
4. **Remplir l’importation** : Contoso peut ensuite remplir les champs manquants dans le fichier d’importation, dont le paramètre DACPAC. Pour commencer, ils vont spécifier ce qu’ils souhaitent faire, à savoir un **Test** d’importation, pour vérifier que tout fonctionne correctement avant la migration complète.
5. **Effectuer un test** : des tests d’importation aident à tester la migration de la collection. Les tests ont une durée de vie limitée et sont supprimés avant l’exécution d’une migration en production. Ils sont supprimés automatiquement après un laps de temps défini. Une remarque concernant le moment de suppression du test est incluse dans l’e-mail de notification de réussite reçu une fois l’importation terminée. Prenez-en note et planifiez en conséquence.
6. **Effectuer la migration en production** : une fois le test de migration terminé, Contoso effectue la migration finale en mettant à jour le fichier import.json, puis en réexécutant l’importation.



### <a name="detach-the-collection"></a>Détacher la collection

Avant de commencer, Contoso prend une sauvegarde locale de SQL Server, et une capture instantanée VMware du serveur TFS, avant d’opérer le détachement.

1.  Dans la console d’administration de TFS, ils sélectionnent la collection à détacher (**ContosoDev**).

    ![Migrer](./media/contoso-migration-tfs-vsts/migrate1.png)

2. Dans **Général**, ils sélectionnent **détacher une Collection**.

    ![Migrer](./media/contoso-migration-tfs-vsts/migrate2.png)

3. Dans l’Assistant Détacher la collection de projets d'équipe > **Message de maintenance**, ils fournissent un message à l’adresse des utilisateurs qui pourraient essayer de se connecter à des projets dans la collection.

    ![Migrer](./media/contoso-migration-tfs-vsts/migrate3.png)

4. Dans **Progression du détachement**, ils surveillent la progression, puis cliquent sur **Suivant** une fois le processus terminé.

    ![Migrer](./media/contoso-migration-tfs-vsts/migrate4.png)

5. Dans **Vérifications de disponibilité**, une fois les vérifications terminées, ils cliquent sur **Détacher**.

    ![Migrer](./media/contoso-migration-tfs-vsts/migrate5.png)

6. Pour terminer, ils cliquent sur **Fermer**.

    ![Migrer](./media/contoso-migration-tfs-vsts/migrate6.png)

6. La collection n’est plus référencée dans la console d’administration de TFS.

    ![Migrer](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Générer un DACPAC

Contoso crée une sauvegarde (DACPAC) à importer dans VSTS.

- SqlPackage.exe dans SQL Server Data Tools est utilisé pour créer le DACPAC. Il existe plusieurs versions de SqlPackage.exe installées avec SQL Server Data Tools, situées dans des dossiers portant des noms tels que 120, 130 et 140. Il est important d’utiliser la version appropriée pour préparer le DACPAC.
- Les importations de TFS 2018 doivent utiliser SqlPackage.exe à partir du dossier 140 ou d’un dossier supérieur.  Pour CONTOSOTFS, ce fichier se trouve dans le dossier : **C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Contoso génère le DACPAC comme suit :

1. Ils ouvrent une invite de commandes et accèdent à l’emplacement de SQLPackage.exe. Ils tapent la commande suivante pour générer le DACPAC :

    **SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory** 

    ![Sauvegarde](./media/contoso-migration-tfs-vsts/backup1.png)

2. Le message suivant s’affiche après exécution de la commande s’exécute.

    ![Sauvegarde](./media/contoso-migration-tfs-vsts/backup2.png)

3. Ils vérifient les propriétés de DacpacFile.

    ![Sauvegarde](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Mise à jour du fichier sur le stockage

Une fois le fichier DACPAC créé, Contoso le charge vers le Stockage Azure.

1. Ils [téléchargent et installent](https://azure.microsoft.com/features/storage-explorer/) l’Explorateur Stockage Azure.

    ![Télécharger](./media/contoso-migration-tfs-vsts/backup5.png)

4. Ils se connectent à leur abonnement et recherchent le compte de stockage créé pour la migration (**contosodevmigration**). Ils créent un nouveau conteneur d’objets blob, **vstsmigration**.

    ![Télécharger](./media/contoso-migration-tfs-vsts/backup6.png)

5. Ils spécifient le fichier DACPAC à charger en tant qu’objet blob de blocs.

    ![Télécharger](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. Une fois le fichier chargé, ils cliquent sur le nom de fichier > **Générer une signature d'accès partagé**. Ils développent les conteneurs d’objets blob sous le compte de stockage, sélectionnent le conteneur avec les fichiers d’importation, puis cliquent sur **Get Shared Access Signature** (obtenir une signature d’accès partagé).

    ![Télécharger](./media/contoso-migration-tfs-vsts/backup8.png)

8. Ils acceptent les valeurs par défaut, puis cliquent sur **Créer**. Cela permet un accès pendant 24 heures.

    ![Télécharger](./media/contoso-migration-tfs-vsts/backup9.png)

9. Ils copient l’URL de la signature d’accès partagé afin que l’outil de migration de TFS puisse l’utiliser.

    ![Télécharger](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> La migration doit se produire avant, dans la fenêtre de temps autorisée, sans quoi les autorisations expirent.
> Ne générez pas de clé SAP à partir du portail Azure. Les clés générées telles que celle-ci ont une portée de compte, et ne fonctionnent pas avec l’importation.

### <a name="fill-in-the-import-settings"></a>Renseigner les paramètres d’importation

Précédemment, Contoso remplissait partiellement le fichier de spécification d’importation (import.json). Désormais, il doivent ajouter les paramètres restants.

Ils ouvrent le fichier import.json, puis remplissent les champs suivants : •   Location : emplacement de la clé SAP générée ci-dessus.
•   Dacpac : définissez le nom du fichier DACPAC que vous avez téléchargé pour le compte de stockage. Incluez l’extension « .dacpac ».
•   ImportType : définissez la valeur DryRun pour l’instant.


![Paramètres d’importation](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Effectuer une migration test

Contoso commence par une migration test pour s’assurer que tout fonctionne comme prévu.

1. Ils ouvrent une invite de commandes et recherchent l’emplacement TfsMigration (C:\TFSMigrator).
2. Dans un premier temps, ils valident le fichier d’importation. Ils veulent s’assurer que le fichier est correctement formaté, et que la clé SAP fonctionne.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

3. La validation retourne une erreur indiquant que la clé SAP a besoin d’un délai d’expiration plus long.

    ![Test](./media/contoso-migration-tfs-vsts/test1.png)

3. Ils utilisent l’Explorateur Stockage Azure pour créer une nouvelle clé SAP dont l’expiration est définie sur sept jours.

    ![Test](./media/contoso-migration-tfs-vsts/test2.png)

3. Ils mettent à jour le fichier import.json, puis réexécutent la validation. Cette fois, elle se termine correctement.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Test](./media/contoso-migration-tfs-vsts/test3.png)
    
7. Ils commencent à exécuter le test :

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. Un message est émis pour confirmer la migration. Notez la durée de conservation des données intermédiaires après le test.

    ![Test](./media/contoso-migration-tfs-vsts/test4.png)

9. La connexion à Azure AD doit être effectuée avec l’ID d’administrateur de Contoso.

    ![Test](./media/contoso-migration-tfs-vsts/test5.png)

10. Un message affiche des informations sur l’importation.

    ![Test](./media/contoso-migration-tfs-vsts/test6.png)

11. Après environ 15 minutes, Contoso accède à l’URL et voit les informations suivantes :

     ![Test](./media/contoso-migration-tfs-vsts/test7.png)

12. Une fois la migration terminée, un responsable du développement de Contoso se connecte à VSTS pour vérifier que le test a fonctionné correctement. Une fois l’authentification effectuée, VSTS a besoin de quelques informations pour confirmer le compte.

    ![Test](./media/contoso-migration-tfs-vsts/test8.png)

13. Dans VSTS, le responsable du développement peut voir que les projets ont été migrés vers VSTS. Un avis indique que le compte sera supprimé dans 15 jours.

    ![Test](./media/contoso-migration-tfs-vsts/test9.png)

14. Le responsable du développement ouvre l’un des projets, et ouvre les **éléments de travail** > **qui luis sont assignés**. Cela montre que données de l’élément de travail ont été migrées, avec son identité.

    ![Test](./media/contoso-migration-tfs-vsts/test10.png)

15. Il vérifie également d’autres projets et codes pour confirmer que le code source et l’historique ont été migrés.

    ![Test](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Exécuter la migration en production

Une fois le test terminé, Contoso passe à la migration en production. Ils suppriment le test, mettent à jour les paramètres d’importation, puis relancent l’importation.

1. Dans le portail VSTS, ils suppriment le compte de test.
2. Ils mettent à jour le fichier import.json pour définir **ImportType** sur **ProductionRun**.

    ![Production](./media/contoso-migration-tfs-vsts/full1.png)

3. Ils démarrent la migration comme ils l’ont fait pour le test : **TfsMigrator import /importFile:C:\TFSMigrator\import.json**.
4. Un message s’affiche pour confirmer la migration et vous avertir que les données pourraient être stockées dans un emplacement sécurisé en tant que zone de transit jusqu'à sept jours.

    ![Production](./media/contoso-migration-tfs-vsts/full2.png)

5. Dans l’interface de connexion à Azure AD, Contoso spécifie un ID d’administrateur de Contoso.

    ![Production](./media/contoso-migration-tfs-vsts/full3.png)

6. Un message affiche des informations sur l’importation.

    ![Production](./media/contoso-migration-tfs-vsts/full4.png)

7. Après environ 15 minutes, Contoso accède à l’URL, et voit les informations suivantes :

    ![Production](./media/contoso-migration-tfs-vsts/full5.png)

8. Une fois la migration terminée, un responsable du développement de Contoso se connecte à VSTS pour vérifier que la migration a fonctionné correctement. Une fois connecté, il peut voit que les projets ont été migrés.

    ![Production](./media/contoso-migration-tfs-vsts/full6.png)

8. Le responsable du développement ouvre l’un des projets, et ouvre les **éléments de travail** > **qui luis sont assignés**. Cela montre que données de l’élément de travail ont été migrées, avec son identité.

    ![Production](./media/contoso-migration-tfs-vsts/full7.png)

9. Il vérifie les autres données d’élément de travail pour confirmer.

    ![Production](./media/contoso-migration-tfs-vsts/full8.png)

15. Il vérifie également d’autres projets et codes pour confirmer que le code source et l’historique ont été migrés.

    ![Production](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Migrer le contrôle de code source de TFVC vers GIT

Une fois la migration terminée, Contoso souhaite migrer de TFVC vers Git pour gérer le code source. Ils doivent importer le code source actuellement dans leur compte VSTS en tant que référentiel Git dans le même compte.

1. Dans le portail VSTS, ils ouvrent l’un des dépôts TFVC (**$/PolicyConnect**) et le révisent.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Ils cliquent sur la liste déroulante **Source** > **Importer**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. Dans **Type de source** ils sélectionnent **TFVC**, puis spécifient le chemin d’accès du dépôt. Ils ont décidé de ne pas migrer l’historique.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > En raison des différences dans la façon dont TFVC et Git stockent les informations de gestion de version, nous vous recommandons de ne pas migrer votre historique. Il s’agit de l’approche que Microsoft a adoptée lors de la migration de Windows et d’autres produits à partir de la gestion de version centralisée vers Git.

4. Après l’importation, Contoso examine le code.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. Ils répètent le processus pour le deuxième dépôt (**$/ SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Après examen de la source, les responsables du développement confirment que la migration vers VSTS a été effectuée. VSTS devient désormais la source pour tout développement au sein des équipes impliquées dans la migration.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**Besoin de plus d’aide ?**

[Apprenez-en davantage](https://docs.microsoft.com/vsts/git/import-from-tfvc?view=vsts) sur l’importation à partir de TFVC.

##  <a name="clean-up-after-migration"></a>Nettoyer après la migration

Une fois la migration terminée, Contoso a besoin d’effectuer les opérations suivantes :

- Examiner l’article [après importation](https://docs.microsoft.com/vsts/articles/migration-post-import?view=vsts) pour plus d’informations sur les activités d’importation supplémentaires.
- Supprimer les dépôts TFVC, ou les mettre en mode lecture seule. Les bases de code ne doivent pas utilisées, mais peut être référencées pour leur historique.

## <a name="next-steps"></a>Étapes suivantes

Contoso va devoir fournir une formation à VSTS et à Git aux membres concernés de l’équipe.



