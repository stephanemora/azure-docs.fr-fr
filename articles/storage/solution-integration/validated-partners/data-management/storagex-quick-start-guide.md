---
title: Analyser et migrer vos données de fichier vers Azure avec Data Dynamics StorageX
titleSuffix: Azure Storage
description: Guide de mise en route pour l’implémentation de Data Dynamics StorageX. Ce guide explique comment migrer les données vers Azure Files, Azure NetApp Files, le Stockage Blob Azure ou n’importe quelle solution NAS ISV disponible.
author: dukicn
ms.author: nikoduki
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 4b3f85c06c8dccd5873556c8c1ebfe1f0087b11e
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407605"
---
# <a name="migrate-data-to-azure-with-data-dynamics-storagex"></a>Migrer des données vers Azure avec Data Dynamics StorageX

Cet article vous aide à déployer Data Dynamics StorageX dans Microsoft Azure. Nous présentons des concepts clés concernant le fonctionnement de StorageX, les conditions préalables au déploiement, le processus d’installation et les procédures d’aide. Pour obtenir des informations plus approfondies, visitez le [portail client Dynamics Data Dynamics](https://www.datdynsupport.com/).

Data Dynamics StorageX est une plate-forme de gestion des données non structurée unifiée, qui permet d’analyser, de gérer et de déplacer des données dans des environnements de stockage hétérogènes. Ses fonctionnalités principales sont les suivantes :
- Fonctionnalités de déplacement de données
    - NAS vers NAS (avec ou sans analyse)
    - Réplication NAS vers objet
    - Archivage NAS vers objet
- Rapport de vérification des fichiers dupliqués
- Ouvrir le rapport des partages
- Analyse des métadonnées de fichier avec balisage personnalisé
  
Vous trouverez plus de fonctionnalités sur StorageX et la comparaison avec des outils similaires dans notre [tableau de comparaison](./migration-tools-comparison.md).

Ce document aborde trois sujets principaux :
- Processus de déploiement de l’offre StorageX dans un environnement Azure
- Étapes de base pour l’ajout de ressources de stockage de fichiers et cloud à StorageX
- Création de stratégies de déplacement de données

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme suivant présente une architecture de référence pour les déploiements de StorageX entre un site local et Azure et au sein d’Azure.

:::image type="content" source="./media/storagex-quick-start-guide/storagex-architecture.png" alt-text="Diagramme illustrant l’architecture de référence pour l’implémentation de StorageX":::

## <a name="storagex-interoperability-matrix"></a>Matrice d’interopérabilité de StorageX

| Fonctionnalité | SMB | NFS | Notes |
| ------- |--- | --- | --- |
| **Migration** | SMB vers SMB | NFSv3 vers NFSv3 | Peut être combiné pour multi-protocole |
| **Archivage NAS vers objet** | Stockage SMB vers Azure Blob | Stockage NFSv3 vers Azure Blob | Archiver les données pour optimiser les coûts |
| **Réplication fichier vers objet** | Stockage SMB vers Azure Blob | Stockage NFSv3 vers Azure Blob | Répliquer des données pour augmenter la protection |

## <a name="before-you-begin"></a>Avant de commencer

La planification initiale facilite la migration et réduit les risques. Voici quelques conseils pour faciliter la migration :

- Collecter des données sur les services source et cible
- Vérifiez que vous disposez d’une liste de vos points de connectivité et de tous les ports réseau nécessaires.
- Votre serveur StorageX dans Azure doit faire partie de votre infrastructure Active Directory. Veillez à faire appel à un administrateur disposant de privilèges Active Directory pour ajouter le serveur à Active Directory.
- Pour faciliter la mise en œuvre, utilisez [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview).

### <a name="get-started-with-azure"></a>Prise en main d’Azure

Microsoft offre une infrastructure à suivre pour vous aider à prendre en main Azure. Le [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) est une approche détaillée de la transformation numérique d’entreprise et un guide complet pour la planification d’une adoption du cloud en production. Le CAF contient un [guide de configuration d’Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) pas à pas pour vous aider à être opérationnel rapidement et en toute sécurité. Vous pouvez en trouver une version interactive dans le [portail Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Vous y trouverez des exemples d’architectures, des pratiques recommandées spécifiques pour le déploiement d’applications, et des ressources de formation gratuites pour vous aider à acquérir une expertise Azure.

### <a name="considerations-for-migrations"></a>Considérations relatives aux migrations

Plusieurs aspects sont importants lorsqu’il s’agit de migrer des données de fichier vers Azure. Avant de continuer, renseignez-vous sur les points suivants :

- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)
- Dernières fonctionnalités prises en charge par Data Dynamics StorageX dans la [matrice de comparaison des outils de migration](./migration-tools-comparison.md)

N’oubliez pas que vous aurez besoin d’une capacité réseau suffisante pour prendre en charge les migrations sans que cela ait un impact sur les applications en production. Cette section décrit les outils et techniques disponibles pour évaluer vos besoins en matière de réseau.

#### <a name="determine-unutilized-internet-bandwidth"></a>Déterminer la bande passante Internet inutilisée

Il est important de connaître la quantité de bande passante généralement inutilisée (ou *marge*) dont vous disposez au quotidien. Cela vous permet d’évaluer si vous pouvez atteindre vos objectifs :

- Temps initial pour les migrations quand vous n’utilisez pas Azure Data Box pour la méthode hors connexion
- Temps nécessaire à la resynchronisation incrémentielle avant le basculement final vers le service de fichiers cible

Utilisez les méthodes suivantes pour identifier la marge de bande passante disponible sur Azure.

- Si vous êtes un client Azure ExpressRoute, consultez votre [utilisation du circuit](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) dans le portail Azure.
- Contactez votre fournisseur de services Internet et demandez-lui des rapports qui indiquent votre utilisation quotidienne et mensuelle actuelle.
- Il existe plusieurs outils capables de mesurer l’utilisation en surveillant votre trafic réseau au niveau du routeur ou du commutateur :
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

#### <a name="creating-a-storagex-service-account"></a>Création d’un compte de service StorageX

Pour les migrations SMB, le compte de service pour exécuter les services StorageX est recommandé. Le compte de service permet de suivre l’accès pour des raisons de support, tout en donnant les privilèges appropriés pour accorder un accès total.

Le processus qui exécute des stratégies de déplacement de données sur les moteurs de données universelles StorageX utilise le compte de service StorageX. Le compte doit avoir tous les privilèges nécessaires qui lui sont attribués dans le domaine Active Directory.

Vous pouvez créer un compte tel qu’un compte d’administrateur de domaine si votre environnement vous y autorise. L’approche recommandée consiste à accorder des autorisations directement sur le serveur NAS. Pour ce faire, placez le compte de service dans les groupes Administrateurs et Opérateurs de sauvegarde.

Le compte de service StorageX requiert les privilèges suivants :

- SeServiceLogonRight
- SeSecurityPrivilege 
- SeBackupPrivilege 
- SeRestorePrivilege 
- SeAuditPrivilege
- SeInteractiveLogonRight

L’accès NFS nécessite une règle d’exportation pour l’adresse IP du serveur StorageX et accorde l’accès pour RW et la racine. Par exemple, « root=10.2.3.12, rw=10.2.3.12 », où 10.2.3.12 est l’adresse IP du serveur StorageX.

## <a name="deployment-guide"></a>Guide de déploiement

Cette section fournit des conseils sur le déploiement d’un serveur StorageX et d’autres composants StorageX dans votre environnement Azure.

Avant le démarrage de l’implémentation, plusieurs conditions préalables sont importantes :

- Le service de stockage cible doit être identifié et créé.
- Assurez-vous que les ports appropriés sont ouverts pour accéder aux services de stockage. Vous pouvez utiliser l’outil [portqryui](https://www.microsoft.com/download/details.aspx?id=24009) de Microsoft pour vérifier que les ports nécessaires sont ouverts.

Pour plus d’informations, consultez les pages suivantes :

- Guide pratique pour créer un [partage de fichiers Azure](../../../files/storage-how-to-create-file-share.md)
- Guide pratique pour créer un [volume SMB](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) ou une [exportation NFS](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md) dans Azure NetApp Files

Toutes les migrations SMB nécessitent qu’Active Directory soit correctement configuré avant d’ajouter des ressources. Nous allons utiliser une implémentation de Azure NetApp Files existante avec un nouveau volume pour la cible de migration. Avant de pouvoir créer un volume Azure NetApp Files, nous devons :

- [Créer un compte Azure NetApp Files](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-netapp-account)
- [Créer un pool de capacité](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-capacity-pool)
- [Joindre Azure NetApp Files compte à domaine Active Directory](../../../../azure-netapp-files/create-active-directory-connections.md)

Une fois la configuration de Azure NetApp Files effectuée, nous pouvons créer un partage que nous utiliserons pour la migration. 

1. Accédez à compte Azure NetApp Files existant, puis sélectionnez **Volumes**. Sélectionnez **Ajouter un volume**. Donnez un nom à ce volume et sélectionnez un pool de capacité et une configuration de mise en réseau.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-1.png" alt-text="Capture d’écran d’ajout d’un nouveau volume Azure NetApp Files":::

1. Sélectionnez le protocole **SMB**, puis la connexion Active Directory existante, et spécifiez un **nom de partage**.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-2.png" alt-text="Capture d’écran des propriétés SMB dans le volume Azure NetApp Files":::

1. Ajoutez des **balises** si vous en utilisez et **créez** un partage.

    Une fois le volume créé, nous pouvons procéder au déploiement de StorageX.

1.  Déployez le déplacement des données de Data Dynamics et l’offre de mobilité dans Azure. L’approche recommandée est d’utiliser [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview). Ajoutez le serveur déployé dans votre domaine Active Directory.
1.  Sur le serveur déployé, vérifiez que tous les ports réseau requis sont ouverts.
1.  Exécutez le script d’installation StorageX. Le script d’installation est disponible sur le bureau lorsque vous vous connectez à votre serveur déployé dans Azure. 
    Tous les fichiers d’installation et journaux de sortie se trouvent dans le dossier **C:\ProgramData\data Dynamics\StorageX**. Dans ce dossier, le sous-dossier **InstallationFiles** contient les fichiers d’installation source compressés, tandis que le dossier **SilentInstaller-DD** contient les fichiers XML de configuration et les journaux d’installation.

    Par défaut, le script d’installation déploie les services suivants :

    - StorageX
    - API StorageX
    - StorageX Processing Engine
    - StorageX Universal Data Engine
    - Récupération de fichier StorageX
    - Elasticsearch
    - Microsoft SQL Express
    - Microsoft SQL Management Studio
    - Kibana
    - Tout logiciel de prise en charge (par exemple, Java)

    Le script d’installation contient un fichier de configuration XML dans lequel ces différents services peuvent être personnalisés, mais nous vous déconseillons de modifier manuellement la configuration.

    Pour procéder à l’installation, cliquez avec le bouton droit sur **StorageXPOCSilentInstaller - Raccourci** sur le bureau et sélectionnez **Exécuter en tant qu’administrateur**. Après le démarrage de l’installation, vous êtes invité à spécifier le compte de service StorageX et le mot de passe de ce compte.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-1.jpg" alt-text="Capture d’écran de l’interface CLI d’installation":::

    Le script d’installation va continuer à installer tous les services. Une fois le processus d’installation terminé, le message s’affiche dans l’interface de ligne de commande.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-2.jpg" alt-text="Capture d’écran montrant la fin de l’installation":::

    StorageX va démarrer automatiquement.

## <a name="migration-guide"></a>Guide de migration

Une fois l’installation terminée et tous les services démarrés, nous pouvons commencer le processus de migration.

1.  Ouvrez la console StorageX.
1.  Cliquez sur l’onglet **Ressources de stockage**.
1.  Dans la vue **Mes ressources**, ajoutez vos périphériques ou services de stockage NAS, appelés **ressources de stockage** dans StorageX. Vous pouvez ajouter différents types :
    - **Ajouter une ressource de stockage de fichiers** pour les périphériques NAS locaux, le serveur de fichiers Windows, Azure NetApp Files ou des serveurs de fichiers génériques,
    - **Ajouter une ressource de stockage d’objets** pour le stockage S3 ou Azure Blob (y compris le service Azure Files).
    - **Ajouter un espace de noms DFS** pour les serveurs de fichiers DFS.

    Sélectionnez **Ajouter une ressource de stockage de fichiers** pour démarrer l’assistant d’ajout de cible de migration Azure NetApp Files.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-1.png" alt-text="Capture d’écran de l’ajout d’une nouvelle ressource au déploiement StorageX":::

1. Sélectionnez **CIFS générique** pour les migrations CIFS et SMB.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-2.png" alt-text="Capture d’écran de l’ajout d’une ressource CIFS générique au déploiement StorageX":::

1. Recherchez le compte d’ordinateur Azure NetApp Files. Le compte d’ordinateur Azure NetApp Files a été créé lors de son ajout au domaine Active Directory.
   
    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-3.png" alt-text="Capture d’écran de l’ajout du compte d’ordinateur Azure NetApp Files":::

    **Terminez** l’Assistant et vérifiez que le compte a été ajouté et qu’il contient le partage que vous avez créé.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-4.png" alt-text="Capture d’écran permettant de parcourir les ressources ajoutées":::

    Toutes les sources et cibles doivent être ajoutées en tant que ressource distincte. Cet exemple illustre l’ajout du partage Azure NetApp Files et peut être légèrement différent pour d’autres services.

1.  Cliquez sur l’onglet **Déplacement des données**.

1.  Dans le volet de l’arborescence, cliquez avec le bouton droit sur le dossier dans lequel vous souhaitez créer la nouvelle stratégie et sélectionnez **Stratégie de migration par phases**.

1.  Suivez les étapes de l’Assistant pour créer une stratégie de migration par phases. Sélectionnez les cibles **Source** et **Destination** appropriées.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-1.png" alt-text="Capture d’écran de sélection des cibles sources et de destination appropriées":::

1. Fournissez un nom et une description pour la stratégie et cliquez sur **Terminer** pour quitter l’assistant.

1.  Cliquez avec le bouton droit sur la nouvelle stratégie et sélectionnez **Exécuter** pour exécuter une migration test. Nous vous recommandons de vérifier la configuration de la sécurité sur plusieurs fichiers sur la ressource source pour vérifier que les autorisations nécessaires ont été fournies.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-2.png" alt-text="Capture d’écran de l’exécution d’une stratégie définie":::

1. Vérifiez l’état de la stratégie pour vous assurer que la migration initiale s’est correctement déroulée. 

## <a name="support"></a>Support

Si des problèmes se produisent, Microsoft et Data Dynamics peuvent fournir une aide via les canaux de support standard. Contactez Microsoft pour les problèmes d’infrastructure ou touchant n’importe quel service Azure. Contactez Data Dynamics pour les problèmes liés à l’installation de Data Dynamics StorageX.

### <a name="how-to-open-a-case-with-azure"></a>Comment ouvrir un cas de support auprès d’Azure

Dans le [portail Azure](https://portal.azure.com), recherchez support dans la barre de recherche en haut. Sélectionnez **Aide + support** -> **Nouvelle demande de support**.

### <a name="how-to-open-a-case-with-data-dynamics"></a>Comment ouvrir un cas de support auprès de Data Dynamics

Accédez au [portail de support Data Dynamics](https://www.datdynsupport.com/). Si vous ne vous êtes pas inscrit, indiquez votre adresse de messagerie et notre équipe de support créera un compte pour vous. Une fois que vous êtes connecté, ouvrez une demande de l’utilisateur. Si vous avez déjà ouvert un dossier de support Azure, notez le numéro de demande de support lors de la création de la demande.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, différentes ressources sont disponibles :

- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)
- Fonctionnalités prises en charge par Data Dynamics StorageX dans la [matrice de comparaison des outils de migration](./migration-tools-comparison.md)
- [Data Dynamics](https://www.datadynamicsinc.com/)
- Le [portail client Data Dynamics](https://www.datdynsupport.com/) contient une documentation complète pour StorageX