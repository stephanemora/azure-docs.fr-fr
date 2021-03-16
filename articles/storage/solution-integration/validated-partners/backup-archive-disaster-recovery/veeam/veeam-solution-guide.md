---
title: Sauvegarder vos données dans Azure avec Veeam
titleSuffix: Azure Blob Storage Docs
description: La page web donne une vue d’ensemble des facteurs à prendre en considération et des étapes à suivre pour tirer parti d’Azure en tant que cible de stockage et emplacement de récupération pour la sauvegarde et la récupération Veeam
keywords: Veeam, sauvegarde dans le cloud, sauvegarde, sauvegarde sur Azure, récupération d’urgence, continuité des activités
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 6d4b005a3f9c79ff14f5ba4053dc651c1ede56e0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124429"
---
# <a name="backup-to-azure-with-veeam"></a>Sauvegarde sur Azure avec Veeam

Cet article constitue un guide pour l’intégration d’une infrastructure Veeam avec le service Stockage Blob Azure. Il décrit les conditions préalables, les principes du service Stockage Azure, l’implémentation et des conseils opérationnels. Cet article traite uniquement de l’utilisation d’Azure en tant que cible de sauvegarde hors site et de récupération d’urgence, ce qui empêche le fonctionnement normal de votre site principal. Veeam offre également une solution d’objectif de délai de récupération (RTO) inférieur, la réplication Veeam, afin de disposer d’une machine virtuelle de secours prête pour le démarrage et la récupération plus rapidement en cas de sinistre, et d’une protection des ressources au sein d’un environnement de production Azure. Veeam dédie également des outils aux ressources Sauvegarde Azure et Office 365. Ces fonctionnalités sont hors du champ d’application de ce document. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Architecture de référence pour les déploiements à partir d’un site local vers Azure et dans Azure

![Architecture de référence de déploiement de Veeam vers Azure](../media/veeam-architecture.png)

Votre déploiement de Veeam existant peut facilement s’intégrer à Azure en ajoutant un compte de stockage Azure, ou plusieurs comptes en guise d’espace de stockage de sauvegarde dans le cloud. Veeam vous permet également de récupérer des sauvegardes à partir d’un site local dans Azure, ce qui vous permet de disposer d’un site de récupération à la demande dans Azure.

## <a name="veeam-interoperability-matrix"></a>Matrice d’interopérabilité de Veeam
| Charge de travail | GPv2 et Stockage Blob | Prise en charge du niveau de stockage froid | Prise en charge du niveau de stockage archive | Prise en charge de la gamme Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Machines virtuelles/données locales | v10a | v10a | N/D | 10a* |
| Machines virtuelles Azure | v10a | v10a | N/D | 10a* |
| Objets blob Azure | v10a | v10a | N/D | 10a* |
| Azure Files | v10a | v10a | N/D | 10a* | 

> [!Note]
La sauvegarde et la réplication de Veeam prenant en charge une API REST uniquement pour Azure Data Box, l’offre Azure Data Box Disk n’est pas prise en charge. La prise en charge du niveau de stockage archive du Stockage Blob Azure est attendue dans Veeam v11.

## <a name="before-you-begin"></a>Avant de commencer

Une petite planification initiale vous permet de vous assurer que vous rejoignez les rangs des nombreux clients satisfaits utilisant Azure en guise de cible de sauvegarde hors site et de site de récupération.

### <a name="are-you-new-to-azure"></a>Vous débutez avec Azure ?

Microsoft offre une infrastructure à suivre pour vous aider à prendre en main Azure. Le [Cloud Adoption Framework](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \(CAF\) est une approche détaillée de la transformation numérique d’entreprise et un guide complet pour la planification d’une adoption du cloud en production. Le CAF inclut un [Guide de configuration Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) contenant des instructions pas à pas destinées à ceux qui débutent avec Azure pour vous aider à devenir opérationnels rapidement et en toute sécurité, dont une version interactive est disponible sur le [portail Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Vous y trouverez des exemples d’architectures et des pratiques recommandées pour le déploiement d’applications, ainsi que des ressources de formation gratuites pour vous aider à acquérir une expertise Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Prendre en compte le réseau entre votre site et Azure

Que vous utilisiez des ressources cloud à des fins de production, de test et de développement, ou en tant que cible de sauvegarde et site de récupération, il est important que vous compreniez vos besoins en termes de bande passante pour l’essaimage de la sauvegarde initiale et pour les transferts réguliers au quotidien.

Azure Data Box offre un moyen de transférer votre base de référence de sauvegarde initiale vers Azure sans avoir besoin de bande passante supplémentaire si la durée estimée du transfert de la base de référence est supérieure à celle que vous pouvez tolérer. Vous pouvez tirer parti de l’estimateur de transfert de données lorsque vous créez un compte de stockage pour estimer le temps nécessaire au transfert de votre sauvegarde initiale.

![Estimateur de transfert de données du service Stockage Azure](../media/az-storage-transfer.png)

N’oubliez pas que vous aurez besoin d’une capacité réseau suffisante pour prendre en charge les transferts de données quotidiens au sein de la fenêtre de transfert requise (fenêtre de sauvegarde) sans que cela ait un impact sur les applications de production. Cette section décrit les outils et techniques disponibles pour évaluer vos besoins en matière de réseau.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Comment pouvez-vous déterminer la quantité de bande passante dont vous aurez besoin ?

Plusieurs options d’évaluation sont disponibles pour déterminer le taux de modification et la taille totale du jeu de sauvegarde pour le transfert de la base de référence initiale vers Azure. Voici quelques exemples d’outils d’évaluation et de création de rapports tels que :
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Comment déterminer la marge dont je dispose avec ma connexion Internet actuelle ?

Il est important de connaître la marge, ou quantité de bande passante généralement inutilisée dont vous disposez au quotidien. Cela vous permettra d’évaluer correctement si vous pouvez atteindre vos objectifs de temps de chargement initial sans utiliser Azure Data Box pour l’essaimage hors connexion, et pour les sauvegardes quotidiennes en fonction du taux de modification identifié ci-dessus et de votre fenêtre de sauvegarde. Vous trouverez ci-dessous les méthodes que vous pouvez utiliser pour identifier la marge de bande passante que vos sauvegardes sur Azure peuvent consommer.

- Êtes-vous un client Azure ExpressRoute ? Regardez votre [utilisation du circuit](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) sur le portail Azure.
- Vous pouvez contacter votre fournisseur de services Internet. Il doit avoir des rapports à partager avec vous, illustrant votre utilisation quotidienne et mensuelle.
- Il existe plusieurs outils capables de mesurer l’utilisation en surveillant votre trafic réseau au niveau du routeur/commutateur, à savoir :
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>Choix des options de stockage appropriées

Les clients qui utilisent d’Azure en tant que cible de sauvegarde recourent au service [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Le service Stockage Blob Azure est une solution de stockage d’objets de Microsoft. Le stockage d’objets blob est optimisé pour stocker de grandes quantités de données non structurées ne suivant aucun modèle ni aucune définition. En outre, le service Stockage Azure est durable, hautement disponible, sécurisé et évolutif. La plateforme de Microsoft offre une flexibilité permettant de sélectionner le bon stockage pour la bonne charge de travail afin d’offrir le [niveau de résilience](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) nécessaire pour honorer vos contrats de niveau de service (SLA) internes. Le service Stockage Blob est assorti d’un mode de paiement à l’utilisation. Vous êtes [facturé mensuellement](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) pour la quantité de données stockées, l’accès à celles-ci et, dans le cas des niveaux de stockage froid et archive, une période de rétention minimale requise. Les options de résilience et de hiérarchisation applicables aux données de sauvegarde sont résumées dans les tableaux ci-dessous.

**Options de résilience du service Stockage Blob Azure :**

|  |Localement redondant  |Redondant interzone  |Géographiquement redondant  |Redondant dans une zone géographique  |
|---------|---------|---------|---------|---------|
|Nombre effectif de copies     | 3         | 3         | 6         | 6 |
|Nombre de zones de disponibilité     | 1         | 3         | 2         | 4 |
|Nombre de régions     | 1         | 1         | 2         | 2 |
|Basculement manuel vers une région secondaire     | N/D         | N/D         | Oui         | Oui |

**Niveaux du service Stockage Blob Azure :**

|  | Niveau de stockage chaud   |Niveau de stockage froid   | Niveau Archive |
| ----------- | ----------- | -----------  | -----------  |
| Disponibilité | 99,9 %         | 99 %         | Hors connexion      |
| Frais d’utilisation | Coûts de stockage supérieurs, coûts d’accès et de transaction inférieurs | Coûts de stockage inférieurs, coûts d'accès et de transaction supérieurs | Coûts de stockage les plus faibles, coûts d'accès et de transaction les plus élevés |
| Conservation des données minimale requise | N/D | 30 jours | 180 jours |
| Latence (temps jusqu’au premier octet) | Millisecondes | Millisecondes | Heures |

#### <a name="sample-backup-to-azure-cost-model"></a>Exemple de modèle de coût de sauvegarde sur Azure

Le concept de paiement à l’utilisation peut être déconcertant pour des clients qui découvrent le cloud public. Vous payez pour la capacité utilisée, ainsi que pour les transactions (lectures et écritures) et la [sortie des données](https://azure.microsoft.com/pricing/details/bandwidth/) relues dans votre environnement local quand [un service Azure ExpressRoute local direct ou un forfait de données illimité ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/) sont utilisés, qui incluent une sortie de données d’Azure. Vous pouvez effectuer une analyse de scénarios en fonction de la tarification catalogue ou de la [tarification de capacité réservée de Stockage Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations) qui permet de réaliser jusqu’à 38 % d’économies, dans la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/). Voici un exemple d’exercice de tarification pour modéliser le coût mensuel de la sauvegarde sur Azure. Ce n’est qu’un exemple et ***votre tarification peut varier en raison d’activités non capturées ici :***


|Facteur de coût  |Coût mensuel  |
|---------|---------|
|100 To de données de sauvegarde sur stockage froid     |1 556,48 USD         |
|2 To de nouvelles données écrites par jour x 30 jours     |72 USD pour les transactions          |
|Total mensuel estimé     |1 628,48 USD         |
|---------|---------|
|Restauration ponctuelle de 5 To localement via l’Internet public   | 527,26 USD         |

> [!Note]
Cette estimation a été effectuée à l’aide de la Calculatrice de prix Azure en fonction du tarif du paiement à l’utilisation dans la région USA Est, et est basée sur la taille de bloc de 256 Ko par défaut de Veeam pour les transferts WAN. Il se peut que cet exemple ne corresponde pas à vos besoins.

## <a name="implementation-and-operational-guidance"></a>Implémentation et instructions d’utilisation

Cette section constitue un bref guide pour l’ajout du service Stockage Azure à un déploiement local de Veeam. Si des instructions détaillées et des considérations relatives à la planification vous intéressent, nous vous recommandons de consulter le [Guide de sauvegarde de Veeam Cloud Connect](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Ouvrez le portail Azure et recherchez « comptes de stockage », ou cliquez sur l’icône des services par défaut.

      ![Portail Azure](../media/azure-portal.png)

      ![Comptes de stockage dans le portail Azure](../media/locate-storage-account.png)

2. Choisissez Ajouter un compte, sélectionnez ou créez un groupe de ressources, fournissez un nom unique, choisissez la région, sélectionnez les performances « Standard », conservez le type de compte « Stockage v2 », choisissez le niveau de réplication correspondant à vos contrats SLA, ainsi que le niveau par défaut de votre logiciel de sauvegarde. Un compte de stockage Azure rend les niveaux de stockage chaud, froid et archive disponibles au sein d’un compte unique, et les stratégies de Veeam vous permettent de tirer parti de plusieurs niveaux pour gérer efficacement le cycle de vie de vos données. Passez à l'étape suivante. 
    
      ![Création d’un compte de stockage](../media/account-create-1.png)

3. Conservez les options de mise en réseau par défaut pour l’instant, et accédez à « Protection des données ». Ici, vous pouvez choisir d’activer la « Suppression réversible » qui vous permet de récupérer un fichier de sauvegarde supprimé par inadvertance au cours de la période de rétention définie, et vous offre une protection contre toute suppression accidentelle ou malveillante. 
    ![Création d’un compte de stockage Partie 2](../media/account-create-2.png)

4. Ensuite, nous recommandons les paramètres par défaut de l’écran « Avancé » pour les cas d’usage de la Sauvegarde sur Azure.

    ![Création d’un compte de stockage Partie 3](../media/account-create-3.png) 

5. Ajoutez des étiquettes pour l’organisation si vous utilisez un étiquetage, puis créez votre compte. Vous disposez maintenant de plusieurs pétaoctets de stockage à la demande.

6. Deux étapes rapides suffisent désormais pour ajouter le compte à votre environnement Veeam. Accédez au compte que vous avez créé dans le portail Azure, puis, dans le menu « service BLOB » dans le panneau du portail, sélectionnez « Conteneurs ». Ajoutez un nouveau conteneur et choisissez un nom explicite. Ensuite, sous « Paramètres », accédez à l’élément « Clés d’accès » et copiez le « Nom du compte de stockage » ainsi que l’une des deux clés d’accès. Vous aurez besoin du nom du conteneur, du nom du compte et de la clé d’accès dans les étapes suivantes.

    ![Création d’un conteneur](../media/container-b.png)
    
    ![Récupérez les informations de ce compte](../media/access-key.png)
    
    > [!Note]
La sauvegarde et la réplication dans Veeam offrent d’autres options pour se connecter à Azure. Pour le cas d’usage évoqué dans cet article, qui recourt au service Stockage Blob Microsoft Azure en tant que cible de sauvegarde, l’utilisation de la méthode ci-dessus est la meilleure pratique recommandée.

7. ***(Facultatif)*** Vous pouvez ajouter des couches de sécurité à votre déploiement.

     1. Configurez un accès en fonction du rôle afin de limiter les utilisateurs autorisés à apporter des modifications à votre compte de stockage. [En savoir plus ici](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. Limitez l’accès au compte à des segments de réseau spécifiques avec le [Pare-feu de stockage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) afin d’empêcher les tentatives d’accès depuis l’extérieur de votre réseau d’entreprise.

    ![Pare-feu de stockage](../media/storage-firewall.png) 

    1. Définissez un [verrou de suppression](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) sur le compte pour empêcher la suppression accidentelle du compte de stockage.

    ![Verrouillage des ressources](../media/resource-lock.png) 1.) Configurez des [meilleures pratiques de sécurité](https://docs.microsoft.com/azure/storage/blobs/security-recommendations) supplémentaires. 
8. Dans la console de gestion de la sauvegarde et de la réplication de Veeam, accédez à « Espace de stockage de sauvegarde » --> cliquez avec le bouton droit dans le volet de vue d’ensemble, puis sélectionnez « Ajouter un espace de stockage de sauvegarde » pour ouvrir l’Assistant Configuration. Dans la boîte de dialogue, sélectionnez Stockage d’objets --> Stockage Blob Microsoft Azure --> Stockage Blob Azure.
    
    ![Écran a de l’Assistant Espace de stockage de Veeam](../media/veeam-repo-a.png)

    ![Écran b de l’Assistant Espace de stockage de Veeam](../media/veeam-repo-b.png)

    ![Écran c de l’Assistant Espace de stockage de Veeam](../media/veeam-repo-c.png)

9. Ensuite, spécifiez un nom et une description pour votre nouvel Espace de stockage blob Microsoft Azure.
    
    ![Écran d de l’Assistant Espace de stockage de Veeam](../media/veeam-repo-d.png)

10. À l’étape suivante, ajoutez les informations d’identification pour accéder à votre compte de stockage Azure. Dans le gestionnaire d’informations d’identification cloud, sélectionnez « Compte de Stockage Microsoft Azure », puis entrez le nom et la clé d’accès de votre compte de stockage. Dans le sélecteur de région, sélectionnez Azure Global et un serveur de passerelle, le cas échéant.
    
    ![Écran e de l’Assistant Espace de stockage de Veeam](../media/veeam-repo-e.png)

> [!Note]
Si vous choisissez de ne pas utiliser un serveur de passerelle Veeam, assurez-vous que toutes les extensions d’espace de stockage pour la montée en puissance parallèle disposent d’un accès direct à Internet.

11. Dans le registre de conteneurs, choisissez votre Conteneur de stockage Azure, puis sélectionnez ou créez un dossier dans lequel stocker vos sauvegardes. Vous pouvez également fixer une limite conditionnelle à la capacité de stockage globale utilisable par Veeam (recommandé). Examinez les informations affichées dans la section Résumé, puis exécutez l’outil de configuration. Vous pouvez ensuite sélectionner le nouvel espace de stockage dans la configuration de votre travail de sauvegarde.

    ![Écran f de l’Assistant Espace de stockage de Veeam](../media/veeam-repo-f.png)
    
    ![Écran g de l’Assistant Espace de stockage de Veeam](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Génération d’alertes et surveillance des performances Azure

Il est recommandé de surveiller vos ressources Azure et la capacité de Veeam à les utiliser comme vous le feriez avec n’importe quelle cible de stockage dont vous dépendez pour stocker vos sauvegardes. Une combinaison des fonctionnalités de surveillance d’Azure Monitor et de Veeam (par exemple, l’onglet Statistiques du nœud Travaux de la console de gestion Veeam, ou des options plus avancées comme Veeam un rapporteur) vous aidera à préserver l’intégrité de votre environnement.

#### <a name="microsoft-azure-portal"></a>Portail Microsoft Azure

Microsoft Azure fournit une solution de surveillance robuste sous la forme d’[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Vous pouvez [configurer Azure Monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) pour suivre la capacité, les transactions, la disponibilité, l’authentification, et bien d’autres aspects du service Stockage Azure. Vous trouverez la référence complète des métriques suivies [ici](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Parmi les métriques utiles à suivre figurent BlobCapacity (pour s’assurer que vous restez en dessous de la [Limite de capacité du compte de stockage](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account)), Entrée et Sortie (pour suivre la quantité de données écrites et lues dans votre compte de stockage Azure), et SuccessE2ELatency (pour suivre le temps d’aller-retour des demandes échangées entre Stockage Azure et votre MediaAgent). 

Vous pouvez également [créer des alertes de journal](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) pour suivre l’intégrité du service Stockage Azure et afficher le [Tableau de bord d’état d’Azure](https://status.azure.com/status) à tout moment.

#### <a name="veeam-reporting"></a>Génération de rapports Veeam

[Configuration de Veeam One Reporting](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Alarmes de sauvegarde et de réplication de Veeam](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Comment ouvrir des cas d’usage

Si vous avez besoin d’aide pour votre solution de sauvegarde sur Azure, nous vous recommandons d’ouvrir un cas auprès de Veeam et d’Azure afin que nos organisations de support technique puissent communiquer de manière collaborative, si nécessaire.

#### <a name="how-to-open-a-case-with-veeam"></a>Comment ouvrir un cas de support auprès de Veeam

Accédez au [Portail de support technique de Veeam](https://www.veeam.com/support.html), connectez-vous, puis ouvrez un cas de support.

Si voulez connaître les options de support que Veeam met à votre disposition, consultez [Stratégie de support client de Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

Vous pouvez également appeler pour ouvrir un cas de support :

[Numéros de support dans le monde](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Comment ouvrir un cas de support auprès de l’équipe du support Azure

Dans le [portail Azure](https://portal.azure.com) recherchez « Support » dans la barre de recherche en haut du portail, puis choisissez « + Nouvelle demande de support ». 
> [!Note]
Lors de l’ouverture d’un cas de support, spécifiez que vous avez besoin d’aide en lien avec le service « Stockage Azure » ou « Mise en réseau Azure », et **PAS** avec le service « Sauvegarde Azure ». Le service Sauvegarde Azure est natif de Microsoft Azure et votre cas de support sera mal aiguillé.

### <a name="links-to-relevant-veeam-documentation"></a>Liens vers la documentation Veeam pertinente

Documentation Veeam fournissant des détails supplémentaires :

[Guide de l’utilisateur de Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Guide d’architecture de Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Lien vers l’offre de la Place de marché

Vous pouvez également continuer à utiliser la solution Veeam que vous connaissez et approuvez pour protéger vos charges de travail en cours d’exécution sur Azure. Veeam a facilité le déploiement de sa solution dans Azure et la protection des machines virtuelles Azure ainsi que de nombreux autres services Azure.

[Déployer la solution de sauvegarde et de réplication Veeam via la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Feuille de données Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Étapes suivantes

Explorez des ressources supplémentaires sur ces sites web externes pour obtenir des informations sur des scénarios d’utilisation spécialisés :

[Vidéos explicatives sur Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Documentations techniques sur Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Base de connaissances et FAQ sur Veeam](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
