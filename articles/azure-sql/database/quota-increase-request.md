---
title: Demander une augmentation de quota
description: Cette page explique comment créer une demande de support pour augmenter les quotas Azure SQL Database et Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 06/04/2020
ms.openlocfilehash: a5530becdcbe150ddc7ab2608bdf964ac269ed99
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111409016"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Demander des augmentations de quota pour Azure SQL Database et SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Cet article explique comment demander une augmentation de quota pour Azure SQL Database et Azure SQL Managed Instance. Il explique également comment activer l’accès d’un abonnement à une région et comment demander l’activation d’un matériel spécifique dans une région.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Créer une demande de support

Suivez les étapes ci-après pour créer une demande de support à partir du portail Azure pour SQL Database.

1. Dans le menu [Portail Azure](https://portal.azure.com), sélectionnez **Aide + support**.

   ![Lien « Aide + support »](./media/quota-increase-request/help-plus-support.png)

1. Dans **Aide + support**, sélectionnez **Nouvelle demande de support**.

    ![Créer une demande de support](./media/quota-increase-request/new-support-request.png)

1. Pour **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)** .

   ![Sélectionner un type de problème](./media/quota-increase-request/select-quota-issue-type.png)

1. Pour l’option **Abonnement**, sélectionnez l’abonnement dont vous voulez augmenter le quota.

   ![Sélectionner un abonnement pour une augmentation du quota](./media/quota-increase-request/select-subscription-support-request.png)

1. Sous **Type de quota**, sélectionnez l’un des types de quota suivants :

   - **SQL Database** pour les quotas de bases de données uniques et de pools élastiques.
   - **SQL Database Managed Instance** pour les instances managées.

   Ensuite, sélectionnez **Next: Solutions >>** .

   ![Sélectionner un type de quota](./media/quota-increase-request/select-quota-type.png)

1. Dans la fenêtre **Détails**, sélectionnez **Entrer les détails** pour entrer des informations supplémentaires.

   ![Lien Entrer les détails](./media/quota-increase-request/provide-details-link.png)

Quand vous cliquez sur **Entrer les détails**, la fenêtre **Détails du quota** s’affiche. Elle vous permet d’ajouter des informations. Les sections suivantes décrivent les différentes options disponibles pour les types de quota **SQL Database** et **SQL Managed Instance**.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> Types de quota SQL Database

Les sections suivantes décrivent les options d’augmentation de quota pour les types de quota **SQL Database** :

- Unités de transaction de base de données (DTU) par serveur
- Serveurs par abonnement
- Accès à la région pour les abonnements ou un matériel spécifique

### <a name="database-transaction-units-dtus-per-server"></a>Unités de transaction de base de données (DTU) par serveur

Suivez les étapes ci-après pour demander une augmentation du nombre de DTU par serveur.

1. Sélectionnez le type de quota **Unités de transaction de base de données (DTU) par serveur**.

1. Dans la liste **Ressource**, sélectionnez la ressource à cibler.

1. Dans le champ **Nouveau quota**, entrez la nouvelle limite de DTU demandée.

   ![Détails du quota de DTU](./media/quota-increase-request/quota-details-dtus.png)

Pour plus d’informations, consultez [Limites de ressources pour des bases de données uniques suivant le modèle d’achat DTU](resource-limits-dtu-single-databases.md) et [Limites de ressources pour des pools élastiques suivant le modèle d’achat DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Serveurs par abonnement

Suivez les étapes ci-après pour demander une augmentation du nombre de serveurs par abonnement.

1. Sélectionnez le type de quota **Serveurs par abonnement**.

1. Dans la liste **Emplacement**, sélectionnez la région Azure à utiliser. Le quota s’entend par abonnement dans chaque région.

1. Dans le champ **Nouveau quota**, entrez le nombre maximal de serveurs demandé dans cette région.

   ![Détails du quota de serveurs](./media/quota-increase-request/quota-details-servers.png)

Pour en savoir plus, consultez [Limites de ressources SQL Database et gouvernance des ressources](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a>Activer l’accès d’un abonnement à une région

Certains types d’offre ne sont pas disponibles dans toutes les régions. Vous pouvez rencontrer une erreur telle que la suivante :

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Si votre abonnement a besoin d’un accès dans une région particulière, sélectionnez l’option **Accès à une région**. Dans votre demande, spécifiez les détails de l’offre et de la référence SKU à activer pour la région. Pour en savoir plus sur les options de l’offre et de la référence SKU, consultez [Tarification Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Sélectionnez le type de quota **Accès à une région**.

1. Dans la liste **Sélectionner un emplacement**, sélectionnez la région Azure à utiliser. Le quota s’entend par abonnement dans chaque région.

1. Entrez le **Modèle d’achat** et les détails sur la **Consommation prévue**.

   ![Demander l’accès à une région](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Demander l’activation d’un matériel spécifique dans une région

Si la génération de matériel que vous souhaitez utiliser n’est pas disponible dans votre région, vous pouvez en faire la demande en suivant les étapes suivantes. Pour plus d’informations sur les générations de matériel et la disponibilité régionale, consultez [générations de matériel pour SQL Database](./service-tiers-sql-database-vcore.md#hardware-generations) ou [générations de matériel pour SQL Managed instance](../managed-instance/service-tiers-managed-instance-vcore.md#hardware-generations).

1. Sélectionnez le type de quota **Autre demande de quota**.

1. Dans le champ **Description**, indiquez votre demande, y compris le nom de la génération de matériel et le nom de la région dans laquelle vous en avez besoin.

   ![Demander du matériel dans une nouvelle région](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>Soumettre votre requête

La dernière étape consiste à renseigner les détails restants de votre demande de quota SQL Database. Ensuite, sélectionnez **Next: Vérifier + créer >>** . Vérifiez les détails de la demande, puis cliquez sur **Créer** pour envoyer la demande.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre demande envoyée, elle est examinée. Vous recevrez une réponse basée sur les informations que vous avez fournies dans le formulaire.

Pour plus d’informations sur les autres limites d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../../azure-resource-manager/management/azure-subscription-service-limits.md).
