---
title: 'Découvrir les serveurs fonctionnant dans un environnement VMware à l’aide d’Azure Migrate : Server Assessment'
description: Apprenez à découvrir les machines virtuelles VMware locales avec l’outil Azure Migrate Server Assessment
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 4d2b0fbb377beacdb75a1a5552855936bee2b205
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041309"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-server-assessment"></a>Tutoriel : Découvrir les serveurs fonctionnant dans un environnement VMware à l’aide d’Azure Migrate : Server Assessment

Dans le cadre de votre migration vers Azure, vous devez découvrir vos charges de travail locales et réaliser votre inventaire.

Ce tutoriel vous montre comment découvrir des serveurs fonctionnant dans un environnement VMware avec l’outil Azure Migrate : Server Assessment, à l’aide d’une appliance Azure Migrate légère. Vous déployez l’appliance en tant que serveur s’exécutant dans vCenter Server afin de découvrir en permanence les serveurs et leurs métadonnées de performances, les applications qui s’exécutent sur les serveurs, les dépendances de serveur et les instances et bases de données SQL.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer un compte Azure
> * Préparer l’environnement VMware pour la découverte
> * Créer un projet Azure Migrate.
> * Configurer l’appliance Azure Migrate
> * Démarrer la découverte continue

> [!NOTE]
> Les tutoriels indiquent le moyen le plus rapide de tester un scénario et utilisent les options par défaut lorsque c’est possible.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, vérifiez les prérequis.


**Prérequis** | **Détails**
--- | ---
**vCenter Server/Hôte ESXi** | Vous avez besoin d’une instance vCenter Server exécutant la version 5.5, 6.0, 6.5 ou 6.7.<br/><br/> Les serveurs doivent être hébergés sur un hôte ESXi exécutant la version 5.5 ou une version ultérieure.<br/><br/> Dans vCenter Server, autorisez les connexions entrantes sur le port TCP 443 pour que l’appliance puisse collecter les métadonnées de configuration et de performances.<br/><br/> L’appliance se connecte à vCenter Server sur le port 443 par défaut. Si vCenter Server écoute un autre port, vous pouvez modifier le port lorsque vous fournissez à vCenter Server des détails sur le gestionnaire de configuration de l’appliance.<br/><br/> Sur les hôtes ESXi, assurez-vous que l’accès entrant est autorisé sur le port TCP 443 pour permettre la détection des applications installées et l’analyse des dépendances sans agent sur les serveurs.
**Appliance** | vCenter Server doit allouer des ressources à un serveur pour l’appliance Azure Migrate :<br/><br/> - 32 Go de RAM, 8 processeurs virtuels et environ 80 Go de stockage sur disque.<br/><br/> - Un commutateur virtuel externe et un accès à Internet sur le serveur de l’appliance, directement ou via un proxy.
**Serveurs** | Toutes les versions de système d’exploitation Windows et Linux sont prises en charge pour la détection des métadonnées de configuration et de performances. <br/><br/> Pour effectuer la détection des applications sur les serveurs, toutes les versions de système d’exploitation Windows et Linux sont prises en charge. Cliquez [ici](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) pour obtenir les versions de système d’exploitation prises en charge pour l’analyse de dépendance sans agent.<br/><br/> Pour effectuer la détection des applications installées et l’analyse des dépendances sans agent, VMware Tools (version ultérieure à 10.2.0) doit être installé et opérationnel sur les serveurs. PowerShell version 2.0 ou ultérieure doit être installé sur les serveurs Windows.<br/><br/> Pour découvrir les instances et bases de données SQL, consultez [ici](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) les versions et les éditions de SQL Server prises en charge, les versions du systèmes d’exploitation Windows prises en charge et les mécanismes d’authentification.

> [!Note]
> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

## <a name="prepare-an-azure-user-account"></a>Préparer un compte de stockage Azure

Pour créer un projet Azure Migrate et inscrire l’appliance Azure Migrate, vous avez besoin d’un compte avec :
- Des autorisations de niveau Contributeur ou Propriétaire sur l’abonnement Azure
- Des autorisations permettant d’inscrire des applications Azure Active Directory (AAD)
- Des autorisations de niveau Propriétaire ou Contributeur plus Administrateur de l’accès utilisateur sur l’abonnement Azure pour créer un coffre de clés, utilisé pendant la migration du serveur sans agent

Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement. Si vous n’êtes pas le propriétaire de l’abonnement, demandez à celui-ci de vous attribuer les autorisations de la façon suivante :

1. Dans le portail Azure, recherchez « abonnements », puis sous **Services**, sélectionnez **Abonnements**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Zone de recherche pour rechercher l’abonnement Azure":::


2. Dans la page **Abonnements**, sélectionnez l’abonnement dans lequel vous souhaitez créer un projet Azure Migrate.
3. Dans l’abonnement, sélectionnez **Contrôle d’accès (IAM)**  > **Vérifier l’accès**.
4. Dans **Vérifier l’accès**, recherchez le compte d’utilisateur correspondant.
5. Dans **Ajouter une attribution de rôle**, cliquez sur **Ajouter**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Rechercher un compte d’utilisateur pour vérifier l’accès et attribuer un rôle":::
    
6. Dans **Ajouter une attribution de rôle**, sélectionnez le rôle Contributeur ou Propriétaire, puis sélectionnez le compte (azmigrateuser dans notre exemple). Ensuite, cliquez sur **Enregistrer**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Ouvre la page Ajouter une attribution de rôle pour attribuer un rôle au compte.":::

1. Pour inscrire l’appliance, votre compte Azure doit disposer d’**autorisations pour inscrire des applications AAD**.
1. Dans le portail Azure, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
1. Dans **Paramètres utilisateur**, vérifiez que les utilisateurs Azure AD peuvent inscrire des applications (défini sur **Oui** par défaut).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Vérifier dans les paramètres utilisateur que les utilisateurs peuvent inscrire des applications Active Directory":::

9. Si les paramètres « Inscriptions d’applications » ont la valeur « Non », demandez au locataire ou à l’administrateur général d’affecter l’autorisation nécessaire. L’administrateur général/le locataire peuvent également attribuer le rôle **Développeur d’applications** à un compte pour permettre l’inscription d’une application AAD. [Plus d’informations](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-vmware"></a>Préparer VMware

Sur vCenter Server, vérifiez que votre compte dispose des autorisations pour créer une machine virtuelle au moyen d’un fichier OVA. Cela est nécessaire quand vous déployez l’appliance Azure Migrate en tant que machine virtuelle VMware à l’aide d’un fichier OVA.

Azure Migrate a besoin d’un compte vCenter Server en lecture seule pour découvrir et évaluer les serveurs fonctionnant dans votre environnement VMware. Si vous souhaitez également procéder à la détection d’applications installées et à l’analyse des dépendances sans agent, le compte doit disposer de privilèges activés pour **Machines virtuelles > Opérations d’invité**.

### <a name="create-an-account-to-access-vcenter"></a>Créer un compte pour accéder à vCenter

Dans le client web vSphere, configurez un compte de la façon suivante :

1. À l’aide d’un compte disposant de privilèges Administrateur, dans le client web vSphere, sélectionnez **Administration**.
2. Sous **Access** (Accès), sélectionnez **Single Sign-On - Users and Groups** (Authentification unique - Utilisateurs et groupes).
3. Dans **Users** (Utilisateurs), ajoutez un nouvel utilisateur.
4. Dans **New User** (Nouvel utilisateur), tapez les informations du compte. Cliquez ensuite sur **OK**.
5. Dans **Global Permissions** (Autorisations globales), sélectionnez le compte d’utilisateur, puis attribuez le rôle **Read-only** (Lecture seule) au compte. Cliquez ensuite sur **OK**.
6.  Si vous souhaitez également procéder à la détection d’applications installées et à l’analyse des dépendances sans agent, accédez à **Rôles** > sélectionnez le rôle **En lecture seule** et, dans **Privilèges**, sélectionnez **Opérations d’invité**. Vous pouvez propager les privilèges à tous les objets sous vCenter Server en cochant la case « Propager aux enfants ».

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Case à cocher permettant d’autoriser les opérations invité pour le rôle Lecture seule":::


> [!NOTE]
> Vous pouvez définir l’étendue du compte vCenter Server pour limiter la détection à des centres de donnés vCenter Server, des clusters, un dossier de clusters, des hôtes, un dossier d’hôtes ou des serveurs individuels spécifiques. [**En savoir plus**](set-discovery-scope.md) sur la définition de l’étendue du compte d’utilisateur vCenter Server.


### <a name="create-an-account-to-access-servers"></a>Créer un compte pour accéder aux serveurs

Vous avez besoin d’un compte d’utilisateur doté des privilèges nécessaires sur les serveurs pour effectuer la détection des applications installées, l’analyse des dépendances sans agent et la détection des instances et bases de données SQL. Vous pouvez fournir le compte d’utilisateur sur le gestionnaire de configuration de l’appliance. L’appliance n’installe pas d’agents sur les serveurs.

1. Pour les serveurs Windows, créez un compte (local ou de domaine) avec des autorisations d’administration sur les serveurs. Pour découvrir les instances et les bases de données SQL, il faut que le compte Windows ou SQL Server soit membre du rôle serveur sysadmin. [En savoir plus](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) sur l’attribution du rôle requis au compte d’utilisateur.
2. Pour les serveurs Linux, créez un compte avec des privilèges racine. Vous pouvez également créer un compte disposant de ces autorisations sur les fichiers /bin/netstat et /bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE.

> [!NOTE]
> Vous pouvez maintenant ajouter plusieurs informations d’identification de serveur sur le gestionnaire de configuration pour effectuer la détection des applications installées, l’analyse des dépendances sans agent et la détection des instances et bases de données SQL. Vous pouvez ajouter plusieurs informations d’identification pour l’authentification de domaine, Windows (hors domaine), Linux (hors domaine) et/ou SQL Server. [**En savoir plus**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Configuration d’un projet

Configurez un nouveau projet Azure Migrate.

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sélectionnez **Créer un projet**.
5. Dans **Créer un projet**, sélectionnez votre abonnement Azure et votre groupe de ressources. Créez un groupe de ressources si vous n’en avez pas.
6. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer. Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et du [secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Zones pour le nom et la région du projet":::

7. Sélectionnez **Create** (Créer).
8. Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. L’outil **Azure Migrate : Server Assessment** est ajouté par défaut au nouveau projet.

> [!NOTE]
> Si vous avez déjà créé un projet, vous pouvez utiliser le même projet pour inscrire des appliances supplémentaires afin de découvrir et d’évaluer un plus grand nombre de serveurs. [**En savoir plus**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurer l’appliance

Azure Migrate : Server Assessment utilise une appliance Azure Migrate légère. L’appliance effectue la détection du serveur, puis envoie la configuration et les métadonnées de performances du serveur à Azure Migrate. L’appliance peut être configurée en déployant un modèle OVA pouvant être téléchargé à partir du projet.

> [!NOTE]
> Si, pour une raison quelconque, vous ne pouvez pas configurer l’appliance à l’aide du modèle, vous pouvez la configurer à l’aide d’un script PowerShell sur un serveur Windows Server 2016 existant. [**En savoir plus**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Effectuer un déploiement avec OVA

Pour configurer l’appliance avec un modèle OVA, vous :
1. Fournissez un nom d'appliance et générez une clé de projet Azure Migrate sur le portail.
1. Téléchargez un fichier de modèle OVA, puis importez-le dans vCenter Server. Vérifiez que l’OVA est sécurisé.
1. Créez la machine virtuelle appliance à partir du modèle OVA et vérifiez qu’elle peut se connecter à Azure Migrate.
1. Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet en utilisant la clé de projet Azure Migrate.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Générer la clé de projet Azure Migrate

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, sélectionnez **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec l’hyperviseur vSphere VMware**.
3. Dans **1 : Générer une clé de projet Azure Migrate**, attribuez un nom à l’appliance Azure Migrate que vous allez configurer pour la découverte de serveurs dans votre environnement VMware. Le nom doit être alphanumérique et compter au maximum de 14 caractères.
1. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure nécessaires. Ne fermez pas la page Découvrir pendant la création des ressources.
1. Une fois les ressources Azure créées, une **clé de projet Azure Migrate** est générée.
1. Copiez la clé car vous en aurez besoin pour terminer l'inscription de l'appliance lors de sa configuration.

### <a name="2-download-the-ova-template"></a>2. Télécharger le modèle OVA

Dans **2 : Télécharger l’appliance Azure Migrate**, sélectionnez le fichier .OVA et cliquez sur **Télécharger**.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier OVA est sécurisé avant de le déployer :

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Vérifiez les toutes dernières versions de l’appliance et les valeurs de hachage :

    - Pour le cloud public Azure :
    
        **Algorithme** | **Télécharger** | **SHA256**
        --- | --- | ---
        VMware (11,9 Go) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Pour Azure Government :
    
        **Algorithme** | **Télécharger** | **SHA256**
        --- | --- | ---
        VMware (85,8 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. Créer le serveur de l’appliance

Importez le fichier téléchargé, puis créez un serveur dans l’environnement VMware.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).
2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .OVA.
3. Dans **Nom** et **Emplacement**, spécifiez un nom convivial pour le serveur. Sélectionnez l’objet d’inventaire dans lequel le serveur doit être hébergée.
5. Dans **Hôte/Cluster**, spécifiez l’hôte ou le cluster sur lequel s’exécute le serveur.
6. Dans **Stockage**, spécifiez la destination du stockage du serveur.
7. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
8. Dans **Mappage réseau**, spécifiez le réseau auquel se connectera. Le réseau nécessite une connexion à Internet pour envoyer des métadonnées à Azure Migrate.
9. Passez en revue les paramètres, confirmez-les, puis cliquez sur **Finish** (Terminer).


### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que le serveur de l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et ceux du [secteur public](migrate-appliance.md#government-cloud-urls).


### <a name="4-configure-the-appliance"></a>4. Configurer l’appliance

Configurez l’appliance pour la première fois.

> [!NOTE]
> Si vous configurez l’appliance à l’aide d’un [**script PowerShell**](deploy-appliance-script.md) au lieu du modèle OVA téléchargé, les deux premières étapes de cette procédure ne s’appliquent pas.

1. Dans la console du client vSphere, cliquez avec le bouton droit sur le serveur, puis sélectionnez **Ouvrir la console**.
2. Spécifiez la langue, le fuseau horaire et le mot de passe pour l’appliance.
3. Ouvrez un navigateur sur une machine pouvant se connecter au serveur de l’appliance, puis ouvrez l’URL du gestionnaire de configuration de l’appliance : `https://appliance name or IP address: 44368`.

   Vous pouvez également ouvrir le gestionnaire de configuration à partir du bureau du serveur de l’appliance en sélectionnant le raccourci du gestionnaire de configuration.
1. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
1. Dans le gestionnaire de configuration > **Configurer les prérequis**, procédez comme suit :
   - **Connectivité** : l’appliance vérifie que le serveur a accès à Internet. Si le serveur utilise un proxy :
     - Cliquez sur **Configurer le proxy** pour spécifier l’adresse du proxy `http://ProxyIPAddress` ou `http://ProxyFQDN`, ainsi que le port d’écoute.
     - Spécifiez les informations d’identification si le proxy nécessite une authentification.
     - Seuls les proxys HTTP sont pris en charge.
     - Si vous avez ajouté les détails du proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
   - **Synchronisation de l’heure** : L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
   - **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées. Une fois la vérification terminée, vous pouvez cliquer sur **Afficher les services de l’appliance** pour voir l’état et les versions des services s’exécutant sur le serveur d’appliance.
   - **Installer VDDK** : L’appliance vérifie que VDDK (VMware vSphere Virtual Disk Development Kit) est installé. S’il n’est pas installé, téléchargez VDDK 6.7 à partir de VMware, puis extrayez le contenu du fichier zip téléchargé sur l’appliance à l’emplacement indiqué dans les **instructions d’installation**.

     L’outil de migration de serveur Azure Migrate utilise VDDK pour répliquer les serveurs durant la migration vers Azure. 
1. Si vous le souhaitez, vous pouvez **réexécuter les prérequis** à tout moment pendant la configuration de l’appliance pour vérifier si celle-ci remplit les remplit tous.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panneau 1 sur le gestionnaire de configuration de l’appliance":::


### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la **clé de projet Azure Migrate** copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Évaluation de serveur > Découvrir > Gérer les appliances existantes**, sélectionnez le nom d’appliance que vous avez indiqué au moment de générer la clé, puis copiez la clé correspondante.
1. Vous aurez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. Le fait de cliquer sur **Connexion** ouvre une boîte de dialogue modale comprenant le code de l’appareil, comme celle affichée ci-dessous.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Boîte de dialogue modale indiquant le code de l’appareil":::

1. Cliquez sur **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
1. Sous le nouvel onglet, collez le code de l’appareil, puis connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure.
   
   La connexion avec un code PIN n’est pas prise en charge.
3. Si vous avez fermé accidentellement l’onglet Connexion avant de vous être connecté, vous devrez actualiser l’onglet Appliance Configuration Manager pour réactiver le bouton de connexion.
1. Une fois connecté, revenez à l’onglet précédent, c’est-à-dire, l’onglet Appliance Configuration Manager.
1. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des autorisations adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
1. Une fois l'appliance inscrite, vous pouvez consulter les détails de l'inscription en cliquant sur **Afficher les détails**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Volet 2 sur le gestionnaire de configuration d’appliance":::

## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

### <a name="provide-vcenter-server-details"></a>Fournir les détails de vCenter Server

L’appliance doit se connecter à vCenter Server pour découvrir les données de configuration et de performances des serveurs.

1. À l’**Étape 1 : Fournir les informations d’identification de vCenter Server**, cliquez sur **Ajouter des informations d’identification** pour attribuer un nom convivial aux informations d’identification, puis ajoutez un **nom d’utilisateur** et un **mot de passe** pour le compte vCenter Server dont l’appliance se servira pour découvrir les serveurs s’exécutant sur le vCenter Server.
    - Vous devriez avoir configuré un compte avec les autorisations requises, comme décrit dans cet article ci-dessus.
    - Si vous souhaitez étendre la découverte à des objets VMware spécifiques (centres de données vCenter Server, clusters, dossier de clusters, hôtes, dossier d’hôtes ou machines virtuelles individuelles), passez en revue les instructions contenues dans [cet article](set-discovery-scope.md) pour restreindre le compte utilisé par Azure Migrate.
1. À l’**Étape 2 : Fournir les détails de vCenter Server**, cliquez sur **Ajouter une source de découverte** pour sélectionner le nom convivial des informations d’identification dans la liste déroulante, puis spécifiez l’**adresse IP/nom de domaine complet** du vCenter Server. Vous pouvez laisser le **port** par défaut (443) ou spécifier un port personnalisé sur lequel vCenter Server est à l’écoute, puis cliquez sur **Enregistrer**.
1. Dès que vous cliquez sur **Enregistrer**, l’appliance tente de valider la connexion au vCenter Server avec les informations d’identification indiquées, et affiche l’**état de validation** dans le tableau pour l’adresse IP/le nom de domaine complet de vCenter Server.
1. Vous pouvez **revalider** la connectivité à vCenter Server à tout moment avant de lancer la découverte.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panneau 3 sur le gestionnaire de configuration de l’appliance pour le détails de vCenter Server":::

### <a name="provide-server-credentials"></a>Fournir les informations d’identification du serveur

À l’**Étape 3 : Fournir les informations d’identification de serveur pour effectuer l’inventaire logiciel, l’analyse des dépendances sans agent et la découverte des instances et bases de données SQL Server**, vous pouvez choisir de fournir plusieurs informations d’identification de serveur ou, si vous ne souhaitez pas tirer parti de ces fonctionnalités, vous pouvez ignorer l’étape et procéder à la découverte du vCenter Server. Vous pouvez revenir sur votre décision à tout moment.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panneau 3 sur le gestionnaire de configuration de l’appliance pour les détails du serveur":::

> [!Note]
> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

Si vous souhaitez tirer parti de ces fonctionnalités, vous pouvez fournir les informations d’identification du serveur en suivant les étapes ci-dessous. L’appliance tentera de mapper automatiquement les informations d’identification aux serveurs pour exécuter les fonctionnalités de découverte.

- Vous pouvez ajouter les informations d’identification du serveur en cliquant sur le bouton **Ajouter des informations d’identification**. Cela a pour effet d’ouvrir une fenêtre modale dans laquelle vous pouvez choisir le **type d’informations d’identification** dans une liste déroulante.
- Vous pouvez fournir des informations d’identification de domaine/Windows (hors domaine)/Linux (hors domaine)/SQL Server. [Apprenez-en davantage](add-server-credentials.md) sur la manière de fournir des informations d’identification et la façon dont nous les traitons.
- Pour chaque type d’informations d’identification, vous devez spécifier un nom convivial, ajouter un **Nom d’utilisateur** et un **Mot de passe**, puis cliquer sur **Enregistrer**.
- Si vous choisissez des informations d’identification de domaine, vous devez également spécifier le nom de domaine complet (FQDN) du domaine. Celui-ci est requis pour valider l’authenticité des informations d’identification auprès de l’Active Directory de ce domaine.
- Examinez les [autorisations requises](add-server-credentials.md#required-permissions) sur le compte pour la découverte des applications installées, l’analyse des dépendances sans agent ou la découverte des instances et bases de données SQL Server.
- Si vous souhaitez ajouter plusieurs informations d’identification à la fois, cliquez sur **Ajouter** pour enregistrer et ajouter d’autres informations d'identification.
- Lorsque vous cliquez sur **Enregistrer** ou **Ajouter**, l’appliance valide les informations d’identification de domaine auprès de l’Active Directory du domaine pour vérifier leur authenticité. Cela permet d’éviter les verrouillages de compte lorsque l’appliance effectue plusieurs itérations pour mapper les informations d’identification aux serveurs concernés.
- Vous pouvez voir l’**État de validation** de toutes les informations d’identification de domaine dans la table des informations d’identification. Seules les informations d’identification de domaine seront validées.
- Si la validation échoue, vous pouvez cliquer sur état **Échec** pour voir l’erreur rencontrée, puis cliquer sur **Revalider les informations d’identification** après avoir corrigé le problème afin de valider à nouveau les informations d’identification de domaine qui ont échoué.


### <a name="start-discovery"></a>Démarrer la découverte

1. Cliquez sur **Démarrer la découverte** pour lancer la découverte du vCenter Server. Une fois la découverte lancée, vous pouvez vérifier son état par rapport à l’adresse IP ou au le nom de domaine complet de vCenter Server dans la table des sources.
1. Si vous avez fourni des informations d’identification de serveur, l’inventaire logiciel (découverte des applications installées) est automatiquement lancé une fois la découverte de vCenter Server terminée. L’inventaire logiciel est effectué une fois toutes les 12 heures.
1. L’ [inventaire logiciel](how-to-discover-applications.md) identifie les instances SQL Server s’exécutant sur les serveurs et utilisant les informations. L’appliance tente de se connecter aux instances via l’authentification Windows ou les informations d’identification d’authentification de SQL Server fournies sur l’appliance, et de collecter des données sur les bases de données SQL Server et leurs propriétés. La découverte SQL est effectuée une fois toutes les 24 heures.
1. Au cours de l’inventaire logiciel, les informations d’identification des serveurs ajoutées sont comparées aux serveurs et validées pour l’analyse des dépendances sans agent. Vous pouvez activer l’analyse des dépendances sans agent pour les serveurs à partir du portail. Seuls les serveurs dont la validation réussit peuvent être sélectionnés pour activer l’analyse des dépendances sans agent.

> [!Note]
>Azure Migrate chiffre la communication entre l’appliance Azure Migrate et les instances SQL Server sources (avec la propriété Chiffrer la connexion définie sur TRUE). Ces connexions sont chiffrées avec [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (défini sur true). La couche transport utilise le protocole SSL pour chiffrer le canal et contourner la chaîne de certificats afin de valider l’approbation. Le serveur d’appliance doit être configuré pour [**approuver l’autorité racine du certificat**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Si aucun certificat n’a été approvisionné sur le serveur à son démarrage, SQL Server génère un certificat auto-signé qui est utilisé pour chiffrer les paquets d’ouverture de session. [**En savoir plus**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

La découverte fonctionne comme ceci :
- Environ 15 minutes s’écoulent avant que l’inventaire des serveurs découverts apparaisse dans le portail.
- La découverte des applications installées peut prendre un certain temps. Sa durée dépend du nombre de serveurs découverts. Pour 500 serveurs, environ une heure s’écoule avant que l’inventaire découvert apparaisse dans le portail Azure Migrate.
- Une fois la découverte des serveurs terminée, vous pouvez activer l’analyse des dépendances sans agent sur les serveurs à partir du portail.
- Les données d’instances et de bases de données SQL Server commenceront à apparaître dans le portail dans les 24 heures après le lancement de la découverte.

## <a name="next-steps"></a>Étapes suivantes

- [Évaluer des serveurs](./tutorial-assess-vmware-azure-vm.md) en vue de leur migration vers des machines virtuelles Azure.
- [Évaluer des serveurs SQL Server](./tutorial-assess-sql.md) en vue de leur migration vers Azure SQL.
- [Passer en revue les données](migrate-appliance.md#collected-data---vmware) que l’appliance collecte pendant la découverte