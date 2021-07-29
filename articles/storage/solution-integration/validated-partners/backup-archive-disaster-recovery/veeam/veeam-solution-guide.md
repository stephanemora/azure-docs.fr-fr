---
title: Sauvegarder vos données dans Azure avec Veeam
titleSuffix: Azure Storage
description: Donne une vue d’ensemble des facteurs à prendre en considération et des étapes à suivre pour utiliser Azure en tant que cible de stockage et emplacement de récupération pour Veeam Backup and Recovery
author: karauten
ms.author: karauten
ms.date: 05/12/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 334ae28c160a01032d5403e06f40846e8b9d9ed5
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839182"
---
# <a name="backup-to-azure-with-veeam"></a>Sauvegarde sur Azure avec Veeam

Cet article vous aide à intégrer une infrastructure Veeam avec le service Stockage Blob Azure. Il comprend des conditions préalables, des considérations, une implémentation et des conseils opérationnels. Cet article traite de l’utilisation d’Azure en tant que cible de sauvegarde hors site et site de récupération en cas d’incident empêchant le fonctionnement normal de votre site principal.

> [!NOTE]
> Veeam offre également une solution d’objectif de délai de récupération (RTO, Recovery Time objective) inférieure, la sauvegarde Veeam et la réplication avec prise en charge des charges de travail de solution VMware Azure. Celle-ci vous permet de disposer d’une machine virtuelle de secours qui peut vous aider à récupérer plus rapidement en cas d’incident dans un environnement de production Azure. Veeam offre également une restauration directe sur Microsoft Azure et d’autres outils dédiés pour sauvegarder les ressources Azure et Office 365. Ces fonctionnalités ne sont pas abordées dans ce document.

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme suivant présente une architecture de référence pour les déploiements de site local vers Azure et dans Azure.

![Schéma de l’architecture de référence Veeam vers Azure.](../media/veeam-architecture.png)

Votre déploiement Veeam existant peut facilement s’intégrer à Azure en ajoutant un compte de stockage Azure, ou plusieurs comptes en guise d’espace de stockage de sauvegarde dans le cloud. Veeam vous permet également de récupérer des sauvegardes à partir d’un site local dans Azure, ce qui vous permet de disposer d’un site de récupération à la demande dans Azure.

## <a name="veeam-interoperability-matrix"></a>Matrice d’interopérabilité de Veeam

| Charge de travail | GPv2 et Stockage Blob | Prise en charge du niveau de stockage froid | Prise en charge du niveau de stockage archive | Prise en charge de la gamme Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Machines virtuelles/données locales | v10a | v10a | N/A | 10a<sup>*</sup> |
| Machines virtuelles Azure | v10a | v10a | N/A | 10a<sup>*</sup> |
| Objets blob Azure | v10a | v10a | N/A | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | N/A | 10a<sup>*</sup> |

Veeam offrait une prise en charge des fonctionnalités Azure ci-dessus dans les versions plus anciennes de son produit. Pour une expérience optimale, il est recommander d’utiliser la dernière version du produit.

<sup>*</sup>La solution Veeam Backup and Replication prend en charge l’API REST uniquement pour Azure Data Box. Par conséquent, Azure Data Box Disk n’est pas pris en charge. Pour plus d’informations sur la prise en charge de Data Box, consultez [cette page](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=110).


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

Plusieurs options d’évaluation sont disponibles pour déterminer le taux de modification et la taille totale du jeu de sauvegarde pour le transfert de la base de référence initiale vers Azure. Voici quelques exemples d’outils d’évaluation et de création de rapports :

- [MiTrend](https://mitrend.com/)
- [Apt are](https://www.veritas.com/insights/aptare-it-analytics)
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
| **Conservation des données minimale requise** | N/D | 30 jours | 180 jours |
| **Latence (temps jusqu’au premier octet)** | Millisecondes | Millisecondes | Heures |

#### <a name="sample-backup-to-azure-cost-model"></a>Exemple de modèle de coût de sauvegarde sur Azure

Le paiement à l’utilisation peut être déconcertant pour des clients qui découvrent le cloud. Vous payez pour la capacité utilisée, ainsi que pour les transactions (lectures et écritures) et la [sortie des données](https://azure.microsoft.com/pricing/details/bandwidth/) relues dans votre environnement local quand [un service Azure ExpressRoute local direct ou un forfait de données illimitées ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/) sont utilisés, qui incluent une sortie de données d’Azure. Vous pouvez utiliser la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour effectuer une analyse de scénario. Vous pouvez baser l’analyse la liste des prix ou sur la [tarification de capacité de réserve de Stockage Azure](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md) qui permet de réaliser jusqu’à 38 % d’économies. Voici un exemple d’exercice de tarification pour modéliser le coût mensuel de la sauvegarde sur Azure. Ce n’est qu’un exemple. *Votre tarification peut varier en raison d’activités non capturées ici.*

|Facteur de coût  |Coût mensuel  |
|---------|---------|
|100 To de données de sauvegarde sur stockage froid     |1 556,48 USD         |
|2 To de nouvelles données écrites par jour x 30 jours     |42 $ en transactions          |
|Total mensuel estimé     |1 598,48 USD         |
|---------|---------|
|Restauration ponctuelle de 5 To localement via l’Internet public   | 527,26 USD         |

> [!Note]
> Cette estimation a été effectuée à l’aide de la Calculatrice de prix Azure en fonction du tarif du paiement à l’utilisation dans la région USA Est, et est basée sur la taille de bloc de 512 Ko par défaut de Veeam pour les transferts WAN. Il se peut que cet exemple ne corresponde pas à vos besoins.

## <a name="implementation-guidance"></a>Conseils d’implémentation

Cette section constitue un bref guide pour l’ajout du service Stockage Azure à un déploiement local de Veeam. Pour obtenir des instructions détaillées et des considérations relatives à la planification, nous vous recommandons d’examiner les instructions Veeam suivantes pour le [niveau de capacité](https://helpcenter.veeam.com/docs/backup/vsphere/capacity_tier.html?ver=110).

1. Ouvrez le portail Azure et recherchez **comptes de stockage**. Vous pouvez également cliquer sur l’icône de service par défaut.

      ![Montre l’ajout d’un compte de stockage dans le portail Azure.](../media/azure-portal.png)

      ![Montre l’emplacement où vous avez tapé le stockage dans la zone de recherche du portail Azure.](../media/locate-storage-account.png)

2. Sélectionnez **Créer** pour ajouter un compte. Sélectionnez ou créez un groupe de ressources, fournissez un nom unique, choisissez la région, sélectionnez les performances **Standard**, conservez le type de compte **Stockage V2**, puis choisissez le niveau de réplication correspondant à vos contrats SLA, ainsi que le niveau par défaut de votre logiciel de sauvegarde. Un compte de stockage Azure rend les niveaux de stockage chaud, froid et archive disponibles au sein d’un compte unique, et les stratégies de Veeam vous permettent d’utiliser plusieurs niveaux pour gérer efficacement le cycle de vie de vos données.

    ![Montre les paramètres de compte de stockage dans le portail.](../media/account-create-1.png)

3. Conservez pour l’instant les options de réseau et de protection des données par défaut. N’activez **pas** la suppression réversible pour les comptes de stockage qui stockent les niveaux de capacité Veeam.

 4. Ensuite, nous recommandons les paramètres par défaut de l’écran **Avancé** pour les cas d’usage de la sauvegarde sur Azure.

    ![Montre l’onglet Paramètres avancés dans le portail.](../media/account-create-3.png)

5. Ajoutez des étiquettes pour l’organisation si vous utilisez un étiquetage, puis créez votre compte.

6. Deux étapes rapides suffisent désormais pour ajouter le compte à votre environnement Veeam. Accédez au compte que vous avez créé dans le portail Azure, puis, dans le menu **Service BLOB** dans le panneau du portail, sélectionnez **Conteneurs**. Ajoutez un conteneur et choisissez un nom explicite. Ensuite, sous **Paramètres**, accédez à l’élément **Clés d’accès** et copiez le **Nom du compte de stockage** ainsi que l’une des deux clés d’accès. Vous aurez besoin du nom du conteneur, du nom du compte et de la clé d’accès aux étapes suivantes.

    ![Montre la création de conteneur dans le portail.](../media/container-b.png)

    ![Montre les paramètres de clé d’accès dans le portail.](../media/access-key.png)

    > [!Note]
    > La solution Veeam Backup and Replication offre d’autres options pour se connecter à Azure. Pour le cas d’usage évoqué dans cet article, qui utilise le service Stockage Blob Microsoft Azure en tant que cible de sauvegarde, l’utilisation de la méthode ci-dessus est la meilleure pratique recommandée.

7. (*Facultatif*) Vous pouvez ajouter des couches de sécurité à votre déploiement.

     1. Configurez un accès en fonction du rôle afin de limiter les utilisateurs autorisés à apporter des modifications à votre compte de stockage. Pour plus d’informations, consultez [Rôles intégrés pour les opérations de gestion](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Limitez l’accès au compte à des segments de réseau spécifiques avec les [paramètres du pare-feu de stockage](../../../../common/storage-network-security.md) afin d’empêcher les tentatives d’accès depuis l’extérieur de votre réseau d’entreprise.

        ![Montre les paramètres du pare-feu de stockage dans le portail.](../media/storage-firewall.png)

     1. Définissez un [verrou de suppression](../../../../../azure-resource-manager/management/lock-resources.md) sur le compte pour empêcher la suppression accidentelle du compte de stockage.

        ![Verrou de ressource](../media/resource-lock.png)

     1. Configurez des [meilleures pratiques de sécurité](../../../../../storage/blobs/security-recommendations.md) supplémentaires.

8. Dans la console de gestion de la sauvegarde et de la réplication de Veeam, accédez à **Backup Infrastructure (Espace de stockage de sauvegarde)** --> cliquez avec le bouton droit dans le volet de vue d’ensemble, puis sélectionnez **Add Backup Repository (Ajouter un espace de stockage de sauvegarde)** pour ouvrir l’Assistant Configuration. Dans la boîte de dialogue, sélectionnez **Object storage (Stockage d’objets)**  -> **Stockage Blob Microsoft Azure** -> **Stockage Blob Azure**.

    ![Affiche la sélection du stockage d’objets dans l’Assistant Veeam Repository (Espace de stockage Veeam).](../media/veeam-repo-a.png)

    ![Affiche la sélection de Stockage Blob Microsoft Azure dans l’Assistant Espace de stockage Veeam.](../media/veeam-repo-b.png)

    ![Affiche la sélection de Stockage Blob Azure dans l’Assistant Espace de stockage Veeam.](../media/veeam-repo-c.png)

9. Ensuite, spécifiez un nom et une description pour votre nouvel espace de stockage blob.

    ![Affiche la saisie d’un nom pour l’espace de stockage dans l’Assistant Espace de stockage Veeam.](../media/veeam-repo-d.png)

10. À l’étape suivante, ajoutez les informations d’identification pour accéder à votre compte de stockage Azure. Dans le gestionnaire d’informations d’identification cloud, sélectionnez **Compte de stockage Microsoft Azure**, puis entrez le nom et la clé d’accès de votre compte de stockage. Dans le sélecteur de région, sélectionnez **Azure Global** et un serveur de passerelle, le cas échéant.

    ![Indique la spécification d’un compte dans l’Assistant Espace de stockage Veeam.](../media/veeam-repo-e.png)

    > [!Note]
    > Si vous choisissez de ne pas utiliser un serveur de passerelle Veeam, assurez-vous que toutes les extensions d’espace de stockage pour la montée en puissance parallèle disposent d’un accès direct à Internet.

11. Dans le registre de conteneurs, choisissez votre conteneur de stockage Azure, puis sélectionnez ou créez un dossier dans lequel stocker vos sauvegardes. Vous pouvez également fixer une limite conditionnelle à la capacité de stockage globale utilisable par Veeam (recommandé). Examinez les informations affichées dans la section Résumé, puis exécutez l’outil de configuration. Vous pouvez maintenant sélectionner le nouvel espace de stockage dans la configuration de votre travail de sauvegarde.

    ![Indique la spécification d’un conteneur dans l’Assistant Espace de stockage Veeam.](../media/veeam-repo-f.png)

    ![Montre la création d’un dossier dans l’Assistant Espace de stockage Veeam.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Instructions d’utilisation

### <a name="azure-alerts-and-performance-monitoring"></a>Surveillance des alertes et des performances Azure

Il est recommandé de surveiller vos ressources Azure et la capacité de Veeam à les utiliser comme vous le feriez avec n’importe quelle cible de stockage dont vous dépendez pour stocker vos sauvegardes. Une combinaison des fonctionnalités de surveillance d’Azure Monitor et de Veeam (onglet **Statistiques** du nœud **Travaux** de la console de gestion Veeam, ou options plus avancées comme Veeam One Reporter) vous aidera à préserver l’intégrité de votre environnement.

#### <a name="azure-portal"></a>Portail Azure

Azure fournit une solution de surveillance robuste sous la forme d’[Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Vous pouvez [configurer Azure Monitor](../../../../blobs/monitor-blob-storage.md) pour suivre la capacité, les transactions, la disponibilité, l’authentification, et bien d’autres aspects du service Stockage Azure. Vous trouverez la référence complète des métriques suivies [ici](../../../../blobs/monitor-blob-storage-reference.md). Parmi les métriques utiles à suivre figurent BlobCapacity (pour s’assurer que vous restez en dessous de la [limite de capacité du compte de stockage](../../../../common/scalability-targets-standard-account.md)), Entrée et Sortie (pour suivre la quantité de données écrites et lues dans votre compte de stockage Azure), et SuccessE2ELatency (pour suivre le temps d’aller-retour des demandes échangées entre Stockage Azure et votre MediaAgent).

Vous pouvez également [créer des alertes de journal](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) pour suivre l’intégrité du service Stockage Azure et afficher le [tableau de bord d’état d’Azure](https://status.azure.com/status) à tout moment.

#### <a name="veeam-reporting"></a>Génération de rapports Veeam

- [Configurer Veeam One Reporting](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Alarmes de la solution Veeam Backup & Replication](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Comment ouvrir des cas d’usage

Quand vous avez besoin d’aide pour votre sauvegarde sur la solution Azure, vous devez ouvrir un cas de support auprès de Veeam et d’Azure. Cela aide nos organisations de support à collaborer si nécessaire.

#### <a name="to-open-a-case-with-veeam"></a>Pour ouvrir un cas de support auprès de Veeam

Sur le [site du support technique de Veeam](https://www.veeam.com/support.html), connectez-vous et ouvrez un cas de support.

Pour comprendre les options de support que Veeam met à votre disposition, consultez [Stratégie de support client de Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

Vous pouvez également téléphoner pour ouvrir un cas de support : [numéros du support dans le monde](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Pour ouvrir un cas de support auprès d’Azure

Dans le [portail Azure](https://portal.azure.com), recherchez **support** dans la barre de recherche en haut. Sélectionnez **Aide + support** -> **Nouvelle demande de support**.

> [!NOTE]
> Lors de l’ouverture d’un cas de support, spécifiez que vous avez besoin d’aide en lien avec le service Stockage Azure ou Azure Networking. Ne spécifiez pas Sauvegarde Azure. Sauvegarde Azure est le nom d’un service Azure et votre cas de support sera mal aiguillé.

### <a name="links-to-relevant-veeam-documentation"></a>Liens vers la documentation Veeam pertinente

Pour plus d’informations, consultez la documentation de Veeam suivante :

- [Guide de l’utilisateur de Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Guide d’architecture de Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Offres de la Place de marché

Vous pouvez continuer à utiliser la solution Veeam que vous connaissez et approuvez pour protéger vos charges de travail en cours d’exécution sur Azure. Veeam a facilité le déploiement de sa solution dans Azure et la protection des machines virtuelles Azure ainsi que de nombreux autres services Azure.

- [Déployer la solution Veeam Backup & Replication via la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Site web de Veeam sur la sauvegarde et la récupération Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les scénarios d’usage spécialisés, consultez les ressources suivantes sur le site web de Veeam :

- [Vidéos explicatives sur Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Documentations techniques sur Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Base de connaissances et FAQ sur Veeam](https://www.veeam.com/knowledge-base.html?ad=menu-resources)