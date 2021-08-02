---
title: Utilisation d’Azure Migrate avec des points de terminaison privés
description: Utilisez le support de liaison privée Azure Migrate pour découvrir, évaluer et migrer à l’aide d’une liaison privée.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: 85202474e8fd3c4340357b99ae91ee7d25d79ebf
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747182"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Utilisation d’Azure Migrate avec des points de terminaison privés  

Cet article décrit l’utilisation d’Azure Migrate pour découvrir, évaluer et migrer des serveurs sur un réseau privé à l’aide d’[Azure Private Link](../private-link/private-endpoint-overview.md).

Vous pouvez utiliser les outils [Azure Migrate : découverte et évaluation](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) et [Azure Migrate : migration de serveur](./migrate-services-overview.md#azure-migrate-server-migration-tool) pour vous connecter de manière privée et sécurisée au service Azure Migrate via un Peering privé ExpressRoute ou une connexion VPN site à site à l’aide d’Azure Private Link.

La méthode de connectivité du point de terminaison privé est recommandée lorsqu’il existe une exigence organisationnelle pour accéder au service Azure Migrate et à d’autres ressources Azure sans traverser les réseaux publics. Grâce à Azure Private Link, vous pouvez utiliser vos circuits de Peering privé ExpressRoute existants pour répondre à vos besoins en matière de bande passante ou de latence.

## <a name="support-requirements"></a>Conditions de prise en charge requises

### <a name="required-permissions"></a>Autorisations requises

**Contributeur + Administrateur de l’accès utilisateur** ou autorisations **Propriétaire** sur l’abonnement.

### <a name="supported-scenarios-and-tools"></a>Scénarios et outils pris en charge

**Déploiement** | **Détails** | **outils**
--- | --- | ---
**Découverte et évaluation** | Effectuez une découverte et une évaluation sans agent à grande échelle de vos serveurs exécutés sur n’importe quelle plateforme : les plateformes hyperviseur, telles que [VMware vSphere](./tutorial-discover-vmware.md) ou [Microsoft Hyper-V](./tutorial-discover-hyper-v.md), ainsi que les clouds publics, tels que [AWS](./tutorial-discover-aws.md) ou [GCP](./tutorial-discover-gcp.md), ou encore des [serveurs matériel nu](./tutorial-discover-physical.md). | Azure Migrate : découverte et évaluation  <br/>
**Inventaire logiciel** | Découvrez des applications, des rôles et des fonctionnalités qui s’exécutent sur des machines virtuelles VMware. | Azure Migrate : découverte et évaluation  
**Visualisation des dépendances** | Utilisez la fonctionnalité d’analyse de dépendance pour identifier et comprendre les dépendances entre les serveurs. <br/> La [visualisation de dépendance sans agent](./how-to-create-group-machine-dependencies-agentless.md) est prise en charge en mode natif avec le support de liaison privée Azure Migrate. <br/>La [visualisation de dépendance basée sur un agent](./how-to-create-group-machine-dependencies.md) nécessite une connectivité Internet. [Découvrez comment](../azure-monitor/logs/private-link-security.md) utiliser des points de terminaison privés pour la visualisation de dépendance basée sur un agent. | Azure Migrate : découverte et évaluation |
**Migration** | Effectuez [des migrations Hyper-v sans agent](./tutorial-migrate-hyper-v.md) ou utilisez l’approche basée sur un agent pour migrer vos [machines virtuelles VMware](./tutorial-migrate-vmware-agent.md), [machines virtuelles Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [serveurs physiques](./tutorial-migrate-physical-virtual-machines.md), [machines virtuelles exécutées sur AWS](./tutorial-migrate-aws-virtual-machines.md), [machines virtuelles exécutées sur GCP](./tutorial-migrate-gcp-virtual-machines.md)ou machines virtuelles s’exécutant sur un autre fournisseur de virtualisation. | Azure Migrate : Server Migration

>[!Note]
>
> Les [migrations VMware sans agent](./tutorial-migrate-vmware.md) requièrent un accès ou une connectivité Internet via le peering Microsoft ExpressRoute. <br/> [Découvrez comment](./replicate-using-expressroute.md) utiliser des points de terminaison privés pour effectuer des réplications via un peering privé ExpressRoute ou une connexion VPN de site à site (S2S).  <br/><br/>

#### <a name="other-integrated-tools"></a>Autres outils intégrés

D’autres outils de migration sont susceptibles de ne pas pouvoir charger de données d’utilisation dans le projet Azure Migrate si l’accès au réseau public est désactivé. Le projet Azure Migrate doit être configuré de façon à autoriser le trafic de tous les réseaux à recevoir des données provenant d’autres offres d’ [éditeurs de logiciels indépendants (ISV)](./migrate-services-overview.md#isv-integration) Microsoft ou externes.


Pour activer l’accès au réseau public pour le projet Azure Migrate,connectez-vous au portail Azure, accédez à la page des **propriétés d’Azure Migrate** sur le portail Azure, sélectionnez **Non** > **Enregistrer**.

![Diagramme montrant la procédure de modification d’un mode d’accès au réseau.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Autres considérations   

**Considérations** | **Détails**
--- | ---
**Tarification** | Pour plus d’informations sur la tarification, consultez [Tarification des blobs Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) et [Tarification d’Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).  
**Conditions requises pour le réseau virtuel** | Le point de terminaison de la passerelle ExpressRoute/VPN doit résider dans le réseau virtuel sélectionné ou dans un réseau virtuel connecté à celui-ci. Vous pourriez avoir besoin d’environ 15 adresses IP dans le réseau virtuel.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Créer un projet avec une connectivité de point de terminaison privé

Utilisez cet [article](./create-manage-projects.md#create-a-project-for-the-first-time) pour configurer un nouveau projet Azure Migrate.

> [!Note]
> Vous ne pouvez pas modifier la méthode de connectivité de point de terminaison privé pour les projets Azure Migrate existants.

Dans la section de configuration **avancée**, indiquez les détails ci-dessous pour créer un point de terminaison privé pour votre projet Azure Migrate.
1. Dans **Méthode de connectivité**, choisissez **Point de terminaison privé**.
2. Dans **Désactiver l’accès au point de terminaison public**, conservez le paramètre par défaut **Non**. Certains outils de migration sont susceptibles de ne pas pouvoir charger de données d’utilisation dans le projet Azure Migrate si l’accès au réseau public est désactivé. [En savoir plus.](#other-integrated-tools)
3. Dans **Abonnement au réseau virtuel**, sélectionnez l’abonnement pour le réseau virtuel de point de terminaison privé.
4. Dans **Réseau virtuel**, sélectionnez le réseau virtuel pour le point de terminaison privé. L’appliance Azure Migrate et les autres composants logiciels qui doivent se connecter au projet Azure Migrate doivent se trouver sur ce réseau ou sur un réseau virtuel connecté.
5. Dans **Sous-réseau**, sélectionnez le sous-réseau pour le point de terminaison privé.

   ![Créer un projet](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

6. Sélectionnez **Create** (Créer). pour créer un projet de migration et y attacher un point de terminaison privé. Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. Ne fermez pas cette page lorsque la création du projet est en cours.

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Découvrir et évaluer les serveurs à des fins de migration à l’aide d’Azure Private Link

### <a name="set-up-the-azure-migrate-appliance"></a>Configurer l’appliance Azure Migrate

1. Dans **Détecter des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez le type de serveur.
2. Dans **Générer une clé de projet Azure Migrate**, fournissez un nom pour l’appliance Azure Migrate.
3. Sélectionnez **Générer une clé** pour créer les ressources Azure requises.

    > [!Important]
    > Ne fermez pas la page Découvrir des machines pendant la création de ressources.  
    - À cette étape, Azure Migrate crée un coffre de clés, un compte de stockage, un coffre Recovery Services (uniquement pour les migrations VMware sans agent), ainsi que quelques ressources internes, et joint un point de terminaison privé à chaque ressource. Les points de terminaison privés sont créés dans le réseau virtuel sélectionné lors de la création du projet.  
    - Une fois les points de terminaison privés créés, les enregistrements de ressource CNAME DNS pour les ressources Azure Migrate sont mis à jour vers un alias dans un sous-domaine avec le préfixe *privatelink*. Par défaut, Azure Migrate crée également une zone DNS privée correspondant au sous-domaine *privatelink* pour chaque type de ressource et insère des enregistrements A DNS pour les points de terminaison privés associés. Cela permet à l’appliance Azure Migrate et à d’autres composants logiciels résidant sur le réseau source d’atteindre les points de terminaison de ressource Azure Migrate sur des adresses IP privées.  
    - Azure Migrate active également une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour le projet migrate et accorde des autorisations à l’identité managée pour accéder en toute sécurité au compte de stockage.  

4. Une fois la clé correctement générée, copiez les détails de la clé pour configurer et inscrire l’appliance.   

#### <a name="download-the-appliance-installer-file"></a>Télécharger le fichier du programme d’installation de l’appliance  

L’outil Azure Migrate : découverte et évaluation utilise une appliance Azure Migrate légère. L’appliance effectue la détection du serveur, puis envoie la configuration et les métadonnées de performances du serveur à Azure Migrate.  

> [!Note]
> L’option de déploiement d’un appareil à l’aide d’un modèle (OVA pour les serveurs dans un environnement VMware et un environnement VHD Hyper-V) n’est pas prise en charge pour les projets Azure Migrate avec une connectivité de point de terminaison privé.

Pour configurer l’appliance :
  1. Téléchargez le fichier ZIP contenant le script du programme d’installation à partir du portail.
  2. Copiez le fichier zip sur le serveur qui hébergera l’appliance.   
  3. Après avoir téléchargé le fichier ZIP, vérifiez la sécurité du fichier.
  4. Exécutez le script du programme d’installation pour déployer l’appliance.

Voici les liens de téléchargement pour chacun des scénarios :

Scénario | Télécharger le lien | Valeur de hachage
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | CBF8927AF137A106E2A34AC4F77CFFCB1CD96873C592E1DF37BC5606254989EC
Physique | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 1CB967D92096EB48E4C3C809097F52C8341FC7CA7607CF840C529E7A21B1A21D
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 0A4FCC4D1500442C5EB35E4095EF781CB17E8ECFE8E4F8C859E65231E00BB154
Scale-out VMware | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 2F035D34E982EE507EAEC59148FDA8327A45D2A845B4A95475EC6D2469D72D28

#### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation du cloud public : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  Vérifiez la version la plus récente de l’appliance en comparant les valeurs de hachage du tableau ci-dessus.

Assurez-vous que le serveur répond aux [exigences matérielles](./migrate-appliance.md) pour le scénario choisi (VMware/Hyper-V/physique ou autre) et peut se connecter aux [URL requises](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity).


#### <a name="run-the-script"></a>Exécuter le script

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.
2. Lancez PowerShell sur la machine avec un privilège administrateur (élevé).
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :

    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1
    ```

5. Une fois le script correctement exécuté, il lance le gestionnaire de configuration de l’appliance afin que vous puissiez configurer l’appliance. En cas de problèmes, consultez les journaux du script sous C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Configurer l’appliance et démarrer la détection en continu

Ouvrez un navigateur sur une machine pouvant se connecter au serveur de l’appliance, puis ouvrez l’URL du gestionnaire de configuration de l’appliance : `https://appliance name or IP address: 44368`. Vous pouvez également ouvrir le gestionnaire de configuration à partir du bureau du serveur de l’appliance en sélectionnant le raccourci du gestionnaire de configuration.

#### <a name="set-up-prerequisites"></a>Configurer les prérequis

1. Lisez les informations tierces et acceptez les **termes du contrat de licence**.    

2. Dans le gestionnaire de configuration > **Configurer les prérequis**, procédez comme suit :
   - **Connectivité** : l’appliance vérifie l’accès aux URL requises. Si le serveur utilise un proxy :
     - Cliquez sur **Configurer le proxy** pour spécifier l’adresse du proxy `http://ProxyIPAddress` ou `http://ProxyFQDN`, ainsi que le port d’écoute.
     - Spécifiez les informations d’identification si le proxy nécessite une authentification. Seuls les proxys HTTP sont pris en charge.
     - Vous pouvez ajouter une liste d’URL/adresses IP qui doivent contourner le serveur proxy.
     - Cliquez sur **Enregistrer** pour inscrire la configuration si vous avez mis à jour les détails du serveur proxy ou avez ajouté des URL/adresses IP pour contourner le proxy.

        > [!Note]
        > Si vous obtenez une erreur avec le lien aka.ms/* lors de la vérification de la connectivité et que vous ne souhaitez pas que l’appliance accède à cette URL sur Internet, vous devez désactiver le service de mise à jour automatique sur l’appliance en suivant les étapes indiquées [**ici**](./migrate-appliance.md#turn-off-auto-update). Une fois la mise à jour automatique désactivée, la vérification de la connectivité de l’URL aka.ms/* est ignorée.

   - **Synchronisation de l’heure** : L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
   - **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées. Une fois la vérification terminée, cliquez sur **Voir les services d’appliance** pour voir l’état et les versions des services qui s’exécutent sur le serveur de l’appliance.
        > [!Note]
        > Si vous avez choisi de désactiver le service de mise à jour automatique sur l’appliance, vous pouvez mettre à jour les services de l’appliance manuellement pour obtenir les dernières versions des services en procédant comme décrit [**ici**](./migrate-appliance.md#manually-update-an-older-version).
   - **Installez VDDK** : (_uniquement nécessaire pour l’appliance VMware)_ l’appliance vérifie que le VDDK (Virtual Disk Development Kit) de VMware vSphere est installé. S’il n’est pas installé, téléchargez VDDK 6.7 à partir de VMware, puis extrayez le contenu du fichier zip téléchargé sur l’appliance à l’emplacement indiqué dans les **instructions d’installation**.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Inscrire l’appliance et démarrer la détection en continu

Une fois la vérification des prérequis terminée, procédez comme suit pour inscrire l’appliance et démarrer la détection en continu pour les scénarios correspondants :
- [Machines virtuelles VMware](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Machines virtuelles Hyper-V](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [Serveurs physiques](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [Machines virtuelles AWS](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [Machines virtuelles GCP](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)


>[!Note]
> Si vous rencontrez des problèmes de résolution DNS lors de l’inscription de l’appliance ou au moment du démarrage de la détection, assurez-vous que les ressources Azure Migrate créées à l’étape **Générer la clé** sur le portail sont accessibles à partir du serveur local qui héberge l’appliance Azure Migrate. [En savoir plus sur la vérification de la connectivité réseau](./troubleshoot-network-connectivity.md).

### <a name="assess-your-servers-for-migration-to-azure"></a>Évaluer vos serveurs en vue de leur migration vers Azure
Une fois la détection terminée, évaluez vos serveurs ([machines virtuelles VMware](./tutorial-assess-vmware-azure-vm.md), [machines virtuelles Hyper-V](./tutorial-assess-hyper-v.md), [serveurs physiques](./tutorial-assess-vmware-azure-vm.md), [machines virtuelles AWS](./tutorial-assess-aws.md), [machines virtuelles GCP](./tutorial-assess-gcp.md)) pour la migration vers des machines virtuelles Azure ou vers la solution VMware sur Azure (AVS) à l’aide de l’outil Azure Migrate : découverte et évaluation.

Vous pouvez également [évaluer vos machines locales](./tutorial-discover-import.md#prepare-the-csv) avec l’outil Azure Migrate : découverte et évaluation en utilisant un fichier CSV (valeurs séparées par des virgules) importé.   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Migrer des serveurs vers Azure à l’aide d’Azure Private Link

Les sections suivantes décrivent les étapes nécessaires à l’utilisation d’Azure Migrate avec des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour les migrations à l’aide d’un peering privé ExpressRoute ou de connexions VPN.  

Cet article montre un chemin d’accès de déploiement de preuve de concept pour les réplications basées sur un agent afin de migrer vos [machines virtuelles VMware](./tutorial-migrate-vmware-agent.md), [machines virtuelles Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [serveurs physiques](./tutorial-migrate-physical-virtual-machines.md), [machines virtuelles exécutées sur AWS](./tutorial-migrate-aws-virtual-machines.md), [machines virtuelles exécutées sur GCP](./tutorial-migrate-gcp-virtual-machines.md) ou vos machines virtuelles exécutées sur un autre fournisseur de virtualisation à l’aide de points de terminaison privés Azure. Vous pouvez utiliser une approche similaire pour effectuer des [migrations Hyper-V sans agent](./tutorial-migrate-hyper-v.md) à l’aide d’une liaison privée.

>[!Note]
>Les [migrations VMware sans agent](./tutorial-assess-physical.md) requièrent un accès ou une connectivité Internet via le peering Microsoft ExpressRoute.

### <a name="set-up-a-replication-appliance-for-migration"></a>Configurer une appliance de réplication pour la migration

Le diagramme suivant illustre le workflow de réplication basé sur un agent avec des points de terminaison privés à l’aide de l’outil Azure Migrate : migration de serveur.  

![Architecture de réplication](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

L’outil ses sert d’une appliance de réplication pour répliquer vos serveurs sur Azure. Consultez cet article pour [préparer et configurer une machine pour l’appliance de réplication](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance).

Une fois l’appliance de réplication configurée, suivez les instructions ci-dessous pour créer les ressources nécessaires à la migration.

1. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Non virtualisé/Autre**.
2. Dans **Région cible**, sélectionnez et confirmez la région Azure vers laquelle vous souhaitez migrer les machines.
3. Sélectionnez **Créer des ressources** pour créer les ressources Azure nécessaires. Ne fermez pas la page pendant la création des ressources.   
    - Cela crée un coffre Recovery Services en arrière-plan et active une identité managée pour le coffre. Un coffre Recovery Services est une entité qui contient les informations de réplication des serveurs et qui permet de déclencher des opérations de réplication.  
    - Si le projet Azure Migrate dispose d’une connectivité de point de terminaison privée, un point de terminaison privé est créé pour le coffre Recovery Services. Cela ajoute cinq noms privés complets (FQDN) au point de terminaison privé, un pour chaque microservice lié au coffre Recovery Services.   
    - Les cinq noms de domaine sont mis en forme dans ce modèle : <br/> _{Vault-ID}-asr-pod01-{type}-.{target-geo-code}_ .privatelink.siterecovery.windowsazure.com  
    - Par défaut, Azure Migrate crée automatiquement une zone DNS privée et ajoute des enregistrements A DNS pour les microservices du coffre Recovery Services. La zone DNS privée est liée au réseau virtuel de point de terminaison privé et permet à l’appliance de réplication locale de résoudre les noms de domaine complets en adresses IP privées.

4. Avant d’inscrire l’appliance de réplication, assurez-vous que les FQDN de la liaison privée du coffre sont accessibles à partir de la machine hébergeant l’appliance de réplication. [Découvrez la procédure de vérification de la connectivité réseau](./troubleshoot-network-connectivity.md).

5. Une fois la connectivité vérifiée, téléchargez le fichier de configuration et de clé de l’appliance, exécutez le processus d’installation et enregistrez l’appliance dans Azure Migrate. Passez en revue les [étapes détaillées ici](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). Une fois l’appliance de réplication configurée, suivez ces instructions pour [installer le service de mobilité](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service) sur les machines que vous souhaitez migrer.

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Répliquer des serveurs vers Azure à l’aide d’Azure Private Link

Suivez [ces étapes](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) pour sélectionner les serveurs à répliquer.  

Dans **Répliquer** > **Paramètres de la cible** > **Compte de stockage de cache/réplication**, utilisez la liste déroulante pour sélectionner un compte de stockage à répliquer sur une liaison privée.  

Si votre projet Azure Migrate dispose d’une connectivité de point de terminaison privé, vous devez [accorder des autorisations à l’identité managée du coffre Recovery Services](#grant-access-permissions-to-the-recovery-services-vault) pour accéder au compte de stockage requis par Azure Migrate.   

De plus, pour activer les réplications sur une liaison privée, [créez un point de terminaison privé pour le compte de stockage.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Accorder des autorisations d’accès au coffre Recovery Services

Vous devez accorder les autorisations au coffre Recovery Services pour permettre l’accès authentifié au compte de stockage de cache/réplication.

Pour identifier le coffre Recovery Services créé par Azure Migrate et accorder les autorisations requises, procédez comme suit :

**_Identifier le coffre Recovery Services et l’ID de l’objet d’identité managée_**

Vous trouverez les détails du coffre Recovery Services sur la page **de propriétés** d’Azure Migrate : migration de serveur.

1. Accédez au **hub Azure Migrate**, sélectionnez **Vue d’ensemble** sur la vignette Azure Migrate : migration de serveur.

    ![Page de présentation du hub Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. Dans le volet gauche, sélectionnez **Propriétés**. Notez le nom du coffre Recovery Services et l’ID de l’identité managée. Le coffre a un _Point de terminaison privé_ comme **type de connectivité** et _Autre_ comme **type de réplication**. Vous aurez besoin de ces informations pour fournir l’accès au coffre.

    ![Page de propriétés Azure Migrate : migration de serveur](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Autorisations d’accès au compte de stockage_**

 Vous devez accorder à l’identité managée du coffre les autorisations de rôle suivantes sur le compte de stockage requis pour la réplication.  Dans ce cas, vous devez créer le compte de stockage à l’avance.

>[!Note]
> Pour migrer des machines virtuelles Hyper-V vers Azure à l’aide d’une liaison privée, vous devez accorder l’accès au compte de stockage de réplication et au compte de stockage de cache.

Les autorisations de rôle pour Resource Manager varient en fonction du type de compte de stockage.

|**Type de compte de stockage** | **Autorisations de rôle**|
|--- | ---|
|Type standard | [Contributeur](../role-based-access-control/built-in-roles.md#contributor)<br>[Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|Type premium | [Contributeur](../role-based-access-control/built-in-roles.md#contributor)<br>[Propriétaire des données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Accédez au compte de stockage de réplication/cache sélectionné pour la réplication. Sélectionnez **Contrôle d’accès (IAM)** dans le volet gauche.

1. Dans la section **Ajouter une attribution de rôle**, sélectionnez **Ajouter** :

   ![Ajouter une attribution de rôle](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. Sur la page **Ajouter une attribution de rôle**, dans le champ **Rôle**, sélectionnez le rôle approprié dans la liste des autorisations mentionnée ci-dessus. Entrez le nom du coffre noté précédemment, puis sélectionnez **Enregistrer**.

    ![Fournir l’accès basé sur des rôles](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. En plus de ces autorisations, vous devez également autoriser l’accès aux services approuvés Microsoft. Si votre accès réseau est limité aux réseaux sélectionnés, sélectionnez **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** dans la section **Exceptions** qui se trouve sous l’onglet **Mise en réseau**.

![Autoriser les services Microsoft approuvés à accéder au compte de stockage](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Créer un point de terminaison privé pour le compte de stockage (facultatif)

Pour répliquer à l’aide de ExpressRoute avec le peering privé, [créez un point de terminaison privé](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) pour les comptes de stockage de cache/réplication (sous-ressource cible : **_blob_**).

>[!Note]
>
> - Vous pouvez créer des points de terminaison privés uniquement sur un compte de stockage Usage général v2 (GPv2). Pour plus d’informations sur la tarification, consultez [Tarification des objets blob de pages Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) et [Tarification d’Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

Créez le point de terminaison privé pour le compte de stockage dans le même réseau virtuel que le point de terminaison privé du projet Azure Migrate ou dans un autre réseau virtuel connecté à ce réseau.

Sélectionnez **Oui** et intégrez à une zone DNS privée. La zone DNS privée permet d’acheminer les connexions entre le réseau virtuel et le compte de stockage via une liaison privée. L’option **Oui** associe automatiquement la zone DNS au réseau virtuel et ajoute les enregistrements DNS pour la résolution des nouvelles adresses IP et des noms de domaine complets créés. En savoir plus sur les [zones DNS privées.](../dns/private-dns-overview.md)

Si l’utilisateur qui crée le point de terminaison privé est également le propriétaire du compte de stockage, la création du point de terminaison privé sera automatiquement approuvée. Dans le cas contraire, le propriétaire doit approuver l’utilisation du point de terminaison privé. Pour approuver ou rejeter une connexion de point de terminaison privé demandée, accédez à **Connexions des points de terminaison privés** sous **Mise en réseau** sur la page du compte de stockage.

Passez en revue le statut de l’état de connexion du point de terminaison privé avant de continuer.

![État d’approbation du point de terminaison privé](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Une fois le point de terminaison privé créé, utilisez la liste déroulante dans **Répliquer** > **Paramètres de la cible** > **Compte de stockage de cache** pour sélectionner le compte de stockage à répliquer sur une liaison privée.  

Assurez-vous que l’appliance de réplication locale dispose d’une connectivité réseau au compte de stockage sur son point de terminaison privé. [Découvrez la procédure de vérification de la connectivité réseau](./troubleshoot-network-connectivity.md).

>[!Note]
>
> - Pour les migrations de machines virtuelles Hyper-V vers Azure, si le compte de stockage de réplication est de type _Premium_, vous devez sélectionner un autre compte de stockage de type _Standard_ pour le compte de stockage de cache. Dans ce cas, vous devez créer des points de terminaison privés pour le compte de stockage de réplication et le compte de stockage de cache.  

Ensuite, suivez ces instructions pour [évaluer et démarrer la réplication](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) et [effectuer des migrations](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration).


## <a name="next-steps"></a>Étapes suivantes
- [Terminez le processus de migration](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration) et passez en revue les [meilleures pratiques post-migration](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices).
