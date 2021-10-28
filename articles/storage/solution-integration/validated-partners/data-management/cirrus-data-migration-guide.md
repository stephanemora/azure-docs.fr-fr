---
title: Migrer vos données de blocs vers Azure à l’aide de Cirrus Data
titleSuffix: Azure Storage
description: Fournit un guide de démarrage rapide pour implémenter Cirrus Migrate Cloud et migrer vos données vers Azure
author: dukicn
ms.author: nikoduki
ms.date: 09/06/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: b9e56df2b4643fabdbdcf0d00baa6fe63c0d9e95
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263227"
---
# <a name="migrate-your-block-data-to-azure-with-cirrus-migrate-cloud"></a>Migrer vos données de blocs vers Azure à l’aide de Cirrus Migrate Cloud

Cirrus Migrate Cloud (CMC) permet la migration de disques d’un système de stockage existant ou d’un cloud vers Azure. La migration est effectuée alors que le système d’origine est toujours en fonctionnement. Ce document présente la méthodologie permettant de réussir la configuration et l’exécution de la migration.

## <a name="overview"></a>Vue d’ensemble

La solution utilise des agents de migration distribués exécutés sur chaque hôte et permettant des connexions directes d’hôte à hôte. Chaque migration d’hôte à hôte est indépendante, ce qui rend la solution évolutive à l’infini, sans goulots d’étranglement centraux pour le flux de données. La migration utilise la technologie cMotion™ pour garantir l’absence d’impact sur la production. 

## <a name="use-cases"></a>Cas d'utilisation

Ce document décrit un cas de migration générique visant à déplacer l’application d’une machine virtuelle (exécutée localement ou dans un autre fournisseur de cloud) vers une machine virtuelle dans Azure. Pour obtenir des guides plus détaillés, étape par étape, dans différents cas d’usage, vous pouvez consulter les liens suivants :

- [Moving the workload to Azure with cMotion](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-on-premises-to-azure-1xo3nuf/) (Déplacement de la charge de travail vers Azure à l’aide de cMotion)
- [Moving from Premium Disks to Ultra Disks](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-between-azure-tiers-sxhppt/) (Déplacement de disques Premium vers des disques Ultra)
- [Moving from AWS to Azure](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-from-aws-to-azure-weegd9/.) (Déplacement d’AWS vers Azure)

## <a name="components"></a>Composants

Cirrus Migrate Cloud est constitué de plusieurs composants :

- La fonctionnalité **cMotion™** de CMC effectue un basculement au niveau du stockage d’une source vers le cloud cible sans temps d’arrêt de l’hôte source. cMotion™ est utilisée pour transférer la charge de travail du disque source FC ou iSCSI d’origine vers le nouveau disque managé Azure de destination.
- **Portail de gestion web** est un service de gestion basé sur le web. Il permet aux utilisateurs de gérer la migration et de protéger tout stockage de blocs. Portail de gestion web fournit des interfaces pour toutes les configurations d’application, la gestion et les tâches administratives de CMC.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-web-portal.jpg" alt-text="Capture d’écran du portail CMC":::

## <a name="implementation-guide"></a>Guide d’implémentation

L’utilisateur doit suivre les meilleures pratiques d’Azure pour implémenter une nouvelle machine virtuelle. Si vous n’êtes pas familiarisé avec le processus, consultez le [guide de démarrage rapide](../../../../virtual-machines/windows/quick-create-portal.md) pour en savoir plus.

Avant de commencer la migration, assurez-vous que les conditions préalables suivantes ont été remplies :

- Vérifiez que le système d’exploitation dans Azure est correctement concédé sous licence.
- Vérifiez l’accès à la machine virtuelle Azure.
- Vérifiez que la licence d’application ou de base de données peut être exécutée dans Azure.
- Vérifiez l’autorisation d’allouer automatiquement la taille du disque de destination.
- Assurez-vous que le disque managé est de la même taille ou d’une taille supérieure à celle du disque source. 
- Assurez-vous que la source ou la machine virtuelle de destination dispose d’un port ouvert pour permettre notre connexion H2H.

1. **Préparez la machine virtuelle Azure**. Le document suppose que la machine virtuelle est entièrement implémentée. Ainsi, une fois les disques de données migrés, l’hôte de destination peut immédiatement démarrer l’application et la mettre en ligne. L’état des données sera le même que celui de la source lorsqu’elle a été arrêtée quelques secondes auparavant. CMC ne migre pas le disque du système d’exploitation de la source vers la destination.

1. **Préparez l’application dans la machine virtuelle Azure**. Dans cet exemple, la source est un hôte Linux. Elle peut exécuter toute application utilisateur accédant au stockage BSD correspondant. Nous utiliserons une application de base de données exécutée au niveau de la source en utilisant un disque de 1 Gio comme périphérique de stockage source. Cependant, toute application peut être utilisée à la place. Configurez une machine virtuelle dans Azure prête à être utilisée comme machine virtuelle de destination. Vérifiez que la configuration des ressources et le système d’exploitation sont compatibles avec l’application et qu’ils sont prêts à recevoir la migration de la source à l’aide du portail CMC. Le ou les périphériques de stockage de blocs de destination seront automatiquement alloués et créés pendant le processus de migration.

1. **Inscrivez-vous pour obtenir un compte CMC**. Pour obtenir un compte CMC, suivez les instructions exactes sur la façon d’obtenir un compte sur la page de support. Vous trouverez plus d’informations [ici](https://support.cirrusdata.cloud/en/article/licensing-m4lhll/).

1. **Créez un projet de migration** reflétant les caractéristiques spécifiques de la migration, le type, le propriétaire de la migration et tous les détails nécessaires pour définir les opérations. 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-create-project.jpg" alt-text="Capture d’écran de la création d’un nouveau projet":::

1. **Définissez les paramètres du projet de migration**. Utilisez le portail web de CMC pour configurer la migration en définissant les paramètres : source, destination et autres paramètres.

1. **Installez les agents CMC de migration sur les hôtes source et de destination**. À l’aide du portail de gestion web de CMC, sélectionnez **Deploy Cirrus Migrate Cloud** (Déployer Cirrus Migrate Cloud) pour obtenir la commande curl de **New Installation** (Nouvelle installation). Exécutez la commande sur l’interface de ligne de commande de la source et de la destination.

1. **Créez une connexion bidirectionnelle entre les hôtes source et de destination**. Utilisez l’onglet **H2H** dans le portail de gestion web de CMC et le bouton **Create New Connection** (Créer une nouvelle connexion). Sélectionnez l’appareil utilisé par l’application, et non l’appareil utilisé par le système d’exploitation Linux.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-1.jpg" alt-text="Capture d’écran montrant la liste des hôtes déployés":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-2.jpg" alt-text="Capture d’écran montrant la liste des connexions hôte à hôte":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-3.jpg" alt-text="Capture d’écran montrant la liste des appareils migrés":::

1. **Démarrez la migration vers la machine virtuelle de destination** à l’aide de **Migrate Host Volumes** (Migrer les volumes d’hôtes) à partir du portail de gestion web de CMC. Suivez les instructions relatives à l’emplacement distant. Utilisez le portail de CMC pour allouer automatiquement des volumes de destination (**Auto allocate destination volumes**) à droite de l’écran. 
 
1. Ensuite, nous devons ajouter les informations d’identification Azure pour permettre la connectivité et l’approvisionnement des disques en utilisant l’onglet **Integrations** (Intégrations) du portail de CMC. Remplissez les champs obligatoires à l’aide des valeurs de votre entreprise privée pour Azure : **Integration Name** (Nom de l’intégration), **Tenant ID** (ID du locataire), **Client/Application ID** (ID du client/de l’application) et **Secret**. Appuyez sur **Enregistrer**. 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-4.jpg" alt-text="Capture d’écran montrant la saisie des informations d’identification Azure":::

    Pour plus d’informations sur la création de l’application Azure AD, consultez nos [instructions étape par étape](https://support.cirrusdata.cloud/en/article/creating-an-azure-service-account-for-cirrus-data-cloud-tw2c9n/). En créant et en inscrivant l’application Azure AD pour CMC, vous activez la création automatique de disques managés Azure sur la machine virtuelle cible.

    >[!NOTE]
    >Dans la mesure où vous avez sélectionné **Auto allocate destination volumes** (Allouer automatiquement des volumes de destination) à l’étape précédente, n’appuyez pas à nouveau sur cette option pour une nouvelle allocation. Si vous le faites, il y aura une sortie et une erreur. Appuyez plutôt sur **Continue** (Continuer).

## <a name="migration-guide"></a>Guide de migration

Après avoir cliqué sur **Save** (Enregistrer) à l’étape précédente, la fenêtre **New Migration Session** (Nouvelle session de migration) apparaît. Renseignez les champs :
   - **Session description** (Description de la session) : fournissez une description explicite
   - **Auto Resync Interval** (Intervalle de resynchronisation automatique) : activez la planification de la migration 
   - Utilisez iQoS pour sélectionner l’impact de la migration sur la production :
     - **Minimum** : limitation minimale du taux de migration à 25 % de la bande passante disponible
     - **Moderate** : limitation modérée du taux de migration à 50 % de la bande passante disponible
     - **Aggressive** : limitation agressive du taux de migration à 75 % de la bande passante disponible
     - **Relentless** (Incessant) ne limite pas la migration.

       :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-iqos.jpg" alt-text="Capture d’écran montrant les options des paramètres iQoS":::

Appuyez sur **Create Session** (Créer une session) pour démarrer la migration.

Dès le début de la synchronisation initiale de la migration jusqu’au démarrage de cMotion, l’utilisateur n’a pas besoin d’interagir avec CMC.  La seule exception est le suivi de la progression. Vous pouvez surveiller l’état actuel et les volumes de session et suivre les changements à l’aide du tableau de bord. 

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-1.jpg" alt-text="Capture d’écran montrant le suivi de la progression":::

Pendant la migration, vous pouvez observer les blocs modifiés sur l’appareil source en appuyant sur le mappage des données modifiées.  

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-2.jpg" alt-text="Capture d’écran montrant le mappage des données modifiées":::

Les détails d’iQoS affichent les blocs synchronisés et l’état de la migration. iQoS montre également qu’il n’y a pas d’impact sur les E/S de production.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-3.jpg" alt-text="Capture d’écran montrant les détails d’iQoS":::

## <a name="moving-the-workload-to-azure-with-cmotion"></a>Déplacement de la charge de travail vers Azure à l’aide de cMotion

Une fois la synchronisation initiale terminée, nous allons nous préparer à déplacer la charge de travail du disque source vers le disque managé Azure de destination à l’aide de cMotion™.

### <a name="start-cmotion"></a>Démarrer cMotion™

À ce stade, les systèmes sont prêts pour le basculement de la migration par cMotion™. 

1. Dans le portail de CMS, sélectionnez **Trigger cMotion™** (Déclencher cMotion™) à l’aide de la session pour basculer la charge de travail du disque source au disque de destination. Pour vérifier si le processus a été effectué, vous pouvez utiliser iostat ou une commande équivalente. Accédez au terminal de la machine virtuelle Azure, exécutez *iostat/dev/<device_name>* (par exemple, /dev/sdc) et notez que les E/S sont écrites par l’application sur le disque de destination dans le cloud Azure.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-4.jpg" alt-text="Capture d’écran montrant l’état actuel de la supervision":::

Dans cet état, la charge de travail peut être basculée ou redéplacée vers le disque source à tout moment. Si vous voulez rétablir la machine virtuelle de production, utilisez le bouton **Session Actions** (Actions de session) et sélectionnez l’option **Revert cMotion™** (Rétablir cMotion™). Nous pouvons basculer dans un sens ou dans l’autre autant de fois que nous le voulons tant que l’application s’exécute sur l’hôte ou la machine virtuelle source.

Lorsque le basculement final vers la machine virtuelle de destination est nécessaire, procédez comme suit :
1. Sélectionnez **Session Actions** (Actions de session).
2. Cliquez sur l’option **Finalize Cutover** (Finaliser le basculement) pour « verrouiller » le basculement vers la nouvelle machine virtuelle Azure, et désactivez l’option de suppression du disque source. Arrêtez toute autre application en cours d’exécution dans l’hôte source pour le basculement final de l’hôte. 

### <a name="move-the-application-to-the-destination-virtual-machine"></a>Déplacer l’application vers la machine virtuelle de destination

Une fois le basculement terminé, l’application doit être basculée vers la nouvelle machine virtuelle. Pour ce faire, procédez comme suit :

1. Arrêtez l’application.
2. Démontez l’appareil migré.
3. Montez le nouvel appareil migré dans la machine virtuelle Azure. 
4. Démarrez la même application dans la machine virtuelle Azure sur le nouveau disque migré. 
 
Observez qu’il n’y a pas d’E/S circulant vers les périphériques des hôtes sources en exécutant la commande iostat dans l’hôte source. L’exécution de la commande iostat dans la machine virtuelle Azure montrera que des E/S s’exécutent sur le terminal de la machine virtuelle Azure.

### <a name="complete-the-migration-session-in-cmc-gui"></a>Terminer la session de migration dans l’interface graphique utilisateur de CMC 

L’étape de migration est terminée lorsque toutes les E/S ont été redirigées vers les périphériques de destination après le déclenchement de cMotion™. Vous pouvez maintenant fermer la session à l’aide de **Session Actions** (Actions de session). Cliquez sur **Delete Session** (Supprimer la session) pour fermer la session de migration. En guise de dernière étape, vous allez supprimer les **agents Cirrus Migrate Cloud** à la fois de l’hôte source et de la machine virtuelle Azure. Pour effectuer la désinstallation, récupérez la **commande Uninstall curl** à partir du bouton **Deploy Cirrus Migrate Cloud** (Déployer Cirrus Migrate Cloud). L’option se trouve dans la section **Hosts** (Hôtes) du portail. 

Une fois les agents supprimés, la migration est entièrement terminée. À présent, l’application source s’exécute en production sur la machine virtuelle Azure de destination avec des disques montés localement.    

## <a name="support"></a>Support

### <a name="how-to-open-a-case-with-azure"></a>Comment ouvrir un cas de support auprès d’Azure

Dans le [portail Azure](https://portal.azure.com), recherchez support dans la barre de recherche en haut. Sélectionnez **Aide + support** -> **Nouvelle demande de support**.

### <a name="engaging-cirrus-support"></a>Faire appel au support de Cirrus

Dans le portail de CMC, sélectionnez l’onglet **Help Center** (Centre d’aide) pour contacter le support de Cirrus Data Solutions, ou accédez au [site web de CDSI](https://support.cirrusdata.cloud/en/) et soumettez une demande de support.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Machines virtuelles Azure](../../../../virtual-machines/windows/overview.md)
- En savoir plus sur [Disques managés Azure](../../../../virtual-machines/managed-disks-overview.md)
- En savoir plus sur la [migration du stockage](../../../common/storage-migration-overview.md)
- [Site web de Cirrus Data](https://www.cirrusdata.com/)
- Guides étape par étape pour [cMotion](https://support.cirrusdata.cloud/en/category/howtos-1un623w/)