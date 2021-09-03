---
title: Analyse et migration de données de fichiers vers Azure avec Komprise Intelligent Data Manager
titleSuffix: Azure Storage
description: Guide de mise en route pour l’implémentation de Komprise Intelligent Data Manager. Il explique comment analyser une infrastructure de fichiers et migrer les données vers Azure Files, Azure NetApp Files, le Stockage Blob Azure ou n’importe quelle solution NAS ISV disponible.
author: dukicn
ms.author: nikoduki
ms.date: 05/20/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 97ad7067b598ad4aa03f46cc7124dcc05d7f2b42
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115694"
---
# <a name="analyze-and-migrate-to-azure-with-komprise"></a>Analyse et migration vers Azure avec Komprise

Cet article vous aide à intégrer l’infrastructure Komprise Intelligent Data Manager aux services de stockage Azure. Il comprend des considérations et des instructions d’implémentation relatives à l’analyse et à la migration des données.

Komprise fournit des données analytiques et des insights sur les systèmes de stockage NAS (Network-Attached Storage). Il permet de migrer des données vers des services de stockage Azure comme Azure Files, Azure NetApp Files, le Stockage Blob Azure ou une solution NAS ISV. En savoir plus sur les [solutions partenaires vérifiées pour le stockage principal et secondaire](../primary-secondary-storage/partner-overview.md).

Komprise est utile dans de nombreux cas d’utilisation différents :

- Analyse de données non structurées locales pour obtenir des insights sur la gestion, le déplacement, le positionnement, l’archivage, la protection et l’isolement des données
- Migration de données non structurées locales vers Azure Files, Azure NetApp Files ou une solution NAS ISV
- Copie ou archivage de données non structurées locales dans le Stockage Blob Azure avec accès natif
- Migration de solutions de stockage d’objets vers le Stockage Blob Azure

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme suivant présente une architecture de référence pour les déploiements de site local vers Azure et dans Azure.

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture.png" alt-text="Architecture de référence décrivant la configuration de base de Komprise Intelligent Data Manager":::

Komprise est une solution logicielle facile à déployer dans un environnement virtuel. Elle se compose des éléments suivants :
- **Director** : console d’administration de la grille Komprise. Elle permet de configurer l’environnement, d’effectuer un monitoring des activités, d’afficher des rapports et des graphes, et de définir des stratégies.
- **Observer** : gérez et analysez les partages, synthétisez les rapports, communiquez avec Director et gérez le trafic de données NFS.
- **Proxy** : simplifiez et accélérez le workflow de données SMB/CIFS, et changez facilement d’échelle pour répondre aux exigences de performances d’un environnement en expansion.

## <a name="before-you-begin"></a>Avant de commencer

Une planification initiale permet de migrer les données avec moins de risques.

### <a name="get-started-with-azure"></a>Prise en main d’Azure

Microsoft offre une infrastructure à suivre pour vous aider à prendre en main Azure. Le [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) est une approche détaillée de la transformation numérique d’entreprise et un guide complet pour la planification d’une adoption du cloud en production. Le CAF contient un [guide de configuration d’Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) pas à pas pour vous aider à être opérationnel rapidement et en toute sécurité. Vous pouvez en trouver une version interactive dans le [portail Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Vous y trouverez des exemples d’architectures, des pratiques recommandées spécifiques pour le déploiement d’applications, et des ressources de formation gratuites pour vous aider à acquérir une expertise Azure.

### <a name="considerations-for-migrations"></a>Considérations relatives aux migrations

Plusieurs aspects sont importants lorsqu’il s’agit de migrer des données de fichier vers Azure. Avant de continuer, renseignez-vous sur les points suivants :

- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)
- Dernières fonctionnalités prises en charge par Komprise Intelligent Data Management dans la [matrice de comparaison des outils de migration](./migration-tools-comparison.md)

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

## <a name="migration-planning-guide"></a>Guide de planification de migration

Komprise est simple à configurer et permet l’exécution simultanée de plusieurs migrations en trois étapes :

1.  Analysez vos données pour identifier les fichiers à migrer ou à archiver.
1.  Définissez des stratégies pour migrer, déplacer ou copier des données non structurées vers le Stockage Azure.
1.  Activez des stratégies qui déplacent automatiquement vos données.

La première étape est essentielle pour la recherche et le classement par ordre de priorité des données à migrer. L’analyse Komprise fournit les informations suivantes :

- Informations sur le temps d’accès pour identifier les éléments suivants :
  - Fichiers rarement consultés qui peuvent être mis en cache localement ou stockés sur un service de fichiers rapide
  - Données froides qui peuvent être archivées dans le Stockage Blob
- Informations sur les principaux utilisateurs, groupes et partages pour déterminer l’ordre de la migration et le groupe le plus concerné au sein de l’organisation afin d’évaluer l’impact sur l’activité
- Nombre de fichiers ou capacité par type de fichier pour déterminer le type de fichiers stockés et identifier les éventuelles possibilités de nettoyage du contenu, qui permettraient de réduire l’effort de migration et le coût du stockage cible
- Nombre de fichiers ou capacité par taille de fichier pour déterminer la durée de la migration, dans la mesure où la migration d’un grand nombre de petits fichiers prend plus de temps que la migration d’un petit nombre de fichiers volumineux

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-1.png" alt-text="Analyse par type de fichier et heure d’accès":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-shares.png" alt-text="Exemple d’analyse des partages":::

- Filtre de la capacité de requête personnalisée pour filtrer le jeu exact de fichiers en fonction de vos besoins spécifiques

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-custom.png" alt-text="Analyse de la requête personnalisée":::

## <a name="deployment-guide"></a>Guide de déploiement

Le service cible doit être déployé avant Komprise. Pour plus d’informations, consultez les pages suivantes :

- Guide pratique pour créer un [partage de fichiers Azure](../../../files/storage-how-to-create-file-share.md)
- Guide pratique pour créer un [volume SMB](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) ou une [exportation NFS](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md) dans Azure NetApp Files

La grille Komprise est déployée dans un environnement virtuel (Hyper-V, VMware, KVM) pour la vitesse, la scalabilité et la résilience. Vous pouvez également configurer l’environnement dans votre abonnement Azure avec la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management).

1. Ouvrez le Portail Azure, puis recherchez **comptes de stockage**. 

    :::image type="content" source="./media/komprise-quick-start-guide/azure-locate-storage-account.png" alt-text="Montre l’emplacement où vous avez tapé le stockage dans la zone de recherche du portail Azure.":::

    Vous pouvez également cliquer sur l’icône **Comptes de stockage** par défaut.

    :::image type="content" source="./media/komprise-quick-start-guide/azure-portal.png" alt-text="Capture d’écran montrant comment ajouter un compte de stockage sur le Portail Azure.":::

2. Sélectionnez **Créer** pour ajouter un compte :
   1. **Créez** un groupe de ressources ou sélectionnez-en un.
   2. Donnez un nom unique à votre compte de stockage.
   3. Choisissez la région.
   4. Sélectionnez le niveau de performance **Standard** ou **Premium** en fonction de vos besoins. Si vous sélectionnez **Premium**, sélectionnez **Partages de fichiers** sous **Type de compte Premium**.
   5. Choisissez la **[Redondance](../../../common/storage-redundancy.md)** qui répond à vos exigences de protection des données.
   
   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-1.png" alt-text="Capture d’écran montrant les paramètres du compte de stockage sur le portail.":::

3. Ensuite, nous recommandons les paramètres par défaut de l’écran **Avancé**. Si vous migrez vers Azure Files, nous vous conseillons d’activer l’option **Partages de fichiers volumineux** si elle est disponible.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-2.png" alt-text="Montre l’onglet Paramètres avancés dans le portail.":::

4. Conservez pour l’instant les options réseau par défaut, puis passez à **Protection des données**. Vous pouvez choisir d’activer la suppression réversible qui vous permet de récupérer des données supprimées par inadvertance au cours de la période de rétention définie. Elle offre une protection contre la suppression accidentelle et la suppression malveillante.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-3.png" alt-text="Montre les paramètres de protection des données dans le portail.":::

5. Ajoutez des étiquettes pour l’organisation si vous utilisez un étiquetage, puis cliquez sur **Créer** pour créer votre compte.
 
6. Il ne vous reste plus que deux étapes rapides à suivre pour pouvoir ajouter le compte à votre environnement Komprise. Accédez au compte que vous avez créé sur le Portail Azure, puis sélectionnez Partages de fichiers dans le menu Service Fichier. Ajoutez un partage de fichiers et choisissez un nom explicite. Ensuite, sous Paramètres, accédez à l’élément Clés d’accès et copiez le Nom du compte de stockage ainsi que l’une des deux clés d’accès. Si les clés ne s’affichent pas, cliquez sur **Afficher les clés**.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-access-key.png" alt-text="Montre les paramètres de clé d’accès dans le portail.":::

7. Accédez aux **Propriétés** du partage de fichiers Azure. Notez l’adresse URL. Elle sera nécessaire pour ajouter la connexion Azure au partage de fichiers cible Komprise :

   :::image type="content" source="./media/komprise-quick-start-guide/azure-files-endpoint.png" alt-text="Localisation du point de terminaison Azure Files.":::

8. (_Facultatif_) Vous pouvez ajouter des couches de sécurité supplémentaires à votre déploiement.
 
   1. Configurez un accès en fonction du rôle afin de limiter les utilisateurs autorisés à apporter des modifications à votre compte de stockage. Pour plus d’informations, consultez [Rôles intégrés pour les opérations de gestion](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
 
   2.  Limitez l’accès au compte à des segments de réseau spécifiques avec les [paramètres du pare-feu de stockage](../../../common/storage-network-security.md). Configurez les paramètres de pare-feu pour empêcher tout accès de l’extérieur de votre réseau d’entreprise.

       :::image type="content" source="./media/komprise-quick-start-guide/azure-storage-firewall.png" alt-text="Montre les paramètres du pare-feu de stockage dans le portail.":::

   3.  Définissez un [verrou de suppression](../../../../azure-resource-manager/management/lock-resources.md) sur le compte pour empêcher la suppression accidentelle du compte de stockage.

       :::image type="content" source="./media/komprise-quick-start-guide/azure-resource-lock.png" alt-text="Montre la définition d’un verrou de suppression dans le portail.":::

   4.  Configurez des [meilleures pratiques de sécurité](../../../blobs/security-recommendations.md) supplémentaires.

1.  **Téléchargez** l’appliance virtuelle Komprise Observer dans Director, déployez-la sur votre hyperviseur et configurez-la avec le réseau et le domaine. Director est fourni sous forme de service cloud géré par Komprise. Les informations nécessaires pour accéder à Director sont envoyées avec l’e-mail de bienvenue une fois la solution achetée.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-1.png" alt-text="Téléchargement de l’image appropriée pour Komprise Observer dans Director":::

1. Pour ajouter les partages à analyser et à migrer, deux options s’offrent à vous :
   1. **Découvrez** tous les partages de votre environnement de stockage en entrant les informations suivantes :
       - Plateforme du système NAS source
       - Nom d’hôte ou adresse IP
       - Nom d’affichage
       - Informations d’identification (pour les partages SMB)

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-2.png" alt-text="Indication du système NAS à découvrir":::

    1. **Spécifiez** un partage de fichiers en entrant les informations suivantes :
       - Informations sur le stockage
       - Protocol
       - Chemin d’accès
       - Nom d’affichage
       - Informations d’identification (pour les partages SMB)
  
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-3.png" alt-text="Indication des solutions NAS à découvrir":::

    Vous devez répéter cette étape pour ajouter d’autres partages sources et d’autres partages de destination. Pour ajouter Azure Files comme destination, vous devez indiquer les détails du compte de stockage Azure et du partage de fichiers :

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-1.png" alt-text="Sélection d’Azure Files comme service cible":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-2.png" alt-text="Saisie des détails d’Azure Files":::

## <a name="migration-guide"></a>Guide de migration

Komprise propose une migration dynamique, pendant laquelle les utilisateurs finaux et les applications ne sont pas perturbés et peuvent continuer à accéder aux données. Le processus de migration permet d’automatiser la migration des répertoires, des fichiers et des liens d’une source vers une destination. L’intégrité des données est vérifiée à chaque étape. Tous les attributs, toutes les autorisations et tous les contrôles d’accès de la source sont appliqués.

Pour configurer et exécuter une migration, procédez comme suit :

1. Connectez-vous à la console Komprise. Les informations nécessaires pour accéder à la console sont envoyées avec l’e-mail de bienvenue une fois la solution achetée.
1. Accédez à **Migrer** et cliquez sur **Ajouter une migration**.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-new-migrate.png" alt-text="Ajout d’une nouvelle tâche de migration":::

1. Ajoutez une tâche de migration en sélectionnant le partage source et le partage de destination concernés. Donnez un nom à la migration. Une fois la configuration effectuée, cliquez sur **Lancer la migration**. 
   
   :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-migration.png" alt-text="Indication des détails de la tâche de migration":::

   (_Facultatif_) Définissez si vous souhaitez conserver le temps d’accès et les listes de contrôle d’accès SMB sur la destination. Cette option dépend du service de fichiers source, du service de fichiers de destination et du protocole sélectionnés.

2. Une fois la migration commencée, vous pouvez accéder à **Migrer** pour suivre la progression.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-monitor-migrations.png" alt-text="Monitoring de toutes les tâches de migration":::

3. Une fois toutes les modifications migrées, exécutez une migration finale en cliquant sur **Actions** et en sélectionnant **Lancer l’itération finale**. Avant la migration finale, nous vous recommandons d’arrêter l’accès aux partages de fichiers sources ou de les déplacer en mode lecture seule (pour les utilisateurs et les applications). Cette étape permet d’éviter toute modification de la source.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-final-migration.png" alt-text="Dernière migration avant le basculement":::

    Une fois la migration finale terminée, basculez tous les utilisateurs et toutes les applications vers le partage de destination. Le passage au nouveau service de fichiers implique généralement de modifier la configuration des serveurs DNS et DFS ou le remplacement des points de montage par la nouvelle destination. 

4. La dernière étape consiste à marquer la migration comme terminée.

## <a name="support"></a>Support

Pour ouvrir un incident auprès de Komprise, connectez-vous au [site de support Komprise](https://komprise.freshdesk.com/).

## <a name="marketplace"></a>Marketplace

Retrouvez la description de Komprise sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, différentes ressources sont disponibles :

- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)
- Fonctionnalités prises en charge par Komprise Intelligent Data Management dans la [matrice de comparaison des outils de migration](./migration-tools-comparison.md)
- [Matrice de compatibilité de Komprise](https://www.komprise.com/partners/microsoft-azure/)
