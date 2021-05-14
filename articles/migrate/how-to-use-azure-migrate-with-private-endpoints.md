---
title: Utilisation d’Azure Migrate avec des points de terminaison privés
description: Utilisez le support de liaison privée Azure Migrate pour découvrir, évaluer et migrer à l’aide d’une liaison privée.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: 02952ab57eeaaec8700ffa97101060558370dd64
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108147118"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Utilisation d’Azure Migrate avec des points de terminaison privés  

Cet article décrit l’utilisation d’Azure Migrate pour découvrir, évaluer et migrer des serveurs sur un réseau privé à l’aide d’une [liaison privée Azure](../private-link/private-endpoint-overview.md). 

Vous pouvez utiliser les outils [Azure Migrate : découverte et évaluation](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) et [Azure Migrate :migration de serveur](./migrate-services-overview.md#azure-migrate-server-migration-tool) pour vous connecter en privé et en toute sécurité au service Azure Migrate via un peering privé ExpressRoute ou une connexion VPN de site à site à l’aide d’une liaison privée Azure. 

La méthode de connectivité du point de terminaison privé est recommandée lorsqu’il existe une exigence organisationnelle pour accéder au service Azure Migrate et à d’autres ressources Azure sans traverser les réseaux publics. Vous pouvez également utiliser le support de liaison privée pour utiliser vos circuits de peering privé ExpressRoute existants afin d’améliorer la bande passante ou les exigences de latence. 

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
> Les [migrations VMware sans agent](./tutorial-migrate-vmware.md) requièrent un accès ou une connectivité Internet via le peering ExperessRoute Microsoft. <br/> [Découvrez comment](./replicate-using-expressroute.md) utiliser des points de terminaison privés pour effectuer des réplications via un peering privé ExpressRoute ou une connexion VPN de site à site (S2S).  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Autres outils intégrés

Certains outils de migration peuvent ne pas pouvoir charger de données d’utilisation dans le projet Azure Migrate si l’accès au réseau public est désactivé. Le projet Azure Migrate doit être configuré de façon à autoriser le trafic de tous les réseaux à recevoir des données provenant d’autres offres d’ [éditeurs de logiciels indépendants (ISV)](./migrate-services-overview.md#isv-integration) Microsoft ou externes. 


Pour activer l’accès au réseau public pour le projet Azure Migrate, accédez à la **page de propriétés** Azure Migrate sur le portail Azure, sélectionnez **Non**, puis **Enregistrer**.

![Diagramme montrant la procédure de modification d’un mode d’accès au réseau.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Autres éléments à prendre en compte   

**Considérations** | **Détails**
--- | --- 
**Tarification** | Pour plus d’informations sur la tarification, consultez [Tarification d’objet blob Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) et [Tarification de liaison privée Azure](https://azure.microsoft.com/pricing/details/private-link/).  
**Conditions requises pour le réseau virtuel** | Le point de terminaison de la passerelle ExpressRoute/VPN doit résider dans le réseau virtuel sélectionné ou dans un réseau virtuel connecté à celui-ci. Vous pourriez avoir besoin d’environ 15 adresses IP dans le réseau virtuel.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Créer un projet avec une connectivité de point de terminaison privé

Utilisez cet [article](./create-manage-projects.md#create-a-project-for-the-first-time) pour configurer un nouveau projet Azure Migrate. 

> [!Note]
> Vous ne pouvez pas modifier la méthode de connectivité de point de terminaison privé pour les projets Azure Migrate existants.

Dans la section de configuration **avancée**, indiquez les détails ci-dessous pour créer un point de terminaison privé pour votre projet Azure Migrate.
- Dans **Méthode de connectivité**, choisissez **Point de terminaison privé**. 
- Dans **Désactiver l’accès au point de terminaison public**, conservez le paramètre par défaut **Non**. Certains outils de migration sont susceptibles de ne pas pouvoir charger de données d’utilisation dans le projet Azure Migrate si l’accès au réseau public est désactivé. [En savoir plus.](#other-integrated-tools)
- Dans **Abonnement au réseau virtuel**, sélectionnez l’abonnement pour le réseau virtuel de point de terminaison privé. 
- Dans **Réseau virtuel**, sélectionnez le réseau virtuel pour le point de terminaison privé. L’appliance Azure Migrate et les autres composants logiciels qui doivent se connecter au projet Azure Migrate doivent se trouver sur ce réseau ou sur un réseau virtuel connecté.
- Dans **Sous-réseau**, sélectionnez le sous-réseau pour le point de terminaison privé. 

Sélectionnez **Create** (Créer). Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. Ne fermez pas cette page lorsque la création du projet est en cours.

![Créer un projet](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Cela crée un projet migrate et y joint un point de terminaison privé. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Découvrir et évaluer les serveurs à des fins de migration à l’aide d’une liaison privée Azure 

### <a name="set-up-the-azure-migrate-appliance"></a>Configurer l’appliance Azure Migrate 

1. Dans **Détecter des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez le type de serveur.
2. Dans **Générer une clé de projet Azure Migrate**, fournissez un nom pour l’appliance Azure Migrate. 
3. Sélectionnez **Générer une clé** pour créer les ressources Azure requises. 

    > [!Important]
    > Ne fermez pas la page Découvrir des machines pendant la création de ressources.  
    - À cette étape, Azure Migrate crée un coffre de clés, un compte de stockage, un coffre Recovery Services (uniquement pour les migrations VMware sans agent), ainsi que quelques ressources internes, et joint un point de terminaison privé à chaque ressource. Les points de terminaison privés sont créés dans le réseau virtuel sélectionné lors de la création du projet.  
    - Une fois les points de terminaison privés créés, les enregistrements de ressource CNAME DNS pour les ressources Azure Migrate sont mis à jour vers un alias dans un sous-domaine avec le préfixe « privatelink ». Par défaut, Azure Migrate crée également une zone DNS privée correspondant au sous-domaine « privatelink » pour chaque type de ressource et insère des enregistrements A DNS pour les points de terminaison privés associés. Cela permet à l’appliance Azure Migrate et à d’autres composants logiciels résidant sur le réseau source d’atteindre les points de terminaison de ressource Azure Migrate sur des adresses IP privées.  
    - Azure Migrate active également une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour le projet migrate et accorde des autorisations à l’identité managée pour accéder en toute sécurité au compte de stockage.  

4. Une fois la clé correctement générée, copiez les détails de la clé pour configurer et inscrire l’appliance.   

#### <a name="download-the-appliance-installer-file"></a>Télécharger le fichier du programme d’installation de l’appliance  

L’outil Azure Migrate : découverte et évaluation utilise une appliance Azure Migrate légère. L’appliance effectue la détection du serveur, puis envoie la configuration et les métadonnées de performances du serveur à Azure Migrate.

Pour configurer l’appliance, téléchargez le fichier ZIP contenant le script du programme d’installation à partir du portail. Copiez le fichier zip sur le serveur qui hébergera l’appliance. Une fois le fichier ZIP téléchargé, vérifiez la sécurité du fichier et exécutez le script du programme d’installation pour déployer l’appliance. 

Voici les liens de téléchargement pour chacun des scénarios avec leurs valeurs de hachage :

Scénario | Télécharger le lien | Valeur de hachage
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | 17EFA01E3A7683F1CE2A08E3A9197A27D8BD2CC03C3AB5C6E00E4261A822BDB3
Physique | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 01028F92C2095452F2DDCB89986CDC1F177AAC58E150A5B219A69CF1B7DA3BE0
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 66D3217AEC1DE51D84EC608B22BDDA605EC9C4FBAB06FC69FEC985886627C224
Scale-out VMware | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 42C1E8D5CF428E35E5B98E4E7465DD08439F0FD5C319340CE3E3ADC3DC1717A6

#### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation du cloud public : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  Vérifiez la version la plus récente de l’appliance en comparant les valeurs de hachage du tableau ci-dessus.

Assurez-vous que le serveur répond aux [exigences matérielles](./migrate-appliance.md) pour le scénario choisi (VMware/Hyper-V/physique ou autre) et peut se connecter aux URL Azure requises : clouds [publics](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) et du [secteur public](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity).


#### <a name="run-the-script"></a>Exécuter le script

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance. 
2. Lancez PowerShell sur la machine avec un privilège administrateur (élevé).
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1```
   
5. Une fois le script correctement exécuté, il lance le gestionnaire de configuration de l’appliance afin que vous puissiez configurer l’appliance. En cas de problèmes, consultez les journaux du script sous C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Configurer l’appliance et démarrer la détection en continu

Ouvrez un navigateur sur une machine pouvant se connecter au serveur de l’appliance, puis ouvrez l’URL du gestionnaire de configuration de l’appliance : `https://appliance name or IP address: 44368`. Vous pouvez également ouvrir le gestionnaire de configuration à partir du bureau du serveur de l’appliance en sélectionnant le raccourci du gestionnaire de configuration.

#### <a name="set-up-prerequisites"></a>Configurer les prérequis

1. Lisez les informations tierces et acceptez les **termes du contrat de licence**.    
 
2. Dans le gestionnaire de configuration > **Configurer les prérequis**, procédez comme suit :
   - **Connectivité** : l’appliance vérifie l’accès aux URL requises. Si le serveur utilise un proxy :
     - Cliquez sur **Configurer le proxy** pour spécifier l’adresse du proxy `http://ProxyIPAddress` ou `http://ProxyFQDN`, ainsi que le port d’écoute.
     - Spécifiez les informations d’identification si le proxy nécessite une authentification. Seuls les proxys HTTP sont pris en charge.
     - Si vous le souhaitez, vous pouvez ajouter une liste d’URL/adresses IP qui doivent contourner le serveur proxy. Si vous utilisez le peering privé ExpressRoute, veillez à contourner ces [URL](./replicate-using-expressroute.md#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations).
     - Vous devez cliquer sur **Enregistrer** pour inscrire la configuration si vous avez mis à jour les détails du serveur proxy ou avez ajouté des URL/adresses IP pour contourner le proxy.
     
        > [!Note]
        > Si vous obtenez une erreur avec le lien aka.ms/* lors de la vérification de la connectivité et que vous ne souhaitez pas que l’appliance accède à cette URL via Internet, vous devez désactiver le service de mise à jour automatique sur l’appliance en suivant les étapes [**ici**](./migrate-appliance.md#turn-off-auto-update). Une fois la mise à jour automatique désactivée, la vérification de la connectivité de l’URL aka.ms/* est ignorée. 

   - **Synchronisation de l’heure** : L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
   - **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées. Une fois la vérification terminée, vous pouvez cliquer sur **Voir les services d'appliance** pour voir l’état et les versions des services s’exécutant sur le serveur de l’appliance.
        > [!Note]
        > Si vous avez choisi de désactiver le service de mise à jour automatique sur l’appliance, vous pouvez mettre à jour les services de l’appliance manuellement pour obtenir les dernières versions des services en procédant comme décrit [**ici**](./migrate-appliance.md#manually-update-an-older-version).
   - **Installez VDDK** : (_uniquement nécessaire pour l’appliance VMware)_ l’appliance vérifie que le VDDK (Virtual Disk Development Kit) de VMware vSphere est installé. S’il n’est pas installé, téléchargez VDDK 6.7 à partir de VMware, puis extrayez le contenu du fichier zip téléchargé sur l’appliance à l’emplacement indiqué dans les **instructions d’installation**.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Inscrire l’appliance et démarrer la détection en continu

Une fois la vérification des configurations requises terminée, procédez comme suit pour inscrire l’appliance et démarrer la détection en continu pour les scénarios correspondants : [machines virtuelles VMware](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate), [machines virtuelles Hyper-V](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate), [serveurs physiques](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate), [machines virtuelles AWS](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate), [machines virtuelles GCP](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate).


>[!Note]
> Si vous rencontrez des problèmes de résolution DNS lors de l’inscription de l’appliance ou au moment du démarrage de la détection, assurez-vous que les ressources Azure Migrate créées à l’étape **Générer la clé** sur le portail sont accessibles à partir du serveur local qui héberge l’appliance Azure Migrate. [En savoir plus sur la vérification de la connectivité réseau](#troubleshoot-network-connectivity).

### <a name="assess-your-servers-for-migration-to-azure"></a>Évaluer vos serveurs en vue de leur migration vers Azure
Une fois la détection terminée, évaluez vos serveurs ([machines virtuelles VMware](./tutorial-assess-vmware-azure-vm.md), [machines virtuelles Hyper-V](./tutorial-assess-hyper-v.md), [serveurs physiques](./tutorial-assess-vmware-azure-vm.md), [machines virtuelles AWS](./tutorial-assess-aws.md), [machines virtuelles GCP](./tutorial-assess-gcp.md)) pour la migration vers des machines virtuelles Azure ou vers la solution VMware sur Azure (AVS) à l’aide de l’outil Azure Migrate : découverte et évaluation. 

Vous pouvez également [évaluer vos machines locales](./tutorial-discover-import.md#prepare-the-csv) avec l’outil Azure Migrate : découverte et évaluation en utilisant un fichier CSV (valeurs séparées par des virgules) importé.   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Migrer des serveurs vers Azure à l’aide d’une liaison privée Azure

Les sections suivantes décrivent les étapes nécessaires à l’utilisation d’Azure Migrate avec des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour les migrations à l’aide d’un peering privé ExpressRoute ou de connexions VPN.  

Cet article montre un chemin d’accès de déploiement de preuve de concept pour les réplications basées sur un agent afin de migrer vos [machines virtuelles VMware](./tutorial-migrate-vmware-agent.md), [machines virtuelles Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [serveurs physiques](./tutorial-migrate-physical-virtual-machines.md), [machines virtuelles exécutées sur AWS](./tutorial-migrate-aws-virtual-machines.md), [machines virtuelles exécutées sur GCP](./tutorial-migrate-gcp-virtual-machines.md) ou vos machines virtuelles exécutées sur un autre fournisseur de virtualisation à l’aide de points de terminaison privés Azure. Vous pouvez utiliser une approche similaire pour effectuer des [migrations Hyper-V sans agent](./tutorial-migrate-hyper-v.md) à l’aide d’une liaison privée.

>[!Note]
>Les [migrations VMware sans agent](./tutorial-assess-physical.md) requièrent un accès ou une connectivité Internet via le peering ExperessRoute Microsoft. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Configurer une appliance de réplication pour la migration 

Le diagramme suivant illustre le workflow de réplication basé sur un agent avec des points de terminaison privés à l’aide de l’outil Azure Migrate : migration de serveur.  

![Architecture de réplication](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

L’outil ses sert d’une appliance de réplication pour répliquer vos serveurs sur Azure. Utilisez cet article pour [préparer et configurer une machine pour l’appliance de réplication. ](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance)

Une fois l’appliance de réplication configurée, suivez les instructions ci-dessous pour créer les ressources nécessaires à la migration. 

1. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Non virtualisé/Autre**.
2. Dans **Région cible**, sélectionnez et confirmez la région Azure vers laquelle vous souhaitez migrer les machines.
3. Sélectionnez **Créer des ressources** pour créer les ressources Azure nécessaires. Ne fermez pas la page pendant la création des ressources.   
    - Cela crée un coffre Recovery Services en arrière-plan et active une identité managée pour le coffre. Un coffre Recovery Services est une entité qui contient les informations de réplication des serveurs et qui permet de déclencher des opérations de réplication.  
    - Si le projet Azure Migrate dispose d’une connectivité de point de terminaison privée, un point de terminaison privé est créé pour le coffre Recovery Services. Cela ajoute cinq noms privés complets (FQDN) au point de terminaison privé, un pour chaque microservice lié au coffre Recovery Services.   
    - Les cinq noms de domaine sont mis en forme dans ce modèle : <br/> _{Vault-ID}-asr-pod01-{type}-.{target-geo-code}_ .privatelink.siterecovery.windowsazure.com  
    - Par défaut, Azure Migrate crée automatiquement une zone DNS privée et ajoute des enregistrements A DNS pour les microservices du coffre Recovery Services. La zone DNS privée est ensuite liée au réseau virtuel du point de terminaison privé. Cela permet à l’appliance de réplication locale de résoudre les noms de domaine complets vers leurs adresses IP privées.

4. Avant d’inscrire l’appliance de réplication, assurez-vous que les FQDN de la liaison privée du coffre sont accessibles à partir de la machine hébergeant l’appliance de réplication. [Découvrez la procédure de vérification de la connectivité réseau](#troubleshoot-network-connectivity). 

5. Une fois la connectivité vérifiée, téléchargez le fichier de configuration et de clé de l’appliance, exécutez le processus d’installation et enregistrez l’appliance dans Azure Migrate. Passez en revue les [étapes détaillées ici](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). Une fois l’appliance de réplication configurée, suivez ces instructions pour [installer le service de mobilité](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service) sur les machines que vous souhaitez migrer. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Répliquer des serveurs vers Azure à l’aide d’une liaison privée Azure 

À présent, suivez[ces étapes](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) pour sélectionner des serveurs pour la réplication.  

Dans **Répliquer** > **Paramètres de la cible** > **Compte de stockage de cache/réplication**, utilisez la liste déroulante pour sélectionner un compte de stockage à répliquer sur une liaison privée.  

Si votre projet Azure Migrate dispose d’une connectivité de point de terminaison privé, vous devez [accorder des autorisations à l’identité managée du coffre Recovery Services](#grant-access-permissions-to-the-recovery-services-vault) pour accéder au compte de stockage requis par Azure Migrate.   

De plus, pour activer les réplications sur une liaison privée, [créez un point de terminaison privé pour le compte de stockage.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Accorder des autorisations d’accès au coffre Recovery Services

L’identité managée du coffre Recovery Services requiert des autorisations pour l’accès authentifié au compte de stockage de cache/réplication. 

Utilisez les instructions ci-dessous pour identifier le coffre Recovery Services créé par Azure Migrate et accordez les autorisations requises. 

**_Identifier le coffre Recovery Services et l’ID de l’objet d’identité managée_**

Vous trouverez les détails du coffre Recovery Services sur la page **de propriétés** d’Azure Migrate : migration de serveur. 

1. Accédez au **hub Azure Migrate**, sélectionnez **Vue d’ensemble** sur la vignette Azure Migrate : migration de serveur.

    ![Page de présentation du hub Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. Dans le volet gauche, sélectionnez **Propriétés**. Notez le nom du coffre Recovery Services et l’ID d’identité managée. Le coffre a un _Point de terminaison privé_ comme **type de connectivité** et _Autre_ comme **type de réplication**. Vous aurez besoin de ces informations pour fournir l’accès au coffre.
      
    ![Page de propriétés Azure Migrate : migration de serveur](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Accorder les autorisations requises pour accéder au compte de stockage_**

 L’identité managée du coffre doit disposer des autorisations de rôle suivantes sur le compte de stockage requis pour la réplication.  Dans ce cas, vous devez créer le compte de stockage à l’avance.

>[!Note]
> Pour migrer des machines virtuelles Hyper-V vers Azure à l’aide d’une liaison privée, vous devez accorder l’accès au compte de stockage de réplication et au compte de stockage de cache. 

Les autorisations des rôles varient en fonction du type de compte de stockage.

- Comptes de stockage basés sur Resource Manager (type Standard) :
  - [Contributeur](../role-based-access-control/built-in-roles.md#contributor) _et_
  - [Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Comptes de stockage basés sur Resource Manager (type Premium) :
  - [Contributeur](../role-based-access-control/built-in-roles.md#contributor) _et_
  - [Propriétaire des données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

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
> - Vous pouvez créer des points de terminaison privés uniquement sur un compte de stockage Usage général v2 (GPv2). Pour plus d’informations sur la tarification, consultez [Tarification des objets Blob de pages Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) et [Tarification des liaisons privées Azure](https://azure.microsoft.com/pricing/details/private-link/)

Le point de terminaison privé pour le compte de stockage doit être créé dans le même réseau virtuel que le point de terminaison privé du projet Azure Migrate ou dans un autre réseau virtuel connecté à ce réseau. 

Sélectionnez **Oui** et intégrez à une zone DNS privée. La zone DNS privée permet d’acheminer les connexions entre le réseau virtuel et le compte de stockage via une liaison privée. L’option **Oui** associe automatiquement la zone DNS au réseau virtuel et ajoute les enregistrements DNS pour la résolution des nouvelles adresses IP et des noms de domaine complets créés. En savoir plus sur les [zones DNS privées.](../dns/private-dns-overview.md)

Si l’utilisateur qui crée le point de terminaison privé est également le propriétaire du compte de stockage, le point de terminaison privé sera automatiquement approuvé. Dans le cas contraire, le propriétaire doit approuver l’utilisation du point de terminaison privé. Pour approuver ou rejeter une connexion de point de terminaison privé demandée, accédez à **Connexions des points de terminaison privés** sous **Mise en réseau** sur la page du compte de stockage.

Passez en revue le statut de l’état de connexion du point de terminaison privé avant de continuer. 

![État d’approbation du point de terminaison privé](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Une fois le point de terminaison privé créé, utilisez la liste déroulante dans **Répliquer** > **Paramètres de la cible** > **Compte de stockage de cache** pour sélectionner le compte de stockage à répliquer sur une liaison privée.  

Assurez-vous que l’appliance de réplication locale dispose d’une connectivité réseau au compte de stockage sur son point de terminaison privé. [Découvrez la procédure de vérification de la connectivité réseau](#troubleshoot-network-connectivity).

>[!Note]
>
> - Pour les migrations de machines virtuelles Hyper-V vers Azure, si le compte de stockage de réplication est de type _Premium_, vous devez sélectionner un autre compte de stockage de type _Standard_ pour le compte de stockage de cache. Dans ce cas, vous devez créer des points de terminaison privés pour le compte de stockage de réplication et le compte de stockage de cache.  

Ensuite, suivez ces instructions pour [évaluer et démarrer la réplication](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) et [effectuer des migrations](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration).  

## <a name="troubleshoot-network-connectivity"></a>Résoudre les problèmes de connectivité réseau 

### <a name="validate-private-endpoints-configuration"></a>Valider la configuration des points de terminaison privés 

Assurez-vous que le point de terminaison privé présente l’état Approuvé.  

1. Accédez à la page de propriétés Azure Migrate : découverte et évaluation et migration de serveur.
2. La page de propriétés contient la liste des points de terminaison privés et des FQDN de liaison privée créés automatiquement par Azure Migrate.  

3. Sélectionnez le point de terminaison privé que vous souhaitez diagnostiquer.  
    1. Vérifiez que l’état de la connexion indique Approuvée.
    2. Si la connexion présente l’état En attente, vous devez l’approuver.
    3. Vous pouvez également accéder à la ressource de point de terminaison privé et vérifier si le réseau virtuel correspond au réseau virtuel de point de terminaison privé du projet Migrate. 

    ![Afficher une connexion de point de terminaison privé](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>Vérifier la résolution de DNS 

L’appliance locale (ou le fournisseur de réplication) accède aux ressources Azure Migrate à l’aide de leur nom de domaine de liaison privée complet (FQDN). Vous pouvez avoir besoin de paramètres DNS supplémentaires pour résoudre l’adresse IP privée du point de terminaison privé à partir de l’environnement source. [Cet article](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) permet de comprendre les scénarios de configuration DNS qui peuvent vous aider à résoudre les problèmes de connectivité réseau.  

Pour valider la connexion de liaison privée, effectuez une résolution DNS des points de terminaison de ressource d’Azure Migrate (FQDN de ressource de liaison privée) à partir du serveur local hébergeant l’appliance Migrate, et assurez-vous qu’elle soit résolue vers une adresse IP privée. Les informations sur les détails du point de terminaison privé et sur les FQDN de la ressource de liaison privée sont disponibles dans les pages de propriétés Découverte et évaluation et Migration de serveur. Sélectionnez **Télécharger les paramètres DNS** pour afficher la liste.   

 ![Propriétés Azure Migrate : découverte et évaluation](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Propriétés Azure Migrate : migration de serveur](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Exemple illustrant la résolution DNS du FQDN de la liaison privée du compte de stockage.  

- Entrez _nslookup<nom-compte-stockage>_ .blob.core.windows.net.  Remplacez <nom-compte-stockage> par le nom du compte de stockage utilisé pour Azure Migrate.  

    Vous recevrez un message similaire à celui ci :  

   ![Exemple de résolution DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- L’adresse IP privée 10.1.0.5 est retournée pour le compte de stockage. Cette adresse appartient au sous-réseau du réseau virtuel du point de terminaison privé.   

Vous pouvez vérifier la résolution DNS pour d’autres artefacts Azure Migrate à l’aide d’une approche similaire.   

Si la résolution DNS est incorrecte, procédez comme suit :  

- Si vous utilisez un DNS personnalisé, vérifiez vos paramètres DNS personnalisés et confirmez que la configuration DNS est correcte. Pour obtenir de l’aide, consultez [Vue d’ensemble du point de terminaison privé : configuration DNS](../private-link/private-endpoint-overview.md#dns-configuration).
- Si vous utilisez des serveurs DNS fournis par Azure, reportez-vous à la section ci-dessous pour une résolutions des problèmes plus approfondie.  

> [!Tip]
> Vous pouvez mettre à jour manuellement les enregistrements DNS de votre environnement source en modifiant le fichier hôte DNS sur votre appliance locale avec les FQDN des ressources de liaison privée et leurs adresses IP privées associées. Cette option n’est recommandée qu’à des fins de test. <br/>  


### <a name="validate-the-private-dns-zone"></a>Valider la zone DNS privée   
Si la résolution DNS ne fonctionne pas comme décrit dans la section précédente, il peut y avoir un problème avec votre zone DNS privée.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirmer l’existence de la ressource de zone DNS privée requise  
Par défaut, Azure Migrate crée également une zone DNS privée correspondant au sous-domaine « privatelink » pour chaque type de ressource. La zone DNS privée est créée dans le même groupe de ressources Azure que le groupe de ressources du point de terminaison privé. Le groupe de ressources Azure doit contenir des ressources de zone DNS privée au format suivant :
- pprivatelink.vaultcore.azure.net pour le coffre de clés 
- privatelink.blob.core.windows.net pour le compte de stockage
- privatelink.siterecovery.windowsazure.com pour le coffre recovery services (pour les réplications Hyper-V et basées sur un agent)
- privatelink.prod.migration.windowsazure.com : migrez un projet, un projet d’évaluation et un site de détection.   

La zone DNS privée est créée automatiquement par Azure Migrate (sauf pour le compte de stockage de cache/de réplication sélectionné par l’utilisateur). Vous pouvez localiser la zone DNS privée liée en accédant à la page du point de terminaison privé et en sélectionnant les configurations DNS. Vous devez voir la zone DNS privée sous la section intégration DNS privée. 

![Capture d’écran de la configuration DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Si la zone DNS n’est pas présente (comme indiqué ci-dessous), [créez une nouvelle ressource de zone DNS privée.](../dns/private-dns-getstarted-portal.md)  

![Créer une zone DNS privée](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Vérifier que la zone DNS privée est liée au réseau virtuel  
La zone DNS privée doit être liée au réseau virtuel qui contient le point de terminaison privé pour que la requête DNS résolve l’adresse IP privée du point de terminaison de la ressource. Si la zone DNS privée n’est pas liée au réseau virtuel approprié, toute résolution DNS à partir de ce réseau virtuel ignore la zone DNS privée.   

Accédez à la ressource de zone DNS privée dans le portail Azure et cliquez sur les liens du réseau virtuel à partir du menu. Vous devez voir les réseaux virtuels liés.

![Voir les liens de réseau virtuel](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Cette opération affiche une liste de liens, chacun avec le nom d’un réseau virtuel dans votre abonnement. Le réseau virtuel qui contient la ressource de point de terminaison privé doit être répertorié ici. Sinon, [suivez cet article](../dns/private-dns-getstarted-portal.md#link-the-virtual-network) pour lier la zone DNS privée à un réseau virtuel.    

Une fois la zone DNS privée liée au réseau virtuel, les requêtes DNS provenant du réseau virtuel recherchent des enregistrements DNS dans la zone DNS privée. C’est obligatoire pour une résolution d’adresse correcte sur le réseau virtuel sur lequel le point de terminaison privé a été créé.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Confirmer que la zone DNS privée contient l’enregistrement A approprié 

Accédez à la zone DNS privée que vous souhaitez dépanner. La page de présentation affiche tous les enregistrements DNS pour cette zone DNS privée. Vérifiez qu’un enregistrement A DNS existe pour la ressource. La valeur de l’enregistrement A (l’adresse IP) doit être l’adresse IP privée des ressources. Si vous trouvez l’enregistrement A mais qu’il contient une adresse IP incorrecte, vous devez supprimer l’adresse IP incorrecte et en ajouter une nouvelle. Il est recommandé de supprimer l’intégralité de l’enregistrement A et d’en ajouter un nouveau, puis d’effectuer un vidage DNS sur l’appliance source locale.   

Exemple illustrant l’enregistrement A DNS d’un compte de stockage dans la zone DNS privée :

![Enregistrements DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Exemple illustrant les enregistrements A DNS pour les microservices du coffre Recovery Services dans la zone DNS privée : 

![Enregistrements DNS pour le coffre Recovery Services](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Lorsque vous supprimez ou modifiez un enregistrement A, la machine peut toujours résoudre l’ancienne adresse IP car la TTL (durée de vie) n’a peut-être pas encore expiré.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Autres éléments susceptibles d’affecter la connectivité de liaison privée  

Il s’agit d’une liste non exhaustive d’éléments qui peuvent être présents dans des scénarios avancés ou complexes : 

- Les paramètres du Pare-feu, qu’il s’agisse du Pare-feu Azure connecté au réseau virtuel ou d’une solution de pare-feu personnalisée déployée dans la machine de l’appliance.  
- Le Peering de réseau, qui peut avoir un impact sur les serveurs DNS utilisés et sur la façon dont le trafic est acheminé.  
- Des solutions de passerelle personnalisée (NAT) peuvent avoir un impact sur la façon dont le trafic est acheminé, notamment le trafic des requêtes DNS.

Pour plus d’informations, consultez le [guide Résoudre les problèmes de connectivité Private Endpoint.](../private-link/troubleshoot-private-endpoint-connectivity.md)  

## <a name="next-steps"></a>Étapes suivantes 
- [Terminez le processus de migration](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration) et passez en revue les [meilleures pratiques post-migration](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices).