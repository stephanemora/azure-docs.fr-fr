---
title: Migration de données de fichier vers Azure avec Datadobi DobiMigrate
titleSuffix: Azure Storage
description: Guide de prise en main pour implémenter Datadobi DobiMigrate et migrer des données vers Azure Files, Azure NetApp Files ou une solution NAS ISV.
author: dukicn
ms.author: nikoduki
ms.date: 04/27/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 6984230044dc70e6e5e05c7ae0dcb4789065c436
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955362"
---
# <a name="migrate-data-to-azure-with-datadobi-dobimigrate"></a>Migration de données vers Azure avec Datadobi DobiMigrate

Cet article vous aide à intégrer l’infrastructure Datadobi DobiMigrate avec le Stockage Azure. Il comprend des conditions préalables, des considérations, une implémentation et des conseils opérationnels.

DobiMigrate permet d’effectuer des migrations de fichiers et d’objets entre les plateformes de stockage. Il migre les données locales vers Azure rapidement, facilement et à moindre coût.

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme suivant présente une architecture de référence pour les déploiements de site local vers Azure et dans Azure.

:::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-reference-architecture.png" alt-text="Architecture de référence décrivant la configuration de base pour DobiMigrate.":::

Vous pouvez facilement intégrer votre déploiement DobiMigrate existant à Azure en ajoutant et en configurant une connexion Azure.

## <a name="before-you-begin"></a>Avant de commencer

Une petite planification initiale vous permet d’utiliser Azure en guise de cible de sauvegarde hors site et de site de récupération.

### <a name="get-started-with-azure"></a>Prise en main d’Azure

Microsoft offre une infrastructure à suivre pour vous aider à prendre en main Azure. Le [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) est une approche détaillée de la transformation numérique d’entreprise et un guide complet pour la planification d’une adoption du cloud en production. Le CAF contient un [guide de configuration d’Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) pas à pas pour vous aider à être opérationnel rapidement et en toute sécurité. Vous pouvez en trouver une version interactive dans le [portail Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Vous y trouverez des exemples d’architectures, des pratiques recommandées spécifiques pour le déploiement d’applications, et des ressources de formation gratuites pour vous aider à acquérir une expertise Azure.

### <a name="considerations-for-migrations"></a>Considérations relatives aux migrations

Plusieurs aspects sont importants lorsqu’il s’agit de migrer des données de fichier vers Azure. Avant de continuer, renseignez-vous sur les points suivants :

- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)
- Dernières fonctionnalités prises en charge par DobiMigrate dans la [matrice de comparaison des outils de migration](./migration-tools-comparison.md)

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

## <a name="implementation-guidance"></a>Conseils d’implémentation

Cette section constitue un guide rapide pour ajouter un partage de fichiers Azure à un déploiement local Azure DobiMigrate. 

1. Ouvrez le Portail Azure, puis recherchez **comptes de stockage**. 

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-locate-storage-account.png" alt-text="Montre l’emplacement où vous avez tapé le stockage dans la zone de recherche du portail Azure.":::

    Vous pouvez également cliquer sur l’icône **Comptes de stockage** par défaut.

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-portal.png" alt-text="Capture d’écran montrant comment ajouter un compte de stockage sur le Portail Azure.":::

2. Sélectionnez **Créer** pour ajouter un compte :
   1. **Créez** un groupe de ressources ou sélectionnez-en un.
   2. Donnez un nom unique à votre compte de stockage.
   3. Choisissez la région.
   4. Sélectionnez le niveau de performance **Standard** ou **Premium** en fonction de vos besoins. Si vous sélectionnez **Premium**, sélectionnez **Partages de fichiers** sous **Type de compte Premium**.
   5. Choisissez la **[Redondance](../../../common/storage-redundancy.md)** qui répond à vos exigences de protection des données.
   
   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-1.png" alt-text="Capture d’écran montrant les paramètres du compte de stockage sur le portail.":::

3. Ensuite, nous recommandons les paramètres par défaut de l’écran **Avancé**. Si vous migrez vers Azure Files, nous vous conseillons d’activer l’option **Partages de fichiers volumineux** si elle est disponible.

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-2.png" alt-text="Montre l’onglet Paramètres avancés dans le portail.":::

4. Conservez pour l’instant les options réseau par défaut, puis passez à **Protection des données**. Vous pouvez choisir d’activer la suppression réversible qui vous permet de récupérer des données supprimées par inadvertance au cours de la période de rétention définie. Elle offre une protection contre la suppression accidentelle et la suppression malveillante.

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-3.png" alt-text="Montre les paramètres de protection des données dans le portail.":::

5. Ajoutez des étiquettes pour l’organisation si vous utilisez un étiquetage, puis cliquez sur **Créer** pour créer votre compte.
 
6. Il ne vous reste plus que deux étapes rapides à suivre pour pouvoir ajouter le compte à votre environnement DobiMigrate. Accédez au compte que vous avez créé sur le Portail Azure, puis sélectionnez Partages de fichiers dans le menu Service Fichier. Ajoutez un partage de fichiers et choisissez un nom explicite. Ensuite, sous Paramètres, accédez à l’élément Clés d’accès et copiez le Nom du compte de stockage ainsi que l’une des deux clés d’accès.

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-access-key.png" alt-text="Montre les paramètres de clé d’accès dans le portail.":::

7. Accédez aux propriétés du partage de fichiers Azure et notez l’adresse URL. Elle sera nécessaire pour ajouter la connexion Azure à DobiMigrate :

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-files-endpoint.png" alt-text="Localisation du point de terminaison Azure Files.":::

8. (_Facultatif_) Vous pouvez ajouter des couches de sécurité supplémentaires à votre déploiement.
 
   1. Configurez un accès en fonction du rôle afin de limiter les utilisateurs autorisés à apporter des modifications à votre compte de stockage. Pour plus d’informations, consultez [Rôles intégrés pour les opérations de gestion](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
 
   2.  Limitez l’accès au compte à des segments de réseau spécifiques avec les [paramètres du pare-feu de stockage](../../../common/storage-network-security.md). Configurez les paramètres de pare-feu pour empêcher tout accès de l’extérieur de votre réseau d’entreprise.

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-storage-firewall.png" alt-text="Montre les paramètres du pare-feu de stockage dans le portail.":::

   3.  Définissez un [verrou de suppression](../../../../azure-resource-manager/management/lock-resources.md) sur le compte pour empêcher la suppression accidentelle du compte de stockage.

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-resource-lock.png" alt-text="Montre la définition d’un verrou de suppression dans le portail.":::

   4.  Configurez des [meilleures pratiques de sécurité](../../../blobs/security-recommendations.md) supplémentaires.

9.  Dans DobiMigrate, accédez à Configuration -> Serveurs de fichiers. Cliquez sur **Ajouter** pour ajouter le type de serveur de fichiers Microsoft Azure Files :

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-server-type.png" alt-text="Ajout du type de serveur Microsoft Azure Files.":::

10. Spécifiez le nom, les détails de la connexion Azure Files et les informations d’identification du compte de stockage :
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-connection-details.png" alt-text="Configuration des détails de la connexion Azure Files.":::

11. Affectez les proxys à la connexion Azure Files, puis cliquez sur **Tester la connexion** pour vérifier que les proxys peuvent communiquer avec Azure Files :
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-connection.png" alt-text="Détails du test de connexion.":::

    Les résultats du test de connexion s’affichent :

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-results.png" alt-text="Affichage des résultats du test de connexion.":::

12. Sous **Partages de migration SMB** figurent tous les partages de fichiers Azure provisionnés sous ce compte de stockage. Définissez **Mappage** sur **Manuel** pour les partages qui se trouvent dans l’étendue de votre migration, par exemple :
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobiprotect-azure-files-shares.png" alt-text="Affichage des partages disponibles.":::

13. Cliquez sur **Terminer** pour finaliser la configuration d’Azure Files. Vous pouvez maintenant lancer une nouvelle tâche de migration.

### <a name="start-a-new-migration"></a>Lancement d’une nouvelle migration

DobiMigrate permet de configurer une nouvelle migration en ajoutant manuellement des chemins de migration ou en utilisant l’importation en bloc. L’importation en bloc ajoute plusieurs migrations avec des options communes.

Pour lancer une nouvelle migration, procédez comme suit :

1. Cliquez sur le bouton **Nouvelle migration** sur le tableau de bord.
   
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-new-migration.png" alt-text="Lancement d’une nouvelle tâche de migration.":::

1. Sélectionnez la source et les chemins à migrer.

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-select-source.png" alt-text="Sélection de la source et des chemins à migrer.":::

1. Sélectionnez la **Destination**.
2. Vérifiez les protocoles, puis confirmez les options de migration.
3. Cliquez sur **Terminer** pour effectuer le processus de migration.

## <a name="support"></a>Support 

Quand vous avez besoin d’aide pour la migration vers la solution Azure, ouvrez un incident auprès de Datadobi et d’Azure.

### <a name="to-open-a-case-with-datadobi"></a>Ouverture d’un incident auprès d’Azure

Sur le [Site de support de Datadobi](https://support.datadobi.com/s/), connectez-vous, puis ouvrez un incident.

### <a name="to-open-a-case-with-azure"></a>Pour ouvrir un cas de support auprès d’Azure

Sur le [Portail Azure](https://portal.azure.com/), recherchez **support** dans la barre de recherche en haut. Sélectionnez **Aide + support** -> **Nouvelle demande de support**.

## <a name="marketplace"></a>Place de marché

Datadobi a facilité le déploiement de sa solution dans Azure pour protéger les machines virtuelles Azure, ainsi que de nombreux autres services Azure. Pour plus d’informations, consultez les références suivantes :

- [Migration de données de fichier vers Azure avec DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=overview)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez nos guides :

- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)
- [Manuel d’utilisation DobiMigrate](https://downloads.datadobi.com/NAS/olh/latest/dobimigrate.html)
- [Guide des prérequis de DobiMigrate](https://downloads.datadobi.com/NAS/guides/latest/prerequisites.html)
- [Guide d’installation de DobiMigrate](https://downloads.datadobi.com/NAS/guides/latest/installguide.html)