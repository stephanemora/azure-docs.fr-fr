---
title: Architecture Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble du service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810104"
---
# <a name="azure-migrate-architecture-and-processes"></a>Architecture et processus Azure Migrate

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres de fournisseurs de logiciels indépendants tiers. 

 Cet article résume l'architecture et les processus d'évaluation et de migration pour les outils Évaluation de serveur Azure Migrate et Migration de serveur Azure Migrate.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Évaluation avec l’outil Évaluation de serveur Azure Migrate

L’outil Évaluation de serveur Azure Migrate Server peut accéder aux machines virtuelles VMware et Hyper-V en vue de leur migration vers Azure. L’évaluation fonctionne de la manière suivante :

1. **Préparer pour l’évaluation** : En local, vous configurez une appliance Azure Migrate légère en tant que machine virtuelle VMware ou Hyper-V, puis enregistrez l'appliance auprès d’Azure Migrate.
2. **Détection des machines virtuelles** : L’appliance Azure Migrate est exécutée pour découvrir les machines virtuelles locales. 
    - Rien ne doit explicitement être installé sur les machines virtuelles découvertes.
    - Les métadonnées de machines virtuelles incluent des informations sur les cœurs, la mémoire, les disques, les tailles de disque et les cartes réseau.
    - Les données de performances incluent des informations sur l’utilisation du processeur et de la mémoire, les IOPS du disque, le débit de disque (Mo/s) et la sortie du réseau (Mo/s)
- **Collecter et évaluer des machines** : Une fois la découverte terminée, dans le portail Azure, vous collectez les machines virtuelles découvertes en groupes qui se composent généralement des machines virtuelles que vous souhaitez migrer ensemble. Vous exécutez une évaluation sur un groupe.


## <a name="vmware-assessment"></a>Évaluation VMware 

Le diagramme résume le fonctionnement de l'évaluation de machines virtuelles VMware avec l’outil Évaluation de serveur Azure Migrate.

![Architecture pour l’évaluation VMware](./media/migrate-architecture/sas-architecture-vmware.png)

Pour ce faire, procédez comme suit :

1. **Déployez l’appliance Azure Migrate** :

    a. Téléchargez le modèle (OVA) à partir du portail Azure.

    b. Importez-le sur la machine vCenter Server pour créer la machine virtuelle.

    c. Connectez-vous à cette machine virtuelle, configurez ses paramètres de base, puis enregistrez-la auprès d’Azure Migrate.

2. **Lancez la découverte** :

    a. Connectez-vous à l'application Collector en cours d'exécution sur l'appliance pour lancer la découverte.

    b. L'appliance envoie continuellement des métadonnées et des données de performances depuis les machines virtuelles vers Azure.

    - L'appliance est toujours connectée au service Azure Migrate.
    - Les changements d'environnement survenus au cours d'une découverte continue sont capturés. Par exemple, l’ajout de machines virtuelles dans l’étendue de la découverte, ou l’ajout de disques de machines virtuelles ou de cartes réseau.

3. **Évaluez les machines** :

    a. Une fois la découverte terminée, vous regroupez les machines virtuelles découvertes dans le portail Azure.  Un groupe est généralement constitué des machines virtuelles que vous souhaitez migrer ensemble.

    b. Exécutez une évaluation pour chaque groupe.

4. **Passez en revue l’évaluation** : 

    a. Une fois l’évaluation terminée, visualisez-la dans le portail.

    b. Pour une analyse plus approfondie, téléchargez l'évaluation au format Excel.



### <a name="vmware-ports"></a>Ports VMware
Azure Migrate utilise les ports de connexion suivants pour VMware :

**Source** | **Cible** | **Port** | **Détails**
--- | --- | --- | ---
Appliance Azure Migrate | Service Azure Migrate | Target-TCP 443 | Envoyez des métadonnées et des données de performances à Azure Migrate.
Appliance Azure Migrate | Serveur vCenter | Target-TCP 443 | Connectez-vous à vCenter Server pour les métadonnées et les données de performance. 443 est le port par défaut, mais il peut être modifié afin que vCenter écoute sur un port différent. 
Client RDP | Appliance Azure Migrate | Target-TCP3389 | Connexion RDP pour déclencher la découverte depuis l'appliance.

### <a name="collected-vmware-metadata"></a>Métadonnées VMware collectées

L'appliance envoie des métadonnées et des données de performances depuis les machines virtuelles vers Azure.

**Action** | **Détails**
--- | ---
**Métadonnées collectées** | Nom de la machine virtuelle vCenter<br/> Chemin d'accès à la machine virtuelle vCenter (hôte/dossier du cluster)<br/> Adresses IP et MAC<br/> Système d’exploitation<br/> Nombre de cœurs/disques/cartes réseau<br/> Taille de la mémoire et du disque.
**Données de performances collectées** | Utilisation du processeur/de la mémoire<br/> Données par disque (débit de lecture/écriture du disque ; lectures/écritures du disque par seconde)<br/> Données de la carte réseau (réseau entrant, réseau sortant).<br/><br/> Les données de performances sont collectées en continu après la connexion de l'appliance à vCenter Server. Les données d’historique ne sont pas collectées.

## <a name="hyper-v-assessment"></a>Évaluation Hyper-V

Le diagramme résume le fonctionnement de l'évaluation Hyper-V avec l’outil Évaluation de serveur Azure Migrate.

![Architecture pour l’évaluation Hyper-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

Pour ce faire, procédez comme suit :

1. **Créez l’appliance Azure Migrate** :

    a. Téléchargez la machine virtuelle au format compressé à partir du portail Azure.

    b. Importez-la sur un hôte Hyper-V.

    c. Connectez-vous à la machine virtuelle de l’appliance. Configurez ses paramètres de base, puis enregistrez-la auprès d’Azure Migrate.

2. **Lancez la découverte** :

    a. Connectez-vous à l'appliance Azure Migrate pour lancer la découverte. Rien ne doit explicitement être installé sur les machines virtuelles découvertes.

    b. L'appliance envoie continuellement les métadonnées et les données de performances des machines virtuelles vers Azure Migrate.

    - L'appliance toujours connectée au service Azure Migrate (via des sessions CIM).
    - Les changements d'environnement survenus au cours d'une découverte continue sont capturés. Par exemple, l’ajout de machines virtuelles dans l’étendue de la découverte, ou l’ajout de disques de machines virtuelles ou de cartes réseau.


3. **Évaluez les machines** :

    a. Une fois la découverte terminée, vous regroupez les machines virtuelles découvertes dans le portail Azure.  Un groupe est généralement constitué des machines virtuelles que vous souhaitez migrer ensemble.

    b. Exécutez une évaluation pour chaque groupe.

4. **Passez en revue l’évaluation** :

    a. Une fois l’évaluation terminée, visualisez-la dans le portail.

    b. Pour une analyse plus approfondie, téléchargez l'évaluation au format Excel.

### <a name="hyper-v-ports"></a>Ports Hyper-V

Le service Azure Migrate utilise les ports de connexion suivants pour Hyper-V :

**Source** | **Cible** | **Port** | **Détails**
--- | --- | --- | ---
Appliance Azure Migrate | Service Azure Migrate | Target-TCP 443 | Envoyez des métadonnées et des données de performances à Azure Migrate.
Appliance Azure Migrate | Hôte/cluster Hyper-V | Target-Default WinRM ports-HTTP:5985; HTTPS:5986 | Connectez-vous à l'hôte pour les métadonnées et les données de performances. Session CIM utilisée pour la connexion
Client RDP | Appliance Azure Migrate | Target-TCP3389 | Connexion RDP pour déclencher la découverte depuis l'appliance.

## <a name="collected-hyper-v-vm-metadata"></a>Métadonnées de machines virtuelles Hyper-V collectées

L'appliance envoie des métadonnées et des données de performances depuis les machines virtuelles vers Azure.


**Action** | **Détails**
--- | ---
**Métadonnées collectées** | nom de la machine virtuelle<br/> Chemin d'accès à la machine virtuelle (hôte/dossier du cluster)<br/> Adresses IP et MAC<br/> Système d’exploitation<br/> Nombre de cœurs/disques/cartes réseau<br/> Taille de la mémoire et du disque<br/> IOPS du disque, débit du disque (Mo/s), débit du réseau (Mo/s)
**Données de performances collectées** |  Utilisation du processeur/de la mémoire<br/> Données par disque (débit de lecture/écriture du disque ; lectures/écritures du disque par seconde)<br/> Données de la carte réseau (réseau entrant, réseau sortant).<br/><br/> Les données de performances sont collectées en continu après la connexion de l'appliance à l'hôte Hyper-V. Les données d’historique ne sont pas collectées.




## <a name="migration-with-azure-migrate-server-migration"></a>Migration avec l’outil Migration de serveur Azure Migrate

En utilisant l’outil Migration de serveur Azure Migrate, vous pouvez migrer les éléments suivants vers Azure :

- Machines virtuelles VMware locales
- Machines virtuelles Hyper-V en local
- Serveurs physiques locaux
- Instances de machines virtuelles Windows AWS
- Instances de machines virtuelles Windows GCP

Le processus de migration diffère légèrement selon ce que vous migrez.


## <a name="migrate-vmware-vms"></a>Migrer des machines virtuelles VMware

La solution Migration de serveur Azure Migrate propose deux méthodes pour migrer des machines virtuelles VMware locales :

- **Réplication sans agent** : Migrez des machines virtuelles sans avoir à installer quoi que ce soit.
- **Réplication basée sur agent**. Installez un agent sur la machine virtuelle pour la réplication.

Bien que la réplication sans agent soit plus facile du point de vue du déploiement, elle présente actuellement un certain nombre de limitations. [En savoir plus](server-migrate-overview.md) sur ces limitations.
 

### <a name="agent-based-migration"></a>Migration basée sur agent

La migration de machines virtuelles VMware basée sur agent nécessite le déploiement d'un certain nombre de composants, comme indiqué dans le tableau.

**Composant** | **Prérequis** | **Détails**
--- | --- | ---
**Machine du serveur de configuration** | Une machine virtuelle VMware unique locale déployée à partir d'un modèle OVF téléchargé.<br/><br/> La machine exécute tous les composants locaux de Site Recovery, y compris le serveur de configuration et le serveur de processus. | **Serveur de configuration** : coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.<br/><br/> **Serveur de traitement**: Installé par défaut sur le serveur de configuration. Il reçoit les données de réplication, les optimise avec une mise en cache, une compression et un chiffrement, puis les envoie à Azure. De plus, le serveur de processus installe le service Mobility d’Azure Site Recovery sur les machines virtuelles, et effectue une découverte automatique sur les machines locales.
**Service de mobilité** | Le service Mobility doit être installé sur chaque machine virtuelle VMware que vous répliquez. | Nous vous recommandons d’autoriser l’installation automatique à partir du serveur de processus. Vous pouvez également installer le service manuellement, ou utiliser une méthode de déploiement automatisée, telle que System Center Configuration Manager.





### <a name="agent-based-replication-process"></a>Processus de réplication basée sur un agent

![Processus de réplication](./media/migrate-architecture/v2a-architecture-henry.png)

1. Lorsque vous activez la réplication pour une machine virtuelle, la réplication initiale vers Azure commence. 
    - La réplication se fait au niveau du bloc, presque en continu, à l’aide de l’agent du service Mobility en cours d’exécution sur la machine virtuelle.
    - Les paramètres de la stratégie de réplication sont appliqués :
        - **Seuil d’objectif de point de récupération**. Ce paramètre n’affecte pas la réplication. Il aide à la supervision. Un événement est déclenché, et un e-mail peut être envoyé, si le RPO en cours dépasse le seuil spécifié.
        - **Rétention des points de récupération**. Ce paramètre spécifie le moment auquel vous souhaitez revenir lors d’une interruption. La durée maximale de rétention sur le stockage premium est de 24 heures. Sur le stockage standard, elle est de 72 heures. 
        - **Instantanés de cohérence d’application**. Un instantané de cohérence d’application peut être pris toutes les 1 à 12 heures, en fonction de vos besoins applicatifs. Les instantanés sont des instantanés d’objet blob Azure standard. L’agent Mobilité en cours d’exécution sur une machine virtuelle demande un instantané VSS conformément à ce paramètre, et insère un signet à ce point dans le temps pour en faire un point de cohérence avec l’application dans le flux de réplication.

2. Le trafic est répliqué sur des points de terminaison publics de stockage Azure via Internet.

    - L’autre solution consiste à utiliser Azure ExpressRoute avec [appairage Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering).
    - La réplication du trafic à partir d’un site local vers Azure via un réseau VPN de site à site n’est pas prise en charge.
3. La réplication des modifications delta dans Azure commence à l’issue de la réplication initiale. Le suivi des modifications d’une machine est envoyé au serveur de traitement.
2. La communication s’effectue comme suit :

    - Les machines virtuelles communiquent avec le serveur de configuration local sur le port HTTPS 443 entrant, pour la gestion de la réplication.
    - Le serveur de configuration orchestre la réplication avec Azure sur le port HTTPS 443 sortant.
    - Les machines virtuelles envoient des données de réplication au serveur de traitement (s’exécutant sur l’ordinateur du serveur de configuration) sur le port HTTPS 9443 entrant. Ce port peut être modifié.
    - Le serveur de traitement reçoit les données de réplication, les optimise et les chiffre, puis les envoie au stockage Azure via le port 443 sortant.
5. Les données de réplication se trouvent tout d’abord dans un compte de stockage de cache dans Azure. Ces journaux sont traités et les données sont stockées dans un disque managé Azure (le disque seed Azure Site Recovery). Les points de récupération sont créés sur ce disque.







## <a name="next-steps"></a>Étapes suivantes

- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
- Pour obtenir de l’aide de la part de la communauté, visitez le [forum MSDN consacré à Azure Migrate](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) ou consultez la rubrique [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate).

