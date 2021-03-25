---
title: Sauvegarder vos données dans Azure avec Commvault
titleSuffix: Azure Storage
description: Donne une vue d’ensemble des facteurs à prendre en considération et des étapes à suivre pour utiliser Azure en tant que cible de stockage et emplacement de récupération pour la sauvegarde et la récupération complètes CommVault
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ce321574ce2878f51864f55bf5618df2c96d1068
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589886"
---
# <a name="backup-to-azure-with-commvault"></a>Sauvegarder sur Azure avec Commvault

Cet article vous aide à intégrer une infrastructure CommVault avec le service Stockage Blob Azure. Il comprend des conditions préalables, des considérations, une implémentation et des conseils opérationnels. Cet article traite de l’utilisation d’Azure en tant que cible de sauvegarde hors site et site de récupération en cas d’incident empêchant le fonctionnement normal de votre site principal.

> [!NOTE]
> Commvault offre une solution avec un objectif de délai de récupération (RTO) inférieur, Commvault Live Sync. Celle-ci vous permet de disposer d’une machine virtuelle de secours qui peut vous aider à récupérer plus rapidement en cas d’incident dans un environnement de production Azure. Ces fonctionnalités ne sont pas abordées dans ce document.

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme suivant présente une architecture de référence pour les déploiements de site local vers Azure et dans Azure.

![Architecture de référence de déploiement de CommVault vers Azure](../media/commvault-diagram.png)

Vous pouvez facilement intégrer votre déploiement existant de CommVault avec Azure en ajoutant un ou plusieurs comptes de stockage Azure en guise de cible de stockage cloud. CommVault vous permet également de récupérer des sauvegardes à partir d’un site local dans Azure, ce qui vous permet de disposer d’un site de récupération à la demande dans Azure.

## <a name="commvault-interoperability-matrix"></a>Matrice d’interopérabilité de CommVault

| Charge de travail | GPv2 et stockage d’objets blob | Prise en charge du niveau de stockage froid | Prise en charge du niveau de stockage archive | Prise en charge de la gamme Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Machines virtuelles/données locales | v11.5 | v11.5 | V11.10 | V11.10 |
| Machines virtuelles Azure | v11.5 | v11.5 | v11.5 | NON APPLICABLE |
| Objets blob Azure | v11.6 | v11.6 | v11.6 | NON APPLICABLE |
| Azure Files | v11.6 | v11.6 | v11.6 | NON APPLICABLE |

## <a name="before-you-begin"></a>Avant de commencer

Une petite planification initiale vous permet d’utiliser Azure en guise de cible de sauvegarde hors site et de site de récupération.

### <a name="get-started-with-azure"></a>Prise en main d’Azure

Microsoft offre une infrastructure à suivre pour vous aider à prendre en main Azure. Le [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) est une approche détaillée de la transformation numérique d’entreprise et un guide complet pour la planification d’une adoption du cloud en production. Le CAF contient un [guide de configuration d’Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) pas à pas pour vous aider à être opérationnel rapidement et en toute sécurité. Vous pouvez en trouver une version interactive dans le [portail Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Vous y trouverez des exemples d’architectures, des pratiques recommandées spécifiques pour le déploiement d’applications, et des ressources de formation gratuites pour vous aider à acquérir une expertise Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Prendre en compte le réseau entre votre site et Azure

Que vous utilisiez des ressources cloud à des fins de production, de test et de développement, ou en tant que cible de sauvegarde et site de récupération, il est important que vous compreniez vos besoins en termes de bande passante pour l’essaimage de la sauvegarde initiale et pour les transferts réguliers au quotidien.

Azure Data Box offre un moyen de transférer votre ligne de base de sauvegarde initiale vers Azure sans avoir besoin de bande passante supplémentaire. Cela est utile si la durée estimée du transfert de la ligne de base est supérieure à celle que vous pouvez tolérer. Vous pouvez utiliser l’estimateur de transfert de données lorsque vous créez un compte de stockage pour estimer le temps nécessaire au transfert de votre sauvegarde initiale.

![Affiche l’estimateur de transfert de données de stockage Azure dans le portail.](../media/az-storage-transfer.png)

N’oubliez pas que vous aurez besoin d’une capacité réseau suffisante pour prendre en charge les transferts de données quotidiens au sein de la fenêtre de transfert requise (fenêtre de sauvegarde) sans que cela ait un impact sur les applications de production. Cette section décrit les outils et techniques disponibles pour évaluer vos besoins en matière de réseau.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Déterminer la quantité de bande passante dont vous aurez besoin

Pour déterminer la quantité de bande passante dont vous aurez besoin, utilisez les ressources suivantes :

- Rapports de votre logiciel de sauvegarde.
- CommVault fournit des rapports standard permettant de déterminer le [taux de modification](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) et la [taille totale du jeu de sauvegarde](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) pour le transfert de base initial vers Azure.
- Sauvegardez des outils de création de rapports et d’évaluation indépendants du logiciel, tels que :
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Déterminer la bande passante Internet inutilisée

Il est important de connaître la quantité de bande passante généralement inutilisée (ou *marge*) dont vous disposez au quotidien. Cela vous permet d’évaluer si vous pouvez atteindre vos objectifs suivants :

- temps initial disponible pour le chargement quand vous n’utilisez pas Azure Data Box pour l’essaimage hors connexion ;
- exécution de sauvegardes quotidiennes en fonction du taux de modification identifié précédemment et de la fenêtre de sauvegarde.

Utilisez les méthodes suivantes pour identifier la marge de bande passante que vos sauvegardes sur Azure peuvent consommer.

- Si vous êtes un client Azure ExpressRoute, consultez votre [utilisation du circuit](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) dans le portail Azure.
- Contactez votre fournisseur de services Internet. Il doit être en mesure de partager des rapports montrant votre utilisation quotidienne et mensuelle.
- Il existe plusieurs outils capables de mesurer l’utilisation en surveillant votre trafic réseau au niveau du routeur/commutateur. notamment :
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Choisir les options de stockage appropriées

Quand vous utilisez Azure en tant que cible de sauvegarde, vous recourez au service [Stockage Blob Azure](../../../../blobs/storage-blobs-introduction.md). Le service Stockage Blob est une solution de stockage d’objets de Microsoft. Le stockage d’objets blob est optimisé pour stocker de grandes quantités de données non structurées ne suivant aucun modèle ni aucune définition. En outre, le service Stockage Azure est durable, hautement disponible, sécurisé et évolutif. Vous pouvez sélectionner le stockage approprié pour votre charge de travail afin d’offrir le [niveau de résilience](../../../../common/storage-redundancy.md) nécessaire pour honorer vos contrats de niveau de service (SLA) internes. Le service Stockage Blob est assorti d’un mode de paiement à l’utilisation. Vous êtes [facturé mensuellement](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) pour la quantité de données stockées, l’accès à celles-ci et, dans le cas des niveaux de stockage froid et archive, une période de rétention minimale requise. Les options de résilience et de hiérarchisation applicables aux données de sauvegarde sont résumées dans les tableaux suivants.

**Options de résilience du service Stockage Blob :**

|  |Localement redondant  |Redondant interzone  |Géoredondant  |Géoredondant interzone  |
|---------|---------|---------|---------|---------|
|**Nombre effectif de copies**     | 3         | 3         | 6         | 6 |
|**Nombre de zones de disponibilité**     | 1         | 3         | 2         | 4 |
|**Nombre de régions**     | 1         | 1         | 2         | 2 |
|**Basculement manuel vers une région secondaire**     | NON APPLICABLE         | N/A         | Oui         | Oui |

**Avec de stockage blob :**

|  | Niveau de stockage chaud   |Niveau de stockage froid   | Niveau de stockage archive |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilité** | 99,9 %         | 99 %         | Hors connexion      |
| **Frais d’utilisation** | Coûts de stockage supérieurs, coûts d’accès et de transaction inférieurs | Coûts de stockage inférieurs, coûts d'accès et de transaction supérieurs | Coûts de stockage les plus faibles, coûts d'accès et de transaction les plus élevés |
| **Conservation des données minimale requise**| NON APPLICABLE | 30 jours | 180 jours |
| **Latence (temps jusqu’au premier octet)** | Millisecondes | Millisecondes | Heures |

#### <a name="sample-backup-to-azure-cost-model"></a>Exemple de modèle de coût de sauvegarde sur Azure

Le paiement à l’utilisation peut être déconcertant pour des clients qui découvrent le cloud. Vous payez pour la capacité utilisée, ainsi que pour les transactions (lectures et écritures) et la [sortie des données](https://azure.microsoft.com/pricing/details/bandwidth/) relues dans votre environnement local quand [un service Azure ExpressRoute local direct ou un forfait de données illimitées ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/) sont utilisés, qui incluent une sortie de données d’Azure. Vous pouvez utiliser la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour effectuer une analyse de scénario. Vous pouvez baser l’analyse la liste des prix ou sur la [tarification de capacité de réserve de Stockage Azure](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md) qui permet de réaliser jusqu’à 38 % d’économies. Voici un exemple d’exercice de tarification pour modéliser le coût mensuel de la sauvegarde sur Azure. Ce n’est qu’un exemple. *Votre tarification peut varier en raison d’activités non capturées ici.*

|Facteur de coût  |Coût mensuel  |
|---------|---------|
|100 To de données de sauvegarde sur stockage froid     |1 556,48 USD         |
|2 To de nouvelles données écrites par jour x 30 jours     |39 USD pour les transactions          |
|Total mensuel estimé     |1 595,48 USD         |
|---------|---------|
|Restauration ponctuelle de 5 To localement via l’Internet public   | 491,26 USD         |

> [!NOTE]
> Cette estimation a été générée dans la Calculatrice de prix Azure à partir du tarif du paiement à l’utilisation dans la région USA Est, et est basée sur la taille de sous-segment par défaut de 32 Mo dans CommVault, qui génère 65 536 demandes Put (transactions d’écriture) par jour. Il se peut que cet exemple ne corresponde pas à vos besoins.

## <a name="implementation-guidance"></a>Conseils d’implémentation

Cette section constitue un bref guide pour l’ajout du service Stockage Azure à un déploiement local de Commvault. Pour obtenir des instructions détaillées et des considérations relatives à la planification, consultez le [Guide de l’architecture de cloud public CommVault pour Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Ouvrez le portail Azure et recherchez **comptes de stockage**. Vous pouvez également cliquer sur l’icône **comptes de stockage** par défaut.

    ![Montre l’ajout d’un compte de stockage dans le portail Azure.](../media/azure-portal.png)
  
    ![Montre l’emplacement où vous avez tapé le stockage dans la zone de recherche du portail Azure.](../media/locate-storage-account.png)

2. Sélectionnez **Créer** pour ajouter un compte. Sélectionnez ou créez un groupe de ressources, fournissez un nom unique, choisissez la région, sélectionnez les performances **Standard**, conservez le type de compte **Stockage V2**, puis choisissez le niveau de réplication correspondant à vos contrats SLA, ainsi que le niveau par défaut de votre logiciel de sauvegarde. Un compte de stockage Azure rend les niveaux de stockage chaud, froid et archive disponibles au sein d’un compte unique, et les stratégies de Commvault vous permettent d’utiliser plusieurs niveaux pour gérer efficacement le cycle de vie de vos données.

    ![Montre les paramètres de compte de stockage dans le portail.](../media/account-create-1.png)

3. Conservez les options de mise en réseau par défaut, puis accédez à **Protection des données**. Ici, vous pouvez choisir d’activer la suppression réversible qui vous permet de récupérer un fichier de sauvegarde supprimé par inadvertance au cours de la période de rétention définie, et vous offre une protection contre toute suppression accidentelle ou malveillante.

    ![Montre les paramètres de protection des données dans le portail.](../media/account-create-2.png)

4. Ensuite, nous recommandons les paramètres par défaut de l’écran **Avancé** pour les cas d’usage de la sauvegarde sur Azure.

    ![Montre l’onglet Paramètres avancés dans le portail.](../media/account-create-3.png)

5. Ajoutez des étiquettes pour l’organisation si vous utilisez un étiquetage, puis créez votre compte.

6. Deux étapes rapides suffisent désormais pour ajouter le compte à votre environnement Commvault. Accédez au compte que vous avez créé dans le portail Azure, puis, dans le menu **Service BLOB** dans le panneau du portail, sélectionnez **Conteneurs**. Ajoutez un conteneur et choisissez un nom explicite. Ensuite, sous **Paramètres**, accédez à l’élément **Clés d’accès** et copiez le **Nom du compte de stockage** ainsi que l’une des deux clés d’accès. Vous aurez besoin du nom du conteneur, du nom du compte et de la clé d’accès aux étapes suivantes.

    ![Montre la création de conteneur dans le portail.](../media/container.png)

    ![Montre les paramètres de clé d’accès dans le portail.](../media/access-key.png)

7. (*Facultatif*) Vous pouvez ajouter des couches de sécurité à votre déploiement.

    1. Configurez un accès en fonction du rôle afin de limiter les utilisateurs autorisés à apporter des modifications à votre compte de stockage. Pour plus d’informations, consultez [Rôles intégrés pour les opérations de gestion](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Limitez l’accès au compte à des segments de réseau spécifiques avec les [paramètres du pare-feu de stockage](../../../../common/storage-network-security.md) afin d’empêcher les tentatives d’accès depuis l’extérieur de votre réseau d’entreprise.

        ![Montre les paramètres du pare-feu de stockage dans le portail.](../media/storage-firewall.png)

    1. Définissez un [verrou de suppression](../../../../../azure-resource-manager/management/lock-resources.md) sur le compte pour empêcher la suppression accidentelle du compte de stockage.

        ![Montre la définition d’un verrou de suppression dans le portail.](../media/resource-lock.png)

    1. Configurez des [meilleures pratiques de sécurité](../../../../../storage/blobs/security-recommendations.md) supplémentaires.

1. Dans le centre de commandes CommVault, accédez à **Gérer** -> **Sécurité** -> **Gestionnaire d’informations d’identification**. Choisissez un **Compte Cloud**, un **Type de fournisseur** de **Stockage Microsoft Azure**, sélectionnez le **MediaAgent** chargé d’échanger des données avec Azure, puis ajoutez le nom du compte de stockage et la clé d’accès.

    ![Montre l’ajout d’informations d’identification dans le centre de commandes CommVault.](../media/commvault-credential.png)

9. Ensuite, accédez à **Stockage** -> **Cloud** dans le centre de commande CommVault. Choisissez **Ajouter**. Entrez un nom convivial pour le compte de stockage, puis, dans la liste **Type**, sélectionnez **Stockage Microsoft Azure**. Sélectionnez un serveur d’agent multimédia à utiliser pour transférer les sauvegardes vers le service Stockage Azure. Ajoutez le conteneur que vous avez créé, choisissez le niveau de stockage à utiliser dans le compte de stockage Azure, puis sélectionnez les informations d’identification créées à l’Étape 8. Enfin, choisissez s’il faut ou non transférer les sauvegardes dédupliquées, et un emplacement pour la base de données de déduplication.

     ![Capture d’écran de l’interface utilisateur cloud Ajouter de CommVault. Dans le menu déroulant Archive, **Archive** est sélectionné.](../media/commvault-add-storage.png)

10. Enfin, ajoutez votre nouvelle ressource Stockage Azure à un plan existant ou nouveau dans le centre de commandes CommVault via **Gérer** -> **Plans** en tant que destination de sauvegarde.

    ![Capture d’écran de l’interface utilisateur du centre de commande CommVault. Dans le volet de navigation de gauche, sous **Gérer**, **Plans** est sélectionné.](../media/commvault-plan.png)

11. *(Facultatif)* Si vous envisagez d’utiliser Azure comme site de récupération, ou CommVault pour migrer des serveurs et applications vers Azure, il est recommandé de déployer un proxy VSA dans Azure. Pour des instructions détaillées, consultez [cet article](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).

## <a name="operational-guidance"></a>Instructions d’utilisation

### <a name="azure-alerts-and-performance-monitoring"></a>Surveillance des alertes et des performances Azure

Il est recommandé de surveiller vos ressources Azure et la capacité de CommVault à les utiliser comme vous le feriez avec n’importe quelle cible de stockage dont vous dépendez pour stocker vos sauvegardes. Une combinaison de la surveillance d’Azure Monitor et du centre de commandes CommVault vous aide à maintenir l’intégrité de votre environnement.

#### <a name="azure-portal"></a>Portail Azure

Azure fournit une solution de surveillance robuste sous la forme d’[Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Vous pouvez [configurer Azure Monitor](../../../../blobs/monitor-blob-storage.md) pour suivre la capacité, les transactions, la disponibilité, l’authentification, et bien d’autres aspects du service Stockage Azure. Vous trouverez la référence complète des métriques collectées [ici](../../../../blobs/monitor-blob-storage-reference.md). Parmi les métriques utiles à suivre figurent BlobCapacity (pour s’assurer que vous restez en dessous de la [limite de capacité du compte de stockage](../../../../common/scalability-targets-standard-account.md)), Entrée et Sortie (pour suivre la quantité de données écrites et lues dans votre compte de stockage Azure), et SuccessE2ELatency (pour suivre le temps d’aller-retour des demandes échangées entre Stockage Azure et votre MediaAgent).

Vous pouvez également [créer des alertes de journal](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) pour suivre l’intégrité du service Stockage Azure et afficher le [tableau de bord d’état d’Azure](https://status.azure.com/status) à tout moment.

#### <a name="commvault-command-center"></a>Centre de commandes CommVault

- [Créer une alerte pour les pools de stockage cloud](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Pour plus d’informations sur l’emplacement où suivre les rapports de performances, l’achèvement des travaux et la résolution des problèmes de base, consultez [Tableaux de bord](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Comment ouvrir des cas d’usage

Quand vous avez besoin d’aide pour votre sauvegarde sur la solution Azure, vous devez ouvrir un cas de support auprès de CommVault et d’Azure. Cela aide nos organisations de support à collaborer si nécessaire.

#### <a name="to-open-a-case-with-commvault"></a>Pour ouvrir un cas de support auprès de CommVault

Sur le [site de support de CommVault](https://www.commvault.com/support), connectez-vous et ouvrez un cas de support.

Pour comprendre les options contractuelles de support disponibles, consultez [Options de support de CommVault](https://ma.commvault.com/support)

Vous pouvez également appeler pour ouvrir un cas ou contacter le support CommVault par e-mail :

- Numéro gratuit : +1 877-780-3077
- [Numéros de support dans le monde](https://ma.commvault.com/Support/TelephoneSupport)
- [E-mail du support CommVault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Pour ouvrir un cas de support auprès d’Azure

Dans le [portail Azure](https://portal.azure.com), recherchez **support** dans la barre de recherche en haut. Sélectionnez **Aide + support** -> **Nouvelle demande de support**.

> [!NOTE]
> Lors de l’ouverture d’un cas de support, spécifiez que vous avez besoin d’aide en lien avec le service Stockage Azure ou Azure Networking. Ne spécifiez pas Sauvegarde Azure. Sauvegarde Azure est le nom d’un service Azure et votre cas de support sera mal aiguillé.

### <a name="links-to-relevant-commvault-documentation"></a>Liens vers la documentation CommVault pertinente

Pour plus d’informations, consultez la documentation de CommVault suivante :

- [Guide de l’utilisateur de CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Guide de l’architecture Azure pour Commvault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Offres de la Place de marché

CommVault a facilité le déploiement de sa solution dans Azure pour protéger les machines virtuelles Azure, ainsi que de nombreux autres services Azure. Pour plus d’informations, consultez les références suivantes :

- [Déployer CommVault via la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Feuille de données CommVault pour Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Liste des services et fonctionnalités Azure pris en charge par CommVault](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Comment utiliser CommVault pour protéger SAP HANA dans Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les scénarios d’utilisation spécialisés, consultez ces ressources CommVault supplémentaires.

- [Utiliser CommVault pour migrer vos serveurs et applications vers Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Protéger SAP dans Azure avec CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Protéger Office 365 avec CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)