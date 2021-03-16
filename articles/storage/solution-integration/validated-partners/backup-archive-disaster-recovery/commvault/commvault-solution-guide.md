---
title: Sauvegarder vos données dans Azure avec Commvault
titleSuffix: Azure Blob Storage Docs
description: La page web donne une vue d’ensemble des facteurs à prendre en considération et des étapes à suivre pour tirer parti d’Azure en tant que cible de stockage et emplacement de récupération pour la sauvegarde et la récupération complètes CommVault
keywords: Commvault, sauvegarde dans le cloud, sauvegarde, sauvegarde sur Azure, récupération d’urgence, continuité des activités
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: f340a06f3b6b7c8fc0d78051fb0496dcab11fb5d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122746"
---
# <a name="back-up-to-azure-with-commvault"></a>Sauvegarder sur Azure avec Commvault

Cet article constitue un guide pour l’intégration d’une infrastructure CommVault avec le service Stockage Blob Azure. Il décrit les conditions préalables, les principes du service Stockage Azure, l’implémentation et des conseils opérationnels. Cet article traite uniquement de l’utilisation d’Azure en tant que cible de sauvegarde hors site et de récupération d’urgence, ce qui empêche le fonctionnement normal de votre site principal. CommVault offre également une solution d’objectif de délai de récupération (RTO) inférieur, CommVault Live Sync, afin de disposer d’une machine virtuelle de secours prête pour le démarrage et la récupération plus rapidement en cas de sinistre, et d’une protection des ressources au sein d’un environnement de production Azure. Ces fonctionnalités sont hors du champ d’application de ce document. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Architecture de référence pour les déploiements à partir d’un site local vers Azure et dans Azure

![Architecture de référence de déploiement de CommVault vers Azure](../media/commvault-diagram.png)

Votre déploiement de CommVault existant peut facilement s’intégrer à Azure en ajoutant un compte de stockage Azure, ou plusieurs comptes en guise de cible de stockage dans le cloud. CommVault vous permet également de récupérer des sauvegardes à partir d’un site local dans Azure, ce qui vous permet de disposer d’un site de récupération à la demande dans Azure.   

## <a name="commvault-interoperability-matrix"></a>Matrice d’interopérabilité de CommVault
| Charge de travail | GPv2 et Stockage Blob | Prise en charge du niveau de stockage froid | Prise en charge du niveau de stockage archive | Prise en charge de la gamme Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Machines virtuelles/données locales | v11.5 | v11.5 | V11.10 | V11.10 |
| Machines virtuelles Azure | v11.5 | v11.5 | v11.5 | N/D |
| Objets blob Azure | v11.6 | v11.6 | v11.6 | N/D |
| Azure Files | v11.6 | v11.6 | v11.6 | N/D | 

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

-  Rapports de votre logiciel de sauvegarde. 
  CommVault fournit des rapports standard permettant de déterminer le [taux de modification](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) et la [taille totale du jeu de sauvegarde](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) pour le transfert de base initial vers Azure.
- Sauvegardez des outils de création de rapports et d’évaluation indépendants du logiciel, tels que :
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
|2 To de nouvelles données écrites par jour x 30 jours     |39 USD pour les transactions          |
|Total mensuel estimé     |1 595,48 USD         |
|---------|---------|
|Restauration ponctuelle de 5 To localement via l’Internet public   | 491,26 USD         |


> [!Note] 
Cette estimation a été générée dans la Calculatrice de prix Azure à partir du tarif du paiement à l’utilisation dans la région USA Est, et est basée sur la taille de sous-segment par défaut de 32 Mo dans CommVault, qui génère 65 536 demandes Put (transactions d’écriture) par jour. Il se peut que cet exemple ne corresponde pas à vos besoins.

## <a name="implementation-and-operational-guidance"></a>Implémentation et instructions d’utilisation

Cette section constitue un bref guide pour l’ajout du service Stockage Azure à un déploiement local de Commvault. Si des instructions détaillées et des considérations relatives à la planification vous intéressent, nous vous recommandons de consulter le [Guide de l’architecture Azure pour Commvault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16).

1. Ouvrez le portail Azure et recherchez « comptes de stockage », ou cliquez sur l’icône des services par défaut.
    
    1. ![Portail Azure](../media/azure-portal.png)
  
    1. ![Comptes de stockage dans le portail Azure](../media/locate-storage-account.png)

2. Choisissez Ajouter un compte, sélectionnez ou créez un groupe de ressources, fournissez un nom unique, choisissez la région, sélectionnez les performances « Standard », conservez le type de compte « Stockage v2 », choisissez le niveau de réplication correspondant à vos contrats SLA, ainsi que le niveau par défaut de votre logiciel de sauvegarde. Un compte de stockage Azure rend les niveaux de stockage chaud, froid et archive disponibles au sein d’un compte unique, et les stratégies de Commvault vous permettent de tirer parti de plusieurs niveaux pour gérer efficacement le cycle de vie de vos données. Passez à l'étape suivante. 

    ![Création d’un compte de stockage](../media/account-create-1.png)

3. Conservez les options de mise en réseau par défaut pour l’instant, et accédez à « Protection des données ». Ici, vous pouvez choisir d’activer la « Suppression réversible » qui vous permet de récupérer un fichier de sauvegarde supprimé par inadvertance au cours de la période de rétention définie, et vous offre une protection contre toute suppression accidentelle ou malveillante. 
    
    ![Création d’un compte de stockage Partie 2](../media/account-create-2.png)

4. Ensuite, nous recommandons les paramètres par défaut de l’écran « Avancé » pour les cas d’usage de la Sauvegarde sur Azure.

    ![Création d’un compte de stockage Partie 3](../media/account-create-3.png) 

5. Ajoutez des étiquettes pour l’organisation si vous utilisez un étiquetage, puis créez votre compte. Vous disposez maintenant de plusieurs pétaoctets de stockage à la demande.

6. Deux étapes rapides suffisent désormais pour ajouter le compte à votre environnement Commvault. Accédez au compte que vous avez créé dans le portail Azure, puis, dans le menu « service BLOB » dans le panneau du portail, sélectionnez « Conteneurs ». Ajoutez un nouveau conteneur et choisissez un nom explicite. Ensuite, sous « Paramètres », accédez à l’élément « Clés d’accès » et copiez le « Nom du compte de stockage » ainsi que l’une des deux clés d’accès. Vous aurez besoin du nom du conteneur, du nom du compte et de la clé d’accès dans les étapes suivantes.
    
    ![Création d’un conteneur](../media/container.png)
    
    ![Récupérez les informations de ce compte](../media/access-key.png)

7. ***(Facultatif)*** Vous pouvez ajouter des couches de sécurité à votre déploiement.
    
    1. Configurez un accès en fonction du rôle afin de limiter les utilisateurs autorisés à apporter des modifications à votre compte de stockage. [En savoir plus ici](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)
    1. Limitez l’accès au compte à des segments de réseau spécifiques avec le [Pare-feu de stockage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) afin d’empêcher les tentatives d’accès depuis l’extérieur de votre réseau d’entreprise.

    ![Pare-feu de stockage](../media/storage-firewall.png) 

    1. Définissez un [verrou de suppression](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) sur le compte pour empêcher la suppression accidentelle du compte de stockage.

    ![Verrou de ressource](../media/resource-lock.png)
    
    1. Configurez des [meilleures pratiques de sécurité](https://docs.microsoft.com/azure/storage/blobs/security-recommendations) supplémentaires.
    
1. Dans le centre de commandes CommVault, accédez à « Gérer » --> « Sécurité » --> « Gestionnaire d’informations d’identification ». Choisissez un « Compte Cloud », un « Type de fournisseur » de Stockage Microsoft Azure, sélectionnez le « MediaAgent » chargé d’échanger des données avec Azure, puis ajoutez le nom du compte de stockage et la clé d’accès.
    
    ![Informations d’identification CommVault](../media/commvault-credential.png)

9. Ensuite, accédez à « Stockage »--> « Cloud » dans le centre de commande CommVault. Choisissez « Ajouter ». Entrez un nom convivial pour le compte de stockage, puis, dans la liste « Type », sélectionnez « Stockage Microsoft Azure ». Sélectionnez un serveur d’agent multimédia à utiliser pour transférer les sauvegardes vers le service Stockage Azure. Ajoutez le conteneur que vous avez créé, choisissez le niveau de stockage à utiliser dans le compte de stockage Azure, puis sélectionnez les informations d’identification créées à l’Étape 8. Enfin, choisissez s’il faut ou non transférer les sauvegardes dédupliquées, et un emplacement pour la base de données de déduplication.
    
     ![Ajouter un stockage CommVault](../media/commvault-add-storage.png)

10. Enfin, ajoutez votre nouvelle ressource Stockage Azure à un plan existant ou nouveau dans le centre de commandes CommVault Command via « Gérer » --> « Plans » en tant que « Destination de sauvegarde ».

    ![Ajouter un stockage CommVault](../media/commvault-plan.png)

11. ***(Facultatif)*** Si vous envisagez de tirer parti d’Azure comme site de récupération, ou CommVault pour migrer des serveurs et applications vers Azure, il est recommandé de déployer un proxy VSA dans Azure. Pour des instructions détaillées, consultez [cet article](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).  

### <a name="azure-alerting-and-performance-monitoring"></a>Génération d’alertes et surveillance des performances Azure

Il est recommandé de surveiller vos ressources Azure et la capacité de CommVault à les utiliser comme vous le feriez avec n’importe quelle cible de stockage dont vous dépendez pour stocker vos sauvegardes. Une combinaison de la surveillance d’Azure Monitor et du centre de commandes CommVault vous aide à maintenir l’intégrité de votre environnement.

#### <a name="microsoft-azure-portal"></a>Portail Microsoft Azure

Microsoft Azure fournit une solution de surveillance robuste sous la forme d’[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Vous pouvez [configurer Azure Monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) pour suivre la capacité, les transactions, la disponibilité, l’authentification, et bien d’autres aspects du service Stockage Azure. Vous trouverez la référence complète des métriques suivies [ici](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Parmi les métriques utiles à suivre figurent BlobCapacity (pour s’assurer que vous restez en dessous de la [Limite de capacité du compte de stockage](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account)), Entrée et Sortie (pour suivre la quantité de données écrites et lues dans votre compte de stockage Azure), et SuccessE2ELatency (pour suivre le temps d’aller-retour des demandes échangées entre Stockage Azure et votre MediaAgent). 

Vous pouvez également [créer des alertes de journal](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) pour suivre l’intégrité du service Stockage Azure et afficher le [Tableau de bord d’état d’Azure](https://status.azure.com/status) à tout moment.

#### <a name="commvault-command-center"></a>Centre de commandes CommVault

[Création d’alertes pour des pools de stockage cloud](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)

[Où les clients peuvent-ils afficher des rapports de performances, suivre l’achèvement des travaux et commencer à résoudre des problèmes de base ?](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm) 

### <a name="how-to-open-support-cases"></a>Comment ouvrir des cas d’usage

Si vous avez besoin d’aide pour votre solution de sauvegarde sur Azure, nous vous recommandons d’ouvrir un cas auprès de CommVault et d’Azure afin que nos organisations de support technique puissent communiquer de manière collaborative, si nécessaire.

#### <a name="how-to-open-a-case-with-commvault"></a>Comment ouvrir un cas de support auprès de CommVault

Accédez au [site de support CommVault](https://www.commvault.com/support), connectez-vous et ouvrez un cas.

Si vous avez besoin de comprendre les options de contrat de support disponibles, consultez les [Options de support de CommVault](https://ma.commvault.com/support)

Vous pouvez également appeler pour ouvrir un cas ou contacter le support CommVault par e-mail :

Numéro gratuit : +1 877-780-3077

[Numéros de support dans le monde](https://ma.commvault.com/Support/TelephoneSupport)

[E-mail du support CommVault](mailto:support@commvault.com)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Comment ouvrir un cas de support auprès de l’équipe du support Azure

Dans le [portail Azure](https://portal.azure.com) recherchez « Support » dans la barre de recherche en haut du portail, puis choisissez « + Nouvelle demande de support ». 
> [!Note]
Lors de l’ouverture d’un cas de support, spécifiez que vous avez besoin d’aide en lien avec le service « Stockage Azure » ou « Mise en réseau Azure », et **PAS** avec le service « Sauvegarde Azure ». Le service Sauvegarde Azure est natif de Microsoft Azure et votre cas de support sera mal aiguillé.

### <a name="links-to-relevant-commvault-documentation"></a>Liens vers la documentation CommVault pertinente

Documentation CommVault fournissant des détails supplémentaires :

[Guide de l’utilisateur de CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)

[Guide de l’architecture Azure pour Commvault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16) 

### <a name="link-to-marketplace-offering"></a>Lien vers l’offre de la Place de marché

Vous pouvez également continuer à utiliser la solution CommVault que vous connaissez et approuvez pour protéger vos charges de travail en cours d’exécution sur Azure. CommVault a facilité le déploiement de sa solution dans Azure et la protection des machines virtuelles Azure ainsi que de nombreux autres services Azure.

[Déployer CommVault via la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)

[Azure Datasheet](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)

[Liste complète des fonctionnalités et services Azure pris en charge](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)

[Comment utiliser CommVault pour protéger SAP HANA dans Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Étapes suivantes

Explorez des ressources supplémentaires sur ces sites web externes pour obtenir des informations sur des scénarios d’utilisation spécialisés :

[Utiliser CommVault pour migrer vos serveurs et applications vers Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)

[Protéger SAP dans Azure avec CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)

[Protéger Office 365 avec CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)
