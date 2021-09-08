---
title: Déployer la reprise d’activité avec JetStream
description: Apprenez à déployer JetStream Disaster Recovery (DR) pour votre cloud privé Azure VMware Solution et les charges de travail VMware locales.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: a26cbaa4f8c37f3148a86005a637b006572685dc
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315121"
---
# <a name="deploy-disaster-recovery-using-jetstream"></a>Déployer la reprise d’activité avec JetStream

[JetStream Disaster Recovery (DR)](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) est installé dans un environnement VMware vSphere et managé via un dispositif de plug-in vCenter. Il fournit une protection continue des données (CDP) native du cloud, qui réplique en permanence les opérations d’E/S de machine virtuelle. Au lieu de capturer des instantanés à intervalles réguliers, il capture et réplique en continu les données au fur et à mesure de leur écriture dans le stockage principal, avec un impact minimal sur les applications en cours d’exécution.  Par conséquent, il vous permet de récupérer rapidement des machines virtuelles et leurs données, en atteignant un objectif de point de récupération inférieur.  

Avec Azure VMware Solution, vous pouvez stocker des données directement sur un cluster de récupération dans vSAN ou un système de fichiers attaché comme Azure NetApp Files. Les données sont capturées via des filtres d’E/S qui s’exécutent dans vSphere. Le magasin de données sous-jacent peut être VMFS, VSAN, vVol ou n’importe quelle plateforme HCI. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-disaster-recovery-diagram.png" alt-text="Diagramme montrant l’intégration de la solution JetStream DR et d’Azure VMware." border="false":::

Le logiciel JetStream DR comprend trois composants principaux : 

- L’appliance virtuelle du serveur d’administration est installée et configurée avant la protection de la récupération d’urgence. 

- L’appliance virtuelle de récupération d’urgence est une image .ISO déployée automatiquement par l’appliance virtuelle du serveur d’administration de JetStream DR.

- Composants hôtes (packages de filtre d’E/S) 

L’appliance virtuelle du serveur d’administration est utilisée pour installer et configurer des composants hôtes sur le cluster de calcul, puis pour administrer le logiciel JetStream DR. L’appliance virtuelle de récupération d’urgence exécute les composants de la récupération d’urgence du chemin d’accès aux données. Plusieurs appliances virtuelles de récupération d’urgence peuvent s’exécuter simultanément pour une meilleure évolutivité. Chaque appliance virtuelle de récupération d’urgence a une ou plusieurs partitions dédiées attachées en tant que numéro d’unité logique iSCSI ou en tant que VDISK de latence faible. Les partitions sont utilisées pour gérer les journaux de réplication et les référentiels pour les métadonnées persistantes.

Dans cet article, vous allez déployer et apprendre à utiliser JetStream DR dans votre cloud privé Azure VMware Solution et vos charges de travail VMware locales.


## <a name="supported-scenarios"></a>Scénarios pris en charge
En fonction des services de protection requis et du type de cloud privé protégé, vous pouvez déployer JetStream DR de deux manières :

- D'un serveur local vers le cloud

- De cloud à cloud

### <a name="on-premises-to-cloud-deployment"></a>Déploiement d’un emplacement local vers le cloud
Les machines virtuelles qui s’exécutent dans le centre de données VMware d’une organisation sont répliquées en continu sur Microsoft Azure. Par conséquent, les machines virtuelles peuvent reprendre l’opération dans Azure VMware Solution s’il existe un incident dans le centre de données local. Pendant que les machines virtuelles s’exécutent dans l’environnement de récupération, elles continuent de répliquer les données pour une protection continue. Après la restauration du centre de données local, les machines virtuelles et leurs données (y compris les nouvelles données générées par les machines virtuelles dans l’environnement de récupération) peuvent revenir à leur centre de données d’origine sans interruption.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Diagramme montrant le déploiement de JetStream depuis un emplacement local vers le cloud privé Azure VMware Solution." border="false":::

### <a name="cloud-to-cloud-deployment"></a>Déploiement du cloud vers le cloud

Dans cette configuration, Azure VMware Solution héberge votre environnement principal dans un centre de données. Il protège les machines virtuelles et les données en les répliquant en continu sur un autre cloud privé dans un autre de ses centres de données. En cas d’incident, les machines virtuelles et les données sont récupérées dans le deuxième centre de données. Cette protection peut être bidirectionnelle, avec le centre de données « A » protégeant « B », et vice-versa.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Diagramme montrant le déploiement de JetStream vers le cloud privé Azure VMware Solution." border="false":::


## <a name="prerequisites"></a>Prérequis

- Cloud privé de Azure VMware Solution déployé en tant que région secondaire. 

- Un serveur de rebond Ubuntu Linux avec une connexion ExpressRoute à votre cloud privé Azure VMware Solution. 

- Dernière version de PowerShell installée sur le serveur de rebond Linux.

- Dernier module tiers de PowerShell Gallery installé.

### <a name="protected-site"></a>Site protégé

Le site protégé héberge un **cluster de service** pour les services administratifs, tels que vCenter, DNS, Active Directory et les **clusters d’ordinateur** où s’exécutent des applications métier protégées. Le site protégé peut être situé à un emplacement local ou hébergé dans une solution Azure VMware.

Vous pouvez utiliser l’un des types suivants :

- Stockage Blob Azure

- Azure VMware Solution vSAN

- Système de fichiers joint à une solution Azure VMware, par exemple Azure NetApp Files


| **Item**  | **Description**  |  
| --- | --- |  
| **vCenter Server**  | <ul><li>Version prise en charge : 6.7</li><li>Port HTTPS : si vous utilisez un pare-feu, le port HTTPS 443 doit être ouvert.</li><li>Connectivité : le nom de domaine complet de l’appliance du serveur de gestion de récupération d’urgence JetStream doit être accessible à partir de vCenter. Sinon, l’installation du plug-in échoue.</li><li>Heure : les horloges MSA de vCenter et JetStream DR doivent être synchronisées.</li></ul>  |
| **Distributed Resource Scheduler (DRS)**  | Il est recommandé sur le cluster de calcul pour l’équilibrage des ressources.  |
| **Cluster**  | Hôtes vSphere : les machines virtuelles à protéger par JetStream DR doivent faire partie d’un cluster.  |
| **Hôte vSphere**  | <ul><li>Version prise en charge : 6.7U1 (build #10302608) ou version ultérieure</li><li>Connectivité : le nom de domaine complet de vCenter Server doit être accessible à partir de l’hôte. Dans le cas contraire, la configuration de l’hôte échoue.</li><li>Heure : les horloges MSA des hôtes vSphere et de JetStream DR doivent être synchronisées.</li><li>Service CIM : le serveur CIM doit être activé ; il s’agit du paramètre par défaut.</li></ul>  |
| **MSA JetStream DR**  | <ul><li>UC : 64 bits, 4 processeurs virtuels</li><li>Mémoire : 4 Go</li><li>Espace disque : 60 Go</li><li>Réseau : des adresses IP statiques ou attribuées dynamiquement (DHCP) peuvent être utilisées. Le nom de domaine complet doit être enregistré avec DNS.</li><li>DNS : résolution de noms DNS pour les hôtes vSphere et vCenter Server</li></ul>  |
| **JetStream DRVA**  | <ul><li>Processeur : 4 cœurs</li><li>Mémoire : 8 Go</li><li>Réseau : des adresses IP statiques ou attribuées dynamiquement (DHCP) peuvent être utilisées.</li></ul>  |
| **Magasin de journaux de réplication**  | Le site protégé doit exposer un appareil de stockage flash à faible latence, que les hôtes partagent dans le cluster pour des performances optimales. Cet appareil peut être contrôlé par le logiciel de récupération d’urgence JetStream ou fourni par un tiers. Il est utilisé en tant que référentiel pour le journal de réplication. Les hôtes DRVA et ESXi doivent avoir un accès direct à ce stockage via iSCSI.  |
| **Ports**  | Lorsque le logiciel JetStream DR est installé, une plage de ports s’ouvre automatiquement sur les hôtes ESXi source. Pour la plupart des utilisateurs, aucune action supplémentaire n’est donc nécessaire. Toutefois, dans les cas où la configuration locale/source a des règles de pare-feu spéciales bloquant ces ports, vous devez ouvrir ces ports manuellement.<br /><br />Plage de ports : 32873-32878  |



### <a name="recovery-site"></a>Site de récupération

Un cluster de solution Azure VMware _Pilot Light_ est établi pour la récupération du basculement. Bien que le site de récupération soit créé dans le cadre du processus d’installation, le cluster du site de récupération n’est pas entièrement rempli ou utilisé lors d’un fonctionnement normal. Les clusters de calcul basculés sont ajoutés au site de récupération à la demande en réponse à un événement d’incident.

### <a name="network"></a>Réseau

Un réseau ayant les caractéristiques suivantes doit être établi entre le site protégé et le site de récupération.

| **Éléments** | **Description** |
| --- | --- |
| **MSA JetStream DR**  | Un réseau de gestion est requis pour le MSA. Ce réseau est utilisé pour accéder aux API RESTful de JetStream DR et effectuer d’autres appels de chemin d’accès aux données. Si un réseau privé est disponible pour la connexion au magasin d’objets, ce réseau privé doit être ajouté à la machine virtuelle MSA en tant que réseau distinct. Si aucun réseau privé n’est disponible, assurez-vous que le réseau de gestion peut être utilisé pour se connecter au magasin d’objets. <br /><br />Un réseau externe dédié peut être utilisé pour l’accès au magasin d’objets ; dans le cas contraire, le trafic de données est envoyé via le réseau de gestion.  | 
| **JetStream DRVA**  | Si le seul réseau utilisé est le réseau de gestion, assurez-vous qu’il a accès au filtre d’E/S et au magasin d’objets. Si plusieurs réseaux existent au sein du cluster, tous doivent être attachés aux machines virtuelles DRVA.  | 
| **Récupération à partir de l’appliance virtuelle de l’objet cloud (RocVA)**  | Si le seul réseau utilisé est le réseau de gestion, assurez-vous qu’il a accès au(x) hôte(s) ESXi et au magasin d’objets. Si plusieurs réseaux existent au sein du cluster, tous doivent être attachés aux machines virtuelles RocVA. La RocVA est une machine virtuelle temporaire créée automatiquement si nécessaire pour la récupération de la machine virtuelle, puis supprimée lorsqu’elle n’est plus nécessaire.  | 
| **Magasin d’objets/stockage d’objets BLOB**  | Le magasin d’objets/le stockage d’objets BLOB doit être accessible à la fois au site protégé et au site de récupération.  | 
| **Magasin de journaux de réplication**  | Les hôtes DRVA et ESXi doivent avoir un accès direct à ce stockage via iSCSI.  | 


## <a name="install-jetstream-dr"></a>Installer JetStream DR

L’installation de JetStream DR est disponible via la fonctionnalité Exécuter la commande dans le portail de la solution Azure VMware. Vous pourrez finaliser l’installation en trois étapes.

:::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="Capture d’écran montrant comment accéder aux commandes d’exécution JetStream disponibles." lightbox="media/run-command/run-command-overview-jetstream.png":::


>[!NOTE]
>Les commandes d’exécution sont exécutées successivement dans l’ordre de leur envoi.

### <a name="check-the-current-state-of-the-system"></a>Vérifier l’état actuel du système

Vous exécuterez la cmdlet `Invoke-PreflightJetDRSystemCheck` pour vérifier l’état de votre système et déterminer si la configuration minimale requise pour le script est atteinte. Il vérifie également la configuration vCenter requise pour exécuter d’autres applets de commande.  

L’applet de commande vérifie les éléments suivants :

- PowerShell
- Nom de domaine complet vCenter
- Rôle CloudAdmin
- Modules VMware

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Exécuter la commande** > **Packages** > **Invoke-PreflightJetDRSystemCheck**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **checkDRsystem**.  |
   | **Délai d'expiration**  | Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression. 



### <a name="check-cluster-configuration"></a>Vérifier la configuration du cluster

Vous exécuterez la cmdlet `Invoke-PreflightJetDRInstall` pour vérifier la configuration de cluster suivante :

- Si les détails du cluster sont corrects 
- Dispose d’au moins quatre hôtes (au minimum)
- S’il existe une machine virtuelle portant le même nom que celle fournie pour l’installation du MSA
- Si un plug-in **jetdr** est présent dans vCenter


1. Sélectionnez **Exécuter la commande** > **Packages** > **Invoke-PreflightJetDRInstall**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **VMName** | Nom de la machine virtuelle du MSA. Par exemple, **jetstreamServer**. |
   | **Cluster** | Nom du cluster sur lequel le MSA sera déployé. Par exemple : **Cluster-1**. |
   | **ProtectedCluster** | Cluster à protéger. Par exemple : **Cluster-1**. |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **check_jetserverdetails**.  |
   | **Délai d'expiration**  | Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression.  

1. Si des erreurs sont signalées, vous pouvez passer à l’étape suivante pour déployer le MSA JetDR.


### <a name="deploy-jetdr-msa"></a>Déployer le MSA JetDR

Vous exécuterez la cmdlet `Install-JetDR` pour déployer le MSA JetDR, inscrire vCenter dans le MSA JetDR et configurer des clusters. Le déploiement télécharge le pack JetDR à partir de Microsoft Server Media (MMS) et crée un nouvel utilisateur avec des privilèges élevés affectés. 

1. Sélectionnez **Exécuter la commande** > **Packages** > **Install-JetDR**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **Réseau** | Mappage réseau pour le MSA à déployer. Par exemple, **VM Network**.  |
   | **HostName** | Nom d’hôte (FQDN) du MSA à déployer.  |
   | **Informations d'identification** | Informations d’identification de l’utilisateur racine du MSA à déployer.   |
   | **Passerelle** | Passerelle du MSA à déployer. Laissez vide pour DHCP.  |
   | **Dns** | Adresse IP DNS que MSA doit utiliser. Laissez vide pour DHCP.  |
   | **MSIp** | Adresse IP du MSA à déployer. Laissez vide pour DHCP.  |
   | **Masque réseau** | Masque réseau du MSA à déployer. Laissez vide pour DHCP.  |
   | **Cluster** | Nom du cluster sur lequel le MSA sera déployé. Par exemple : **Cluster-1**. |
   | **VMName** | Nom de la machine virtuelle du MSA. Par exemple, **jetstreamServer**. |
   | **Magasins de données** | Magasin de données où le MSA sera déployé.  |
   | **ProtectedCluster** | Cluster à protéger. Par exemple : **Cluster-1**. |
   | **RegisterWithIp** | Inscrivez le MSA à partir de l’IP au lieu du nom d’hôte. <ul><li>True si le nom d’hôte du MSA n’est pas inscrit dans le DNS.</li><li>False si le nom d’hôte du MSA est inscrit dans le DNS. </li></ul> |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **check_jetserverdetails**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression.  


## <a name="uninstall-jetstream-dr"></a>Désinstaller JetStream DR

Vous désinstallez JetStream DR en deux étapes. 


### <a name="check-current-state-of-the-jetstream-appliance"></a>Vérifier l’état actuel de l’appliance JetStream

Vous exécuterez la cmdlet `Invoke-PreflightJetDRUninstall` pour diagnostiquer la configuration du cluster et de la machine virtuelle MSA existante. Elle vérifie l’état actuel de l’appliance JetStream DR et si la configuration minimale requise pour le script est atteinte :

- Si les détails du cluster sont corrects 
- Dispose d’au moins quatre hôtes (au minimum)
- Si vCenter est inscrit auprès du MSA

1. Sélectionnez **Exécuter la commande** > **Packages** > **Invoke-PreflightJetDRUninstall**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **MSIp** | Adresse IP de la machine virtuelle MSA.  |
   | **Informations d'identification** | Informations d’identification de l’utilisateur racine de la machine virtuelle MSA. Elles doivent être les mêmes que celles fournies au moment de l’installation.  |
   | **ProtectedCluster** | Nom du cluster protégé, par exemple **Cluster-1**.  Il doit s’agir du cluster qui a été fourni au moment de l’installation. |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **uninstallcheck_jetserverdetails**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression.  




### <a name="uninstall-jetdr"></a>Désinstaller JetDR

Vous exécuterez la cmdlet `Uninstall-JetDR` pour désinstaller la récupération d’urgence Jetstream et ses composants.  Cela annule la configuration du cluster, annule l’inscription de vCenter à partir du MSA, puis supprime l’utilisateur.

1. Sélectionnez **Exécuter la commande** > **Packages** > **Uninstall-JetDR**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **MSIp** | Adresse IP de la machine virtuelle MSA.  |
   | **Informations d'identification** | Informations d’identification de l’utilisateur racine de la machine virtuelle MSA. Elles doivent être les mêmes que celles fournies au moment de l’installation.   |
   | **ProtectedCluster** | Nom du cluster protégé, par exemple **Cluster-1**.  Il doit s’agir du cluster qui a été fourni au moment de l’installation. |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **uninstallcheck_jetserverdetails**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression.  


## <a name="next-steps"></a>Étapes suivantes

- [JetStream DR pour la solution VMware Azure - Démonstration complète](https://vimeo.com/475620858/2ce9413248)
   
   - [Prise en main de la solution de JetStream DR pour Azure VMware Solution](https://vimeo.com/491880696/ec509ff8e3)

   - [Configuration et protection des machines virtuelles](https://vimeo.com/491881616/d887590fb2)

   - [Basculement vers Azure VMware Solution](https://vimeo.com/491883564/ca9fc57092)

   - [Restauration automatique sur un site local](https://vimeo.com/491884402/65ee817b60)

- [Configuration de l’infrastructure JetStream DR pour Azure VMware Solution](https://vimeo.com/480574312/b5386a871c) (*détails techniques, pas de suivi vocal*)
