---
title: Migrer des charges de travail locales vers Azure avec Azure DMS et Site Recovery | Microsoft Docs
description: Apprenez à préparer Azure pour la migration des machines locales à l’aide des services Azure Database Migration et Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182239"
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>Scénario 2 : Migration lift-and-shift vers Azure

La société Contoso envisage la migration vers Azure. Pour s’y essayer, elle souhaite évaluer et migrer une petite application de type voyage locale vers Azure. Il s’agit d’une application de type voyage à deux niveaux, avec une application web s’exécutant sur la première machine virtuelle et une base de données SQL Server sur la deuxième machine virtuelle. L’application est déployée dans VMware, et l’environnement est géré par un serveur vCenter Server. 

Dans le [Scénario 1 : Évaluer la migration vers Azure](migrate-scenarios-assessment.md), elle utilise l’Assistant Migration de données (DMA) pour évaluer la base de données SQL Server pour l’application. De plus, elle utilise le service Azure Migrate pour évaluer les machines virtuelles de l’application. Dans ce scénario, après avoir terminé l’évaluation, elle souhaite migrer la base de données vers une instance gérée Azure SQL à l’aide du service Azure Database Migration (DMS), et les machines virtuelles locales vers les machines virtuelles Azure à l’aide du service Azure Site Recovery.

Si vous souhaitez tester le [Scénario 1](migrate-scenarios-assessment.md), puis ce scénario à l’aide de cette application de voyage utilisée à titre d’illustration, vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).



**Service** | **Description** | **Coût**
--- | --- | ---
[Service de gestion de base de données](https://docs.microsoft.com/azure/dms/dms-overview) | Le service DMS permet des migrations fluides depuis plusieurs sources de base de données vers des plateformes de données Azure, avec un temps d’arrêt minimal. | Le service est actuellement en préversion publique (avril 2018), et peut être utilisé gratuitement durant la période de préversion.<br/><br/> Remarque : le service DMS est limité à un [nombre de régions](https://docs.microsoft.com/azure/dms/dms-overview) pendant la préversion.
[Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance est un service de base de données gérée qui représente une instance SQL Server entièrement gérée dans le cloud Azure. Il partage le même code que la dernière version du moteur de base de données SQL Server et possède les dernières fonctionnalités, améliorations en termes de performances et les correctifs de sécurité. | L’utilisation d’instances gérées Azure SQL Database exécutées dans Azure entraîne des frais en fonction de la capacité. [Plus d’informations](https://azure.microsoft.com/pricing/details/sql-database/managed/) 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Le service orchestre et gère la migration et la récupération d’urgence pour les machines virtuelles Azure, les machines virtuelles locales et les serveurs physiques.  | Lors de la réplication vers Azure, des frais sur le Stockage Azure sont facturés.  Des machines virtuelles Azure sont créées en cas de basculement, et entraînent des frais. [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les frais et la tarification.

Dans ce scénario, nous allons configurer un VPN site à site pour que le service DMS puisse se connecter à la base de données locale, créer une instance Azure SQL Managed Instance dans Azure et migrer la base de données. Pour Site Recovery, nous allons préparer l’environnement VMware local, configurer la réplication et migrer les machines virtuelles vers Azure.  


> [!IMPORTANT]
> Vous devez avoir été sélectionné pour tester la préversion publique fermée de SQL Managed Instance. Vous avez besoin d’un abonnement Azure pour vous [inscrire](https://portal.azure.com#create/Microsoft.SQLManagedInstance). La confirmation d’inscription prend quelques joueurs. Veillez donc à l’effectuer avant de commencer le déploiement de ce scénario.

## <a name="architecture"></a>Architecture

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

Dans ce scénario :

- Contoso est un nom fictif représentant une entreprise classique. Contoso souhaite évaluer et migrer son application de voyage locale à deux niveaux.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- L’application de voyage interne repose sur deux machines virtuelles, WEBVM et SQLVM, et se trouve sur l’hôte VMware ESXi (**contosohost1.contoso.com**).
- L’environnement VMware est géré par le serveur vCenter Server (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.

## <a name="prerequisites"></a>Prérequis


Si vous voulez exécuter ce scénario, voici ce qu’il vous faut.

Configuration requise | Détails
--- | ---
**Abonnement Azure** | Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.<br/><br/> S’il vous faut plus d’autorisations granulaires, consultez [cet article](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Récupération de site (local)** | Un serveur vCenter Server local qui exécute la version 5.5, 6.0 ou 6.5<br/><br/> Un hôte ESXi qui exécute la version 5.5, 6.0 ou 6.5<br/><br/> Une ou plusieurs machines virtuelles VMware exécutées sur l’hôte ESXi.<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuration [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) prise en charge.
**DMS** | Pour le service DMS, il vous faut un [appareil VPN local compatible](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Vous devez être en mesure de configurer l’appareil VPN local. Il doit disposer d’une adresse IPv4 publique. Elle ne peut pas être placée derrière un appareil NAT.<br/><br/> Veillez à disposer de l’accès à votre base de données SQL Server locale.<br/><br/> Le pare-feu Windows doit pouvoir accéder au moteur de la base de données source. [Plus d’informations](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)<br/><br/> S’il y a un pare-feu devant votre machine de base de données, ajoutez des règles pour autoriser l’accès à la base de données, et aux fichiers via le port SMB 445.<br/><br/> Les informations d’identification utilisées pour se connecter à l’instance source SQL Server et à l’instance cible Managed Instance doivent appartenir à des membres du rôle serveur sysadmin.<br/><br/> Il vous faut un partage réseau dans votre base de données locale que le service DMS peut utiliser pour sauvegarder la base de données source.<br/><br/> Vérifiez que le compte de service qui exécute l’instance source de SQL Server dispose de privilèges d’écriture pour le partage réseau.<br/><br/> Notez l’utilisateur Windows (et son mot de passe) qui dispose d’un contrôle total sur le partage réseau. Azure Database Migration Service emprunte ces informations d’identifications pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure.<br/><br/> Le processus d’installation SQL Server Express définit le protocole TCP/IP sur **Désactivé** par défaut. Veillez à l’activer.


# <a name="scenario-overview"></a>Présentation du scénario

Voici ce que nous allons faire :

> [!div class="checklist"]
> * **Étape 1 : configuration d’une connexion VPN site à site**. Le service DMS se connecte à votre base de données locale via une connexion VPN site à site. Nous créons un réseau virtuel pour la connexion VPN et, plus tard, le réutiliserons pour Site Recovery. Le réseau et le coffre Recovery Services doivent se trouver dans la même région.
> * **Étape 2 : configuration d’une instance SQL Azure Managed Instance**. Vous devez créer au préalable une instance gérée vers laquelle la base de données SQL Server locale migrera.
> * **Étape 3 : configuration d’une signature d’accès URI pour DMS**. Une signature d’accès partagé (SAP) Uniform Resource Identifier (URI) fournit un accès délégué aux ressources présentes dans votre compte de stockage, pour que vous puissiez attribuer des autorisations limitées aux objets de stockage. Configurez une SAP URI pour que le service DMS puisse accéder au conteneur du compte de stockage sur lequel le service charge les fichiers de sauvegarde SQL Server.
> * **Étape 4 : préparation du service DMS**. Vous inscrivez le fournisseur de migration de base de données, créez une instance puis un projet DMS.
> * **Étape 5 : préparation d’Azure pour Site Recovery**. Vous créez un compte de stockage Azure pour conserver les données répliquées.
> * **Étape 6 : préparation d’une machine virtuelle VMware locale pour Site Recovery**. Vous préparez des comptes pour la découverte de machine virtuelle et l’installation d’agents, et préparez la connexion aux machines virtuelles Azure après basculement. 
> * **Étape 7 : réplication des machines virtuelles**. Vous configurez l’environnement source et cible de Site Recovery ainsi qu’une stratégie de réplication et commencez la réplication des machines virtuelles vers le stockage Azure.
> * **Étape 8 : migration de la base de données avec DMS**. Vous exécutez une migration de base de données.
> * **Étape 9 : migration des machines virtuelles avec Site Recovery**. Vous exécutez un basculement pour migrer les machines virtuelles vers Azure.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>Étape 1 : Configuration d’une connexion VPN de site à site

Pour préparer une connexion VPN de site à site, vous configurez un réseau virtuel et des sous-réseaux, créez une passerelle VPN pour le réseau virtuel et configurez une passerelle de réseau locale pour qu’Azure sache comment se connecter à votre VPN local. Ensuite, créez et vérifiez la connexion site à site.

### <a name="set-up-a-virtual-network"></a>Configuration d’un réseau virtuel

Créez un réseau virtuel Azure pour fournir au service DMS une connectivité site à site à votre instance SQL Server locale.


1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**. Sélectionnez **Mise en réseau** > **Réseau virtuel**.
2. Dans **Réseau virtuel** > **Sélectionner un modèle de déploiement**, sélectionnez **Resource Manager** > **Créer**
3. Dans **Créer un réseau virtuel** > **Nom**, saisissez un nom de réseau unique. Dans notre scénario, le nom est **ContosoVNET**.
4. Dans **Espace d’adressage**, ajoutez la plage d’adresses IP. La plage ne doit pas chevaucher votre espace d’adressage local.
5. Dans **Groupe de ressources**, créez un groupe ou sélectionnez un groupe existant. Dans ce scénario, nous utilisons le nom **ContosoRG**.
6. Dans **Emplacement**, sélectionnez la région dans laquelle créer le réseau virtuel. Nous utilisons **Est des États-Unis 2**.
7. Dans **Sous-réseau**, ajoutez le nom et la plage d’adresses du premier sous-réseau. Nous avons créé le sous-réseau **Apps**.
8. Désactivez les points de terminaison de service.

    ![Créez un réseau virtuel](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Sélectionnez **Épingler au tableau de bord** afin de retrouver votre réseau plus facilement à l’avenir, puis cliquez sur **Créer**.
10. La création du réseau virtuel prend quelques secondes. Une fois qu’il est créé, vérifiez s’il apparait sur tableau de bord du portail Azure.
11. Veillez à ce que les ports de communication suivants soient autorisés dans votre réseau virtuel : 443, 53, 9354, 445, 12000.


### <a name="set-up-subnets"></a>Configuration des sous-réseaux

Nous disposerons de quatre sous-réseaux dans notre réseau Azure :

![Liste des sous-réseaux](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

Vous avez configuré le premier sous-réseau (Apps) lors de la création du réseau. Créez maintenant les sous-réseaux restants. Le sous-réseau de passerelle est utilisé par la connexion de la passerelle VPN, pour qu’Azure achemine le trafic via une connexion VPN à votre site local.

1. Dans le réseau virtuel, sous **Paramètres**, cliquez sur **Sous-réseau** > **+Sous-réseau**.
2. Configurez un sous-réseau **Migration des données**, avec cette plage d’adresses, puis cliquez sur **OK**.

    ![Sous-réseau Données](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. Dans **+Sous-réseau**, configurez un sous-réseau **Identité**, avec cette plage d’adresses, puis cliquez sur **OK**.
    ![Sous-réseau Identité](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. Dans **Sous-réseau**, cliquez sur **Sous-réseau de passerelle**, puis sur **OK**.
    - Ce sous-réseau contient les adresses IP que votre passerelle VPN utilisera pour les connexions VPN.
    - Le nom de ce sous-réseau est défini automatiquement pour qu’Azure le reconnaisse.
    - Ajustez la plage d’adresse renseignée automatiquement pour correspondre au sous-réseau local. 

    ![Sous-réseau de passerelle](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>Configuration d’une passerelle de réseau virtuel

1. Sur le côté gauche de la page du portail, cliquez sur **+** et tapez « Passerelle de réseau virtuel » dans la barre de recherche. Dans **Résultats**, cliquez sur **Passerelle de réseau virtuel**.
2. En bas de la page Passerelle de réseau virtuel, cliquez sur **Créer**.
3. Dans **Créer une passerelle de réseau virtuel** >  **Nom**, spécifiez un nom pour l’objet de passerelle.
4. Dans **Type de passerelle**, sélectionnez **VPN**.
5. Dans **Type de VPN**, sélectionnez **Basé sur l’itinéraire**.
6. Dans **Référence**, sélectionnez la référence SKU de passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. N’activez pas le mode Actif/actif. [En savoir plus](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) sur les références SKU.
7. Dans **Réseau virtuel** > **Choisir un réseau virtuel**, ajoutez le réseau que vous avez créé. La passerelle sera ajoutée à ce réseau.
8. Dans **Première configuration IP**, spécifiez une adresse IP publique.  Une passerelle VPN doit avoir une adresse IP publique. Cette adresse est affectée dynamiquement à la ressource lors de la création de la passerelle VPN. Seules les adresses dynamiques sont actuellement prises en charge mais les adresses IP ne changent pas après avoir été assignées.
    - Cliquez sur **Créer la configuration IP de la passerelle**. Dans **Choisir une adresse IP publique**, cliquez sur **+Créer**.
    - Dans **Créer une adresse IP publique**, spécifiez un nom pour votre adresse IP publique (Contoso-Gateway). Laissez la référence SKU en **De base**, puis cliquez sur **OK** afin d’enregistrer les modifications.
        
    ![Passerelle de réseau virtuel](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. Cliquez sur **Créer** pour créer la passerelle VPN. Les paramètres sont validés et la vignette « Déploiement d’une passerelle de réseau virtuel » s’affiche sur le tableau de bord.

    ![Valider la passerelle de réseau virtuel](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

### <a name="set-up-a-local-network-gateway"></a>Configurer une passerelle de réseau local

Configurez une passerelle de réseau local dans Azure pour représenter votre site local.

1. Dans le portail, cliquez sur **+Créer une ressource**.
2. Dans la zone de recherche, tapez **passerelle de réseau local**, puis appuyez sur **Entrée** pour lancer la recherche. Dans les résultats, cliquez sur **Passerelle de réseau local** > **Créer**.
3. Dans **Créer une passerelle de réseau local** > **Nom**, spécifiez un nom qu’Azure utilisera pour votre objet Passerelle de réseau local.
4. Dans **Adresse IP**, spécifiez l’adresse IP publique du périphérique VPN local auquel Azure se connectera. L’adresse IP ne doit pas être derrière un NAT, et Azure doit pouvoir y accéder.
5. Dans **Espace d’adressage**, saisissez les plages d’adresses de réseau local qui seront acheminées via la passerelle VPN vers le périphérique local. Veillez à ce qu’elles ne chevauchent pas les plages du réseau virtuel Azure.
6. **Configurer les paramètres BGP** n’est pas important pour ce scénario.
7. Dans **Abonnement**, vérifiez que l’abonnement affiché est correct.
8. Dans **Groupe de ressources**, sélectionnez le groupe de ressource que vous avez utilisé pour créer le réseau (ContosoRG).
9. Dans **Emplacement**, sélectionnez la région dans laquelle vous avez créé le réseau virtuel.

    ![Passerelle locale](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Cliquez sur **Créer** pour créer la passerelle locale.

### <a name="configure-your-on-premises-vpn-device"></a>Configuration de votre périphérique VPN local

Voici ce dont vous avez besoin pour configurer votre périphérique VPN local :

- L’adresse IPv4 publique de la passerelle de réseau virtuelle Azure que vous venez de créer.
- La clé pré-partagée (celle que vous avez utilisée lors de la création de la connexion VPN site à site).  

Pour vous aider à configurer votre périphérique VPN local :

- En fonction du périphérique VPN local dont vous disposez, vous pourrez peut-être télécharger un script de configuration de périphérique VPN. [Plus d’informations](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript)
- Consultez [plus de ressources utiles](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>Créer la connexion VPN

À présent, créez la connexion VPN de site à site entre la passerelle de réseau virtuel et votre périphérique VPN local.

1. Ouvrez la page du réseau virtuel dans **Réseaux virtuels** > **Vue d’ensemble** > **Périphériques connectés**. 
2. Cliquez sur **Connexions** > **+Ajouter**.
3. Dans **Ajouter une connexion** > **Nom**, spécifiez un nom pour la connexion.
4. Dans **Type de connexion**, sélectionnez **Site à site (IPSec)**.
5. La valeur **Passerelle de réseau virtuel** est fixe, car vous vous connectez à partir de cette passerelle.
6. Dans **Passerelle de réseau local**, spécifiez la passerelle de réseau local que vous avez créée.
7. Dans **Clé partagée**, spécifiez celle qui correspond à la valeur que vous utilisez pour votre périphérique VPN local. Dans cet exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
8. Les valeurs pour **Abonnement**, **Groupe de ressources**, et **Emplacement** sont fixes.

    ![Connexion VPN](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Cliquez sur **OK** pour créer la connexion. Le message « Création de la connexion » clignote à l’écran.
5. Après la création de la connexion, affichez-la sur la page **Connexions** de la passerelle de réseau virtuel. L’état passe de Inconnu à Connexion, puis à Réussi.

### <a name="verify-the-vpn-connection"></a>Vérifier la connexion VPN

Dans le portail Azure, vous pouvez afficher l’état de la connexion d’une passerelle VPN Resource Manager en accédant à cette connexion. 

1. Cliquez sur **Toutes les ressources** et accédez à votre passerelle de réseau virtuel.
2. Dans la page Passerelle de réseau virtuel, cliquez sur **Connexions**. Vous pouvez voir l’état de chaque connexion.
3. Cliquez sur le nom de la connexion et affichez plus d’informations dans **Essentiels**. Ce champ indique « Opération réussie » et « Connecté » lorsqu’une connexion a été établie.

Vérifiez maintenant que vous pouvez vous connecter à la machine virtuelle SQL Server via le VPN. Utilisez une connexion Bureau à distance et connectez-vous à l’adresse IP de la machine virtuelle.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>Étape 2 : préparation d’une instance Azure SQL Managed Instance

### <a name="set-up-a-virtual-network"></a>Configuration d’un réseau virtuel

Pour ce scénario, vous devrez créer un autre réseau virtuel dédié à l’instance Azure SQL Managed Instance.  Notez les exigences suivantes :

- Pour créer l’instance gérée, vous avez besoin d’un sous-réseau dédié.
- Le sous-réseau doit être vide et ne doit contenir aucun autre service cloud, et ne doit pas être un sous-réseau de passerelle. Après la création de l’instance gérée, vous ne devez pas ajouter de ressources au sous-réseau.
- Aucun groupe de sécurité ne doit être associé au sous-réseau.
- Le sous-réseau doit avoir une table de routage utilisateur (UDR) avec un itinéraire Internet de tronçon suivant 0.0.0.0/0 comme seul itinéraire affecté. 
- DNS personnalisé éventuel : si un DNS personnalisé est spécifié sur le réseau virtuel, vous devez ajouter l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) à la liste. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)
- Le sous-réseau ne doit pas avoir de point de terminaison de service (stockage ou SQL) associé. Les points de terminaison doivent être désactivés sur le réseau virtuel.
- Le sous-réseau doit avoir un minimum de 16 adresses IP. [En savoir plus](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) sur le dimensionnement d’un sous-réseau d’instance gérée.

Configurez le réseau virtuel comme suit :

1.  Dans le portail Azure, cliquez sur **Créer une ressource**. 
2. Sélectionnez **Mise en réseau** > **Réseau virtuel**.
3. Dans **Réseau virtuel** > **Sélectionner un modèle de déploiement**, sélectionnez **Resource Manager** > **Créer**.
4. Dans **Créer un réseau virtuel** > **Nom**, saisissez un nom de réseau unique. Dans notre scénario, **ContosoVNETSQLMI**.
5. Dans **Espace d’adressage**, ajoutez la plage d’adresses IP indiquée ci-dessous. La plage ne doit pas chevaucher votre espace d’adressage local et ContosoVNET.
6. Dans **Groupe de ressources**, créez un groupe ou sélectionnez un groupe existant. Dans ce scénario, nous utilisons le nom **ContosoRG**.
7. Dans **Emplacement**, sélectionnez la région dans laquelle créer le réseau virtuel. Nous utilisons **Est des États-Unis 2**.
8. Dans **Sous-réseau**, ajoutez le nom et la plage d’adresses du premier sous-réseau. Nous avons créé le sous-réseau **Instances gérées**.
9. Désactivez les points de terminaison de service.

    ![Réseau de l’instance gérée](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. Une fois le réseau déployé, vous devez modifier les paramètres DNS. Dans ce scénario, le DNS pointe d’abord vers un contrôleur de domaine dans le sous-réseau Identité à l’aide d’une adresse IP privée statique (172.16.3.4), puis de l’outil de résolution Azure DNS 168.63.129.16.

    ![Réseau de l’instance gérée](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Configuration du routage

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure. Cliquez sur la page **Table de routage** > **Créer sur la table de routage**.
2. Dans **Créer la table de routage** > **Nom**, spécifiez un nom pour la table.
3. Sélectionnez votre abonnement, votre groupe de ressources et l’emplacement. Laissez BGP désactivé, et cliquez sur **Créer**.
    ![Table de routage](media/migrate-scenarios-lift-and-shift/route-table.png)

4. Une fois la table de routage créée, ouvrez-la puis cliquez sur **Itinéraires** > **+Ajouter**.
5. Dans **Ajouter un itinéraire** > **Nom**, spécifiez un nom d’itinéraire.
6. Dans **Préfixe d’adresse**, saisissez 0.0.0.0/0.
7. Dans **Type de tronçon suivant**, sélectionnez **Internet**. Cliquez ensuite sur **OK**.

     ![Table de routage](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Appliquer le routage au sous-réseau de l’instance gérée

1. Ouvrez le réseau virtuel que vous avez créé. Cliquez sur **Sous-réseaux** > nom-du-sous-réseau.
2. Cliquez sur **Table de routage** > nom-de-la-table. Cliquez ensuite sur **Enregistrer**.

     ![Table de routage](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Créer une instance gérée

1. Cliquez sur **Créer une ressource**, localisez **Managed Instance** puis sélectionnez **Azure SQL Database Managed Instance (préversion)**.
2. Cliquez sur **Créer**.
3. Dans **SQL Managed Instance**, sélectionnez votre abonnement, puis vérifiez que les **Conditions de préversion** sont **Acceptées**.
4. Dans **Nom de l’instance gérée**, spécifiez un nom. 
5. Spécifiez un nom d’utilisateur et un mot de passe en tant qu’administrateur pour l’instance.
6. Sélectionnez votre groupe de ressource, l’emplacement et le réseau virtuel de l’instance.
7. Cliquez sur **Niveau tarifaire** pour dimensionner le calcul et le stockage. Par défaut, l’instance obtient 32 Go d’espace de stockage gratuit (avril 2018).
8. Spécifiez le stockage et le nombre de cœurs virtuels.
9. Cliquez sur **Appliquer** pour enregistrer les paramètres, et sur **Créer** pour déployer l’instance gérée. Pour afficher l’état du développement, cliquez sur **Notifications** puis sur **Déploiement en cours**.

    ![Instance gérée](media/migrate-scenarios-lift-and-shift/mi-1.png)

Une fois l’instance gérée déployée, deux nouvelles ressources apparaissent dans le groupe de ressources ContosoRG : le cluster virtuel pour les instances gérées, et l’instance SQL Managed Instance. Les deux sont situés à l’emplacement Est des États-Unis 2.

![Instance gérée](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>Étape 3 : obtention d’une SAP URI pour le service DMS

Obtenez une SAP URI pour que le service DMS puisse accéder au conteneur de votre compte de stockage, pour charger les fichiers de sauvegarde utilisés pour migrer les bases de données vers Azure SQL Database Managed Instance. 

1. Ouvrez l’Explorateur de stockage (version préliminaire).
2. Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob pour lequel vous souhaitez obtenir une SAP. Développez le dossier **Conteneurs d’objets blob**du compte de stockage.
3. Cliquez avec le bouton droit sur le conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. Dans **Signature d’accès partagé**, spécifiez la stratégie, les dates de début et d’expiration, le fuseau horaire et les niveaux d’accès de la ressource. Cliquez ensuite sur **Créer**.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. Vous accédez alors à une deuxième boîte de dialogue  dans laquelle vous pouvez visualiser le conteneur d’objets blob, ainsi que les URL et les chaînes de requête que vous pouvez utiliser pour accéder à la ressource de stockage. Sélectionnez **Copier** pour copier les valeurs. Conservez-les en lieu sûr. Vous en aurez besoin pour configurer le service DMS.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>Étape 4 : préparation du service DMS

Vous inscrivez le fournisseur de migration de base de données puis créez une instance.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

1. Sur votre abonnement, sélectionnez **Fournisseurs de ressources**. 
2. Recherchez « migration », puis à droite de Microsoft.DataMigration, sélectionnez **Inscrire**. 


### <a name="create-an-instance"></a>Créer une instance

1. Sélectionnez **+ Créer une ressource**, recherchez « Azure Database Migration Service », puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.
2. Dans l’écran Azure Database Migration Service (version préliminaire), sélectionnez **Créer**.
3. Spécifiez un nom pour le service, l’abonnement, le groupe de ressources, le réseau virtuel et le niveau tarifaire.
4. Sélectionnez **Créer** pour créer le service.

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>Étape 5 : préparation d’Azure pour le service Site Recovery

### <a name="set-up-a-virtual-network"></a>Configuration d’un réseau virtuel

Il vous faut un réseau Azure dans lequel les machines virtuelles Azure seront placées à leur création après basculement, ainsi qu’un compte de stockage Azure dans lequel les données répliquées sont stockées.

- Dans le cadre de ce scénario, nous utiliserons le réseau Azure que nous avons créé précédemment pour le service DMS.
- Remarque : le réseau que vous utilisez et le coffre Site Recovery doivent se trouver dans la même région.


### <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

Site Recovery réplique les données de machine virtuelle dans le stockage Azure. Les machines virtuelles Azure sont créées à partir du stockage quand vous basculez du site local vers Azure.

1. Dans le menu [Portail Azure](https://portal.azure.com), sélectionnez **Nouveau** > **Stockage** > **Compte de stockage**.
2. Dans **Créer un compte de stockage**, entrez un nom correspondant au compte. Pour ces didacticiels, utilisez le nom **contosovmsacct1910171607**. Le nom doit être unique dans Azure, avoir entre 3 et 24 caractères, et contenir uniquement des nombres et des lettres minuscules.
3. Dans **Modèle de déploiement**, sélectionnez **Resource Manager**.
4. Dans **Type de compte**, sélectionnez **Usage général**. Dans **Performances**, sélectionnez **Standard**. Ne sélectionnez pas Stockage Blob.
5. Dans **Réplication**, sélectionnez la valeur par défaut **Stockage géo-redondant avec accès en lecture** pour la redondance de stockage.
6. Dans **Abonnement**, sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.
7. Dans **Groupe de ressources**, entrez un nouveau groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Pour ces didacticiels, utilisez le nom **ContosoRG**.
8. Dans **Emplacement**, sélectionnez un emplacement géographique pour votre compte de stockage. Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services. Dans ce scénario, nous utilisons la région **Est des États-Unis 2**.

   ![Créez un compte de stockage.](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Sélectionnez **Créer** pour créer le compte de stockage.

### <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Surveillance + Gestion** > **Backup and Site Recovery**.
2. Dans **Name**, entrez un nom convivial pour identifier le coffre. Pour ce didacticiel, utilisez **ContosoVMVault**.
3. Dans **Groupe de ressources**, sélectionnez le groupe de ressources existant nommé **contosoRG**.
4. Dans **Emplacement**, saisissez la région Azure **Est des États-Unis 2**.
5. Pour accéder rapidement au coffre à partir du tableau de bord, sélectionnez **Épingler au tableau de bord** > **Créer**.
Le nouveau coffre apparaît dans **Tableau de bord** > **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>Étape 6 : préparation de VMware local pour Site Recovery

Afin de préparer VMware pour Site Recovery, voici ce qu’il faut faire :

- Préparer un compte sur le serveur vCenter ou sur l’hôte vSphere ESXi pour automatiser la détection de machines virtuelles
- Préparer un compte pour l’installation automatique du service Mobilité sur les machines virtuelles VMware
- Préparez les machines virtuelles locales si vous voulez vous connecter aux machines virtuelles Azure après basculement.


### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. Au moins un compte en lecture seule est nécessaire.
- Orchestrer la réplication, le basculement et la restauration automatique. Il vous faut un compte qui peut exécuter des opérations telles que la création et la suppression de disques, et l’allumage de machines virtuelles.

Créez le compte comme suit :

1. Pour utiliser un compte dédié, créez un rôle au niveau du vCenter. Donnez un nom au rôle, tel que **Azure_Site_Recovery**.
2. Attribuez au rôle les autorisations résumées dans le tableau ci-dessous.
3. Créez un utilisateur sur le serveur vCenter ou sur l’hôte vSphere. Affectez le rôle à l’utilisateur.

**Tâche** | **Rôle/Autorisations** | **Détails**
--- | --- | ---
**Détection des machines virtuelles** | Au moins un utilisateur en lecture seule<br/><br/> Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).
**Réplication complète, basculement, restauration automatique** |  Créez un rôle (Azure_Site_Recovery) avec les autorisations nécessaires, puis attribuez le rôle à un utilisateur ou à un groupe d’utilisateurs VMware<br/><br/> Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur la machine virtuelle que vous souhaitez répliquer.

- Site Recovery peut faire une installation automatique par push de ce composant, lorsque vous activez la réplication pour la machine virtuelle.
- Pour une installation automatique par push, vous devez préparer un compte qui sera utilisé par Site Recovery pour accéder à la machine virtuelle.
- Vous spécifiez ce compte quand vous configurez la réplication dans la console d’Azure.
- Il vous faut un domaine ou un compte local avec les autorisations nécessaires pour l’installation sur la machine virtuelle.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Après basculement vers Azure, imaginons que vous souhaitiez vous connecter à des machines virtuelles répliquées dans Azure à partir de votre réseau local.

Pour vous connecter à des machines virtuelles Windows à l’aide de RDP après le basculement, effectuez les opérations suivantes :

1. Pour accéder via Internet, activez RDP sur la machine virtuelle locale avant le basculement. Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public** et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.
2. Pour accéder via un VPN de site à site, activez RDP sur la machine locale. RDP doit être autorisé dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**.
3. Vérifiez que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [Plus d’informations](https://support.microsoft.com/kb/3031135)
4. Aucune mise à jour de Windows ne doit être en attente sur la machine virtuelle quand vous déclenchez un basculement. S’il y en a, vous ne pouvez pas vous connecter à la machine virtuelle avant la fin de la mise à jour.
5. Sur la machine virtuelle Azure Windows, après le basculement, vérifiez les **Diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle. Si vous ne pouvez pas vous connecter, vérifiez que la machine virtuelle est en cours d’exécution et lisez ces [conseils de résolution des problèmes](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>Étape 7 : réplication des machines virtuelles avec Site Recovery

### <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication

1. Dans **Coffres Recovery Services**, sélectionnez le nom du coffre, **ContosoVMVault**.
2. Dans **Prise en main**, sélectionnez Site Recovery. Sélectionnez ensuite **Préparer l’infrastructure**.
3. Dans **Objectif de protection** > **Où se trouvent vos machines**, sélectionnez **Local**.
4. Dans **Où voulez-vous répliquer vos machines**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Oui, avec l’hyperviseur VMware vSphere**. Sélectionnez ensuite **OK**.

    ![Modèle OVF](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

Dans **Planification d’un déploiement**, cliquez sur **Oui, je l’ai fait** dans la liste déroulante.

### <a name="set-up-the-source-environment"></a>Configurer l’environnement source


Pour configurer un environnement source, vous avez besoin d’une seule machine locale à haut niveau de disponibilité pour héberger les composants Site Recovery locaux. Ces composants englobent le serveur de configuration et le serveur de processus.

- Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.
- Le serveur de processus fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.
- De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.


Pour déployer le serveur de configuration en tant que machine virtuelle VMware hautement disponible :

- Téléchargez un modèle OVF (Open Virtualization Format).
- Importez le modèle dans VMware pour créer la machine virtuelle.
- Configurez le serveur de configuration, puis inscrivez-le dans le coffre. 

Après l’inscription, Site Recovery détecte les machines virtuelles VMware locales.



#### <a name="download-the-vm-template"></a>Télécharger le modèle de machine virtuelle

1. Dans le coffre, allez dans **Préparer l’infrastructure** > **Source**.
2. Dans **Préparer la source**, sélectionnez **+Serveur de configuration**.

    ![Télécharger un modèle](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration pour VMware** s’affiche dans **Type de serveur**.
2. Téléchargez le modèle OVF pour le serveur de configuration.

    ![Télécharger le modèle OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  Vous pouvez télécharger la dernière version du modèle de serveur de configuration directement à partir du [Centre de téléchargement Microsoft](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>Importer le modèle dans VMware

1. Connectez-vous au serveur VMware vCenter à l’aide du client VMware vSphere.
2. Dans le menu **Fichier**, sélectionnez **Déployer le modèle OVF** pour démarrer l’Assistant Déploiement du modèle OVF. 

     ![Modèle OVF](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. Dans **Sélectionner une source**, entrez l’emplacement du modèle OVF téléchargé.
4. Dans **Examiner les détails**, sélectionnez **Suivant**.
5. Dans **Sélectionner le nom et le dossier** et **Sélectionner la configuration**, acceptez les paramètres par défaut.
6. Dans **Sélectionner un stockage**, pour des performances optimales, sélectionnez **Remise à zéro rapide d’un approvisionnement important** dans **Sélectionner le format de disque virtuel**.
7. Dans le reste des pages de l’Assistant, acceptez les paramètres par défaut.
8. Dans **Prêt à finaliser** :

    * Pour configurer la machine virtuelle avec les paramètres par défaut, sélectionnez **Mettre sous tension après le déploiement** > **Terminer**.

    * Si vous souhaitez ajouter une interface réseau supplémentaire, décochez la case **Mettre sous tension après le déploiement**. Sélectionnez ensuite **Terminer**. Par défaut, le modèle de serveur de configuration est déployé avec une seule carte d’interface réseau, mais vous pouvez en ajouter d’autres après le déploiement.



#### <a name="register-the-configuration-server"></a>Inscrire le serveur de configuration 

1. À partir de la console du client vSphere de VMware, activez la machine virtuelle.
2. La machine virtuelle démarre sur une expérience d’installation de Windows Server 2016. Acceptez le contrat de licence et entrez un mot de passe administrateur.
3. Une fois l’installation terminée, connectez-vous à la machine virtuelle en tant qu’administrateur.
4. L’outil de configuration d’Azure Site Recovery démarre la première fois que vous vous connectez.
5. Saisissez un nom utilisé pour inscrire le serveur de configuration sur Site Recovery. Sélectionnez ensuite **Suivant**.

    ![Modèle OVF](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, sélectionnez **Connecter** pour vous connecter à votre abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.

    ![Modèle OVF](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. L’outil effectue des tâches de configuration, puis redémarre.
8. Reconnectez-vous à la machine. L’Assistant Gestion de serveur de configuration démarre automatiquement.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Configurer les paramètres et ajouter le serveur VMware

1. Dans l’Assistant Gestion de serveur de configuration, sélectionnez **Configurer la connectivité**, puis la carte d’interface réseau qui recevra le trafic de réplication. Ensuite, sélectionnez **Enregistrer**. Vous ne pouvez pas modifier ce paramètre une fois qu’il a été configuré.
2. Dans **Sélectionner le coffre Recovery Services**, sélectionnez votre abonnement Azure ainsi que le groupe de ressources et le coffre appropriés.

    ![coffre](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. Dans **Installer le logiciel tiers**, acceptez le contrat de licence. Sélectionnez **Télécharger et installer** pour installer MySQL Server.
4. Sélectionnez **Installer VMware PowerCLI**. Assurez-vous que toutes les fenêtres du navigateur sont fermées avant de continuer. Sélectionnez **Continuer**.
5. Dans **Valider la configuration de l’appliance**, les conditions préalables sont vérifiées avant que vous ne poursuiviez.
6. Dans **Configurer le serveur vCenter Server/vSphere ESXi**, entrez le nom de domaine complet ou l’adresse IP du serveur vCenter ou de l’hôte vSphere, sur lequel sont situées les machines virtuelles que vous souhaitez répliquer. Entrez le numéro de port sur lequel le serveur écoute. Entrez un nom convivial à utiliser pour le serveur VMware dans le coffre.
7. Entrez les informations d’identification à utiliser par le serveur de configuration pour se connecter au serveur VMware. Site Recovery utilise ces informations d’identification pour détecter automatiquement les machines virtuelles VMware disponibles pour la réplication. Sélectionnez **Ajouter**, puis **Continuer**.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. Dans **Configurer les informations d’identification de la machine virtuelle**, entrez le nom d’utilisateur et le mot de passe à utiliser pour installer automatiquement le service Mobilité sur les machines, une fois la réplication activée. Pour les machines Windows, le compte doit disposer des privilèges d’administrateur local sur les machines que vous souhaitez répliquer. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Sélectionnez **Finaliser la configuration** pour terminer l’inscription. 
8. Une fois l’inscription terminée, dans le portail Azure, vérifiez que le serveur de configuration et le serveur VMware sont répertoriés sur la page **Source** dans le coffre. Sélectionnez ensuite **OK** pour configurer les paramètres cibles.
9. Site Recovery se connecte aux serveurs VMware en utilisant les paramètres spécifiés et découvre les machines virtuelles.

> [!NOTE]
> L’affichage du nom de compte dans le portail peut prendre plus de 15 minutes. Pour procéder à une mise à jour immédiate, sélectionnez **Serveurs de configuration** > ***nom du serveur*** > **Actualiser le serveur**.

### <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Dans **Préparer l’infrastructure** > **Cible**, sélectionnez l’abonnement Azure à utiliser.
2. Pour le modèle de déploiement cible, sélectionnez **Azure Resource Manager**.

3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.


### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

1. Dans **Préparer l’infrastructure** > **Paramètres de réplication** > **Stratégie de réplication**, cliquez sur **Créer et associer**.
1. Dans **Créer une stratégie de réplication**, entrez le nom de stratégie **VMwareRepPolicy**.
2. Dans **Seuil RPO**, utilisez la valeur par défaut de 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
3. Dans **Rétention des points de récupération**, utilisez la valeur par défaut de 24 heures pour la durée de la fenêtre de conservation pour chaque point de récupération. Pour ce didacticiel, utilisez la valeur 72 heures. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
4. Dans **Fréquence des captures instantanées de cohérence d’application**, utilisez la valeur par défaut de 60 minutes pour la fréquence de création des captures instantanées de cohérence d’application. Sélectionnez **OK** pour créer la stratégie.

    ![Créer une stratégie de réplication](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. La stratégie est automatiquement associée au serveur de configuration. 

    ![Associer la stratégie de réplication](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>Activer la réplication

Démarrez à présent la réplication des machines virtuelles. Site Recovery installe le service Mobilité sur chaque machine virtuelle quand vous activez la réplication. 


1. Sélectionnez **Répliquer l’application** > **Source**.
2. Dans **Source**, sélectionnez le serveur de configuration.
3. Dans **Type de machine**, sélectionnez **Machines virtuelles**.
4. Dans la zone **Hyperviseur vCenter/vSphere**, sélectionnez le serveur vCenter qui gère l’hôte vSphere, ou sélectionnez l’hôte.
5. Sélectionnez le serveur de processus (serveur de configuration). Sélectionnez ensuite **OK**.

    ![Activer la réplication](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. Dans **Cible**, sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles basculées. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou Resource Manager) pour les machines virtuelles basculées.
2. Sélectionnez le compte de stockage Azure que vous souhaitez utiliser pour répliquer les données.
3. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement.
4. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer le paramètre réseau à l’ensemble des machines que vous sélectionnez à des fins de protection. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine.
5. Sélectionnez le sous-réseau dans le réseau virtuel. Sélectionnez ensuite **OK**.

    ![Activer la réplication](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, sélectionnez la machine virtuelle (WEBVM) que vous voulez. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. 

    ![Activer la réplication](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. Pour surveiller les machines virtuelles que vous ajoutez, consultez l’heure de la dernière découverte des machines virtuelles dans **Serveurs de configuration** > **Dernier contact à**. Pour ajouter des machines virtuelles sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (sans le sélectionner) et sélectionnez **Actualiser**. Il peut être nécessaire d’attendre 15 minutes ou plus avant que les modifications prennent effet et qu’elles apparaissent dans le portail.
8. Dans **Propriétés** > **Configurer les propriétés**, sélectionnez le compte que le serveur de processus doit utiliser pour installer automatiquement le service Mobilité sur la machine. 

    ![Activer la réplication](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée.
10. Sélectionnez **Activer la réplication**.

    ![Activer la réplication](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Suivez la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. 
12. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement et apparaît dans **Vue d’ensemble** > **Essentiels**.

    ![Essentials](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>Étape 8 : migration de la base de données avec le service DMS

Créez un projet DMS et procédez à la migration.


### <a name="create-a-database-migration-project"></a>Créer un projet Database Migration

1. Localisez la ressource DMS dans le portail Azure et ouvrez-la.
2. Sélectionnez **+ Nouveau projet de migration**.
3. Dans **Nouveau projet de migration**, spécifiez un nom pour le projet.
4. Dans **Type du serveur source**, sélectionnez **SQL Server**. Dans **Type de serveur cible**, sélectionnez **Azure SQL Database Managed Instance**.
5. Cliquez sur **Créer** pour créer le projet.
6. Dans **Détails de la source**, spécifiez les détails de connexion de SQL Server source local. Cliquez sur **Enregistrer**.
7. Dans **Détails de la cible**, spécifiez les détails de connexion de la cible, à savoir l’instance préprovisionnée d’Azure SQL Database Managed Instance vers laquelle la base de données locale doit être migrée. Cliquez ensuite sur **Enregistrer**.
8. Dans **Récapitulatif du projet**, consultez et vérifiez les détails associés au projet de migration.

    ![Projet DMS](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Migrer la base de données

1. Après la création du projet, l’assistant Migration s’affiche.
2. Spécifiez les informations d’identification pour les serveurs source et cible, puis cliquez sur **Enregistrer**. L’Assistant tente de se connecter au serveur SQL Server local.

    ![Assistant DSM](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. Dans **Mapper aux bases de données cibles**, sélectionnez les bases de données sources que vous souhaitez migrer. Cliquez ensuite sur **Enregistrer**.

    ![Assistant DSM](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. Dans **Détails de la cible**, sélectionnez **Je connais les détails de ma cible**. Renseignez le nom DNS de vos instances SQL Managed Instances, ainsi que les informations d’identification. Cliquez ensuite sur **Enregistrer**.

    ![Assistant DSM](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. Dans le projet enregistré, sélectionnez **+Nouvelle activité**. Sélectionnez ensuite **Exécuter la migration**.
6. Lorsque vous y êtes invité, entrez les informations d’identification des serveurs source et cible. Cliquez ensuite sur **Enregistrer**.
7. Dans **Mapper aux bases de données cibles**, sélectionnez les bases de données sources que vous souhaitez migrer. Cliquez ensuite sur **Enregistrer**
8. Dans **Configurer les paramètres de migration** > **Emplacement du serveur de sauvegarde**, spécifiez le partage réseau que vous avez créé sur la machine locale. DMS emmène les sauvegardes source dans ce partage.  N’oubliez pas que le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges d’écriture pour ce partage réseau.
9. Spécifiez le nom d’utilisateur et mot de passe que le service DMS peut emprunter pour le chargement des fichiers de sauvegarde dans le conteneur de stockage Azure, pour la restauration.
10. Spécifiez la SAP URI qui permet au service DMS d’accéder au conteneur de compte de stockage dans lequel le service charge les fichiers de sauvegarde et qui est utilisé pour la migration sur Azure SQL Database Managed Instance.  Cliquez ensuite sur **Enregistrer**.
11. Dans **Résumé de la migration**, spécifiez un nom pour l’activité de migration > **Exécutez la migration**.
12. Dans le projet > **Vue d’ensemble**, surveillez l’état de la migration. Une fois la migration terminée, vérifiez que les bases de données cible existent sur l’instance gérée.


## <a name="step-9-migrate-vms-with-site-recovery"></a>Étape 9 : migration des machines virtuelles avec Site Recovery

Nous vous conseillons d’exécuter un basculement de test pour vous assurer que tout fonctionne comme prévu, avant d’exécuter une migration complète. Quand vous effectuez un test de basculement, voici ce qui se produit :

1. Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour la migration sont en place.
2. Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
3. Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.

### <a name="run-a-test-failover"></a>Exécuter un test de basculement

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** > Machine virtuelle.
2. Dans les propriétés de la machine virtuelle, cliquez sur **+Basculement de test**.

    ![Test de basculement](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Sélectionnez **Dernier traitement** pour basculer la machine virtuelle vers le dernier moment disponible. L’horodatage est affiché. Cette option, avec laquelle aucun temps n’est passé à traiter les données, offre un objectif de délai de récupération faible.
2. Dans **Tester le basculement**, sélectionnez le réseau Azure cible auquel les machines virtuelles Azure seront connectées après le basculement.
3. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression dans le coffre > **Paramètres** > **Travaux** > **Basculement de test**.
4. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure > **Machines virtuelles**. Vérifiez que la machine virtuelle est de la taille appropriée, qu’elle est connectée au réseau approprié et qu’elle est en cours d’exécution. Vous devriez à présent pouvoir vous connecter à la machine virtuelle répliquée dans Azure.
5. Pour supprimer les machines virtuelles créées lors du test de basculement, cliquez sur **Nettoyer le test de basculement** sur la machine virtuelle. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement.

### <a name="migrate-the-machines"></a>Migrer les machines

Une fois que vous avez vérifié que le basculement de test fonctionne comme prévu, créez un plan de récupération à migrer vers Azure.

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Dans le coffre, sélectionnez **Plans de récupération (Site Recovery)** > **+Plan de récupération**.
2. Dans **Créer un plan de récupération**, spécifiez un nom pour le plan.
3. Choisissez le serveur de configuration source et Azure en tant que cible. Sélectionnez **Resource Manager** comme modèle de déploiement. Le basculement et la récupération doivent être activés sur les ordinateurs de l’emplacement source. 
4. Dans **Sélectionner les éléments**, ajoutez les machines (WEBVM) au plan. Cliquez ensuite sur **OK**.
5. Cliquez sur **OK** pour créer le plan.

    ![Plan de récupération](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Exécuter un basculement

1. Dans **Plans de récupération**, cliquez sur le plan > **Basculement**.
2. Dans **Basculement** > **Point de récupération**, sélectionnez le point de récupération le plus récent.
3. Le paramètre de clé de chiffrement ne s’applique pas à ce scénario.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tenter d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.

    ![Basculement](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Vérifiez que la machine virtuelle Azure s’affiche dans Azure comme prévu.

    ![Basculement](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Terminer la migration**. Cette opération termine le processus de migration, interrompt la réplication pour la machine virtuelle et arrête la facturation Site Recovery pour la machine virtuelle.

    ![Basculement](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Mettre à jour la chaîne de connexion

La dernière étape du processus de migration est la mise à jour de la chaîne de connexion de l’application pour pointer vers la base de données migrée qui est exécutée sur votre instance SQL Managed Instance.

1. Trouvez la nouvelle chaîne de connexion dans le portail Azure en cliquant sur **Paramètres** > **Chaînes de connexion**.

    ![Basculement](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. Vous devez mettre à jour cette chaîne avec le nom d’utilisateur et le mot de passe de votre instance SQL MI.
3. Une fois la chaîne configurée, vous devez remplacer la chaîne de connexion actuelle dans le fichier web.config de votre application.
4. Après avoir mis à jour et enregistré le fichier, redémarrez IIS sur la machine WEBVM. Vous pouvez faire ceci à l’aide de la commande IISRESET /RESTART depuis une invite de commande.
5. Après avoir redémarré IIS, votre application utilisera maintenant la base de données exécutée sur votre instance SQL MI.
6. À ce stade, la machine virtuelle locale SQLVM peut être arrêtée, et la migration est terminée.



## <a name="conclusion"></a>Conclusion

Dans ce scénario, nous avons :

> [!div class="checklist"]
> * Nous venons de migrer notre base de données locale vers une instance Azure SQL Managed Instance avec le service DMS.
> * Nous venons de migrer nos machines virtuelles locales vers des machines virtuelles Azure, avec le service Azure Site Recovery.
