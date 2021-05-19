---
title: Sauvegarder vos données dans Azure avec DobiProtect
titleSuffix: Azure Storage
description: Donne une vue d’ensemble des facteurs à prendre en considération et des étapes à suivre pour utiliser Azure en tant que cible de stockage et emplacement de récupération pour DobiProtect
author: karauten
ms.author: karauten
ms.date: 04/12/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ef74136627f7f7e90e94b91f212438d8e9daf0d6
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489594"
---
# <a name="backup-to-azure-with-dobiprotect"></a>Sauvegarder sur Azure avec DobiProtect

Cet article vous aide à intégrer une infrastructure DobiProtect avec le service Stockage Blob Azure. Il comprend des conditions préalables, des considérations, une implémentation et des conseils opérationnels. Cet article traite de l’utilisation d’Azure en tant que cible de sauvegarde hors site et site de récupération en cas d’incident empêchant le fonctionnement normal de votre site principal.
DobiProtect couvre DobiSync et DobiReplicate. DobiSync vous permet de protéger les données en créant périodiquement une copie de celles-ci et en les stockant sur un serveur de stockage d’objets.  DobiReplicate vous permet de créer une copie d’objets ou de données à partir d’un objet ou d’un magasin principal attaché au réseau, dans un magasin secondaire et de maintenir la synchronisation des informations et des métadonnées associées. Dans le cadre de ce document, les instructions seront incluses uniquement pour DobiSync.

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme suivant présente une architecture de référence pour les déploiements de site local vers Azure et dans Azure.

![Architecture de référence de déploiement de DobiProtect vers Azure](../media/dobiprotect-diagram.jpg)

Vous pouvez facilement intégrer votre déploiement existant de DobiProtect avec Azure en ajoutant un ou plusieurs comptes de stockage Azure en guise de cible de stockage cloud. DobiProtect vous permet également de récupérer des sauvegardes à partir d’un site local dans Azure, ce qui vous permet de disposer d’un site de récupération à la demande dans Azure.

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
> Cette estimation a été générée dans la Calculatrice de prix Azure à partir du tarif du paiement à l’utilisation dans la région USA Est, et est basée sur la taille de sous-segment de 32 Mo, qui génère 65 536 demandes Put (transactions d’écriture) par jour. Cet exemple peut ne pas refléter la tarification actuelle d’Azure ou ne pas être applicable à vos besoins.

## <a name="implementation-guidance"></a>Conseils d’implémentation

Cette section constitue un bref guide pour l’ajout du service Stockage Azure à un déploiement local de DobiSync. 

1. Ouvrez le portail Azure et recherchez **comptes de stockage**. Vous pouvez également cliquer sur l’icône **comptes de stockage** par défaut.

    ![Montre l’ajout d’un compte de stockage dans le portail Azure.](../media/azure-portal.png)
  
    ![Montre l’emplacement où vous avez tapé le stockage dans la zone de recherche du portail Azure.](../media/locate-storage-account.png)

2. Sélectionnez **Créer** pour ajouter un compte. Sélectionnez ou créez un groupe de ressources, fournissez un nom unique, choisissez la région, sélectionnez les performances **Standard**, conservez le type de compte **Stockage V2**, puis choisissez le niveau de réplication correspondant à vos contrats SLA, ainsi que le niveau par défaut de votre logiciel de sauvegarde. Un compte de stockage Azure rend les niveaux de stockage chaud, froid et archive disponibles au sein d’un compte unique, et les stratégies de DobiSync vous permettent d’utiliser plusieurs niveaux pour gérer efficacement le cycle de vie de vos données.

    ![Montre les paramètres de compte de stockage dans le portail.](../media/account-create-1.png)

3. Conservez les options de mise en réseau par défaut, puis accédez à **Protection des données**. Ici, vous pouvez choisir d’activer la suppression réversible qui vous permet de récupérer un fichier de sauvegarde supprimé par inadvertance au cours de la période de rétention définie, et vous offre une protection contre toute suppression accidentelle ou malveillante.

    ![Montre les paramètres de protection des données dans le portail.](../media/account-create-2.png)

4. Ensuite, nous recommandons les paramètres par défaut de l’écran **Avancé** pour les cas d’usage de la sauvegarde sur Azure.

    ![Montre l’onglet Paramètres avancés dans le portail.](../media/account-create-3.png)

5. Ajoutez des étiquettes pour l’organisation si vous utilisez un étiquetage, puis créez votre compte.

6. Il ne vous reste plus que deux étapes rapides à suivre pour pouvoir ajouter le compte à votre environnement DobiSync. Accédez au compte que vous avez créé dans le portail Azure, puis, dans le menu **Service BLOB** dans le panneau du portail, sélectionnez **Conteneurs**. Ajoutez un conteneur et choisissez un nom explicite. Ensuite, sous **Paramètres**, accédez à l’élément **Clés d’accès** et copiez le **Nom du compte de stockage** ainsi que l’une des deux clés d’accès. Vous aurez besoin du nom du conteneur, du nom du compte et de la clé d’accès aux étapes suivantes.

    ![Montre la création de conteneur dans le portail.](../media/container-c.png)

    ![Montre les paramètres de clé d’accès dans le portail.](../media/access-key.png)

7. (*Facultatif*) Vous pouvez ajouter des couches de sécurité à votre déploiement.

    1. Configurez un accès en fonction du rôle afin de limiter les utilisateurs autorisés à apporter des modifications à votre compte de stockage. Pour plus d’informations, consultez [Rôles intégrés pour les opérations de gestion](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Limitez l’accès au compte à des segments de réseau spécifiques avec les [paramètres du pare-feu de stockage](../../../../common/storage-network-security.md) afin d’empêcher les tentatives d’accès depuis l’extérieur de votre réseau d’entreprise.

        ![Montre les paramètres du pare-feu de stockage dans le portail.](../media/storage-firewall.png)

    1. Définissez un [verrou de suppression](../../../../../azure-resource-manager/management/lock-resources.md) sur le compte pour empêcher la suppression accidentelle du compte de stockage.

        ![Montre la définition d’un verrou de suppression dans le portail.](../media/resource-lock.png)

    1. Configurez des [meilleures pratiques de sécurité](../../../../../storage/blobs/security-recommendations.md) supplémentaires.

1. Dans DobiSync, accédez à **Configuration** -> **Stockage d’objets** et ajoutez un stockage d’objets :

    ![Cela affiche une interface Nouveau stockage d’objets dans DobiSync.](../media/dobisync-new-storage.png)

9. Spécifiez le **nom** du **compte de stockage** et la **clé du compte** : 

     ![Capture d’écran de l’ajout d’informations d’identification d’objet blob Azure dans DobiSync](../media/dobisync-add-storage.jpg)

10. Spécifiez un nom pour la connexion au **stockage d’objets blob Azure** : ![ capture d’écran de l’interface utilisateur Détails de la cible de synchronisation DobiSync](../media/dobisync-target-details.jpg)
    

11. À l’étape suivante, vous verrez tous les **conteneurs d’objets blob** qui sont configurés sous ce **compte de stockage** particulier. Sélectionnez le protocole et les informations d’identification des conteneurs qui seront vos cibles de synchronisation, par exemple : 

    ![Capture d’écran de l’interface utilisateur des conteneurs DobiSync](../media/dobisync-container-list.jpg)
 
12. Affectez les proxys à cette connexion d’objet blob Azure, puis cliquez sur **Tester la connexion** pour vous assurer que les proxys sont en mesure de communiquer avec les conteneurs :

    ![Capture d’écran de l’interface utilisateur des conteneurs DobiSync vérifiés](../media/dobisync-container-checked.jpg)

13. Les **résultats du test** de connexion s’affichent : 

    ![Capture d’écran de l’interface utilisateur de test de connexion DobiSync](../media/dobisync-connection.jpg)

13. Cliquez sur « Terminer » pour finaliser la configuration de l’objet blob Azure. Vous pouvez ensuite lancer une nouvelle session de synchronisation.

## <a name="operational-guidance"></a>Instructions d’utilisation

#### <a name="azure-portal"></a>Portail Azure

Azure fournit une solution de surveillance robuste sous la forme d’[Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Vous pouvez [configurer Azure Monitor](../../../../blobs/monitor-blob-storage.md) pour suivre la capacité, les transactions, la disponibilité, l’authentification, et bien d’autres aspects du service Stockage Azure. Vous trouverez la référence complète des métriques collectées [ici](../../../../blobs/monitor-blob-storage-reference.md). Parmi les métriques utiles à suivre figurent BlobCapacity (pour s’assurer que vous restez en dessous de la [limite de capacité du compte de stockage](../../../../common/scalability-targets-standard-account.md)), Entrée et Sortie (pour suivre la quantité de données écrites et lues dans votre compte de stockage Azure), et SuccessE2ELatency (pour suivre le temps d’aller-retour des demandes échangées entre Stockage Azure et votre MediaAgent).

Vous pouvez également [créer des alertes de journal](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) pour suivre l’intégrité du service Stockage Azure et afficher le [tableau de bord d’état d’Azure](https://status.azure.com/status) à tout moment.

#### <a name="dobisync-documentation"></a>Documentation de DobiSync

Pour plus d’informations sur la configuration de DobiSync, consultez le [Manuel de l’utilisateur](https://downloads.datadobi.com/NAS/olh/latest/dobisync.html) de DobiSync.

### <a name="how-to-open-support-cases"></a>Comment ouvrir des cas d’usage

Quand vous avez besoin d’aide pour la sauvegarde vers la solution Azure, ouvrez un incident auprès de Datadobi et d’Azure. Cela aide nos organisations de support à collaborer si nécessaire.

#### <a name="to-open-a-case-with-datadobi"></a>Ouverture d’un incident auprès d’Azure

Sur le [Site de support de Datadobi](https://support.datadobi.com/s/), connectez-vous, puis ouvrez un incident.

#### <a name="to-open-a-case-with-azure"></a>Pour ouvrir un cas de support auprès d’Azure

Dans le [portail Azure](https://portal.azure.com), recherchez **support** dans la barre de recherche en haut. Sélectionnez **Aide + support** -> **Nouvelle demande de support**.

> [!NOTE]
> Lors de l’ouverture d’un cas de support, spécifiez que vous avez besoin d’aide en lien avec le service Stockage Azure ou Azure Networking. Ne spécifiez pas Sauvegarde Azure. Sauvegarde Azure est le nom d’un service Azure et votre cas de support sera mal aiguillé.

### <a name="links-to-relevant-datadobi-documentation"></a>Liens vers la documentation Datadobi pertinente

Pour plus d’informations, consultez la documentation de Datadobi suivante :

- [Guide des conditions préalables](https://downloads.datadobi.com/NAS/guides/latest/prerequisites.html)
- [Guide d’installation de DobiProtect](https://downloads.datadobi.com/NAS/guides/latest/installguide.html)

## <a name="next-steps"></a>Étapes suivantes

Datadobi a facilité le déploiement de sa solution dans Azure pour protéger les machines virtuelles Azure, ainsi que de nombreux autres services Azure. Pour en savoir plus, consultez le document de référence suivant :

- [Protéger les données de fichier dans Azure avec DobiProtect](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datadobi1602192408529.datadobi-dobiprotect?tab=overview)