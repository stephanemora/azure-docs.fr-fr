---
title: Demander une augmentation de quota
description: Cette page explique comment créer une demande de support pour augmenter les quotas d’instances managées, serveurs et bases de données uniques Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: fb576b81adeec99e4080c744749097390d1add1d
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110963"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Demander des augmentations de quota pour Azure SQL Database

Cet article explique comment demander une augmentation de quota pour les instances managées, serveurs et bases de données uniques Azure SQL Database. Il explique également comment activer l’accès d’un abonnement à une région.

## <a id="newquota"></a> Créer une demande de support

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

1. Dans la fenêtre **Détails**, sélectionnez **Fournir des détails** pour entrer des informations supplémentaires.

   ![Lien « Fournir des détails »](./media/quota-increase-request/provide-details-link.png)

Quand vous cliquez sur **Fournir des détails**, la fenêtre **Détails du quota** s’affiche. Elle vous permet d’ajouter des informations. Les sections suivantes décrivent les différentes options disponibles pour les types de quota **SQL Database** et **SQL Database Managed Instance**.

## <a id="sqldbquota"></a> Types de quota SQL Database

Les sections suivantes décrivent trois options d’augmentation de quota pour les types de quota **SQL Database** :

- Unités de transaction de base de données (DTU) par serveur
- Serveurs par abonnement
- Activer l’accès d’un abonnement à une région

### <a name="database-transaction-units-dtus-per-server"></a>Unités de transaction de base de données (DTU) par serveur

Suivez les étapes ci-après pour demander une augmentation du nombre de DTU par serveur.

1. Sélectionnez le type de quota **Unités de transaction de base de données (DTU) par serveur**.

1. Dans la liste **Ressource**, sélectionnez la ressource à cibler.

1. Dans le champ **Nouveau quota**, entrez la nouvelle limite de DTU demandée.

   ![Détails du quota de DTU](./media/quota-increase-request/quota-details-dtus.png)

Pour plus d’informations, consultez [Limites de ressources pour des bases de données uniques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-single-databases.md) et [Limites de ressources pour des pools élastiques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Serveurs par abonnement

Suivez les étapes ci-après pour demander une augmentation du nombre de serveurs par abonnement.

1. Sélectionnez le type de quota **Serveurs par abonnement**.

1. Dans la liste **Emplacement**, sélectionnez la région Azure à utiliser. Le quota s’entend par abonnement dans chaque région.

1. Dans le champ **Nouveau quota**, entrez le nombre maximal de serveurs demandé dans cette région.

   ![Détails du quota de serveurs](./media/quota-increase-request/quota-details-servers.png)

Pour en savoir plus, consultez [Limites de ressources SQL Database et gouvernance des ressources](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a>Activer l’accès d’un abonnement à une région

Certains types d’offre ne sont pas disponibles dans toutes les régions. Vous pouvez rencontrer une erreur telle que la suivante :

`This location is not available for subscription`

Si votre abonnement a besoin d’un accès à une région particulière, utilisez l’option **Autre demande de quota** pour demander l’accès. Dans votre demande, spécifiez les détails de l’offre et de la référence SKU à activer pour la région. Pour en savoir plus sur les options de l’offre et de la référence SKU, consultez [Tarification Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Autres détails de quota](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a> Type de quota SQL Server Managed Instance

Pour le type de quota **SQL Server Managed Instance**, effectuez les étapes suivantes :

1. Dans la liste **Région**, sélectionnez la région Azure à cibler.

1. Entrez les nouvelles limites demandées pour **Sous-réseau** et **vCore**.

   ![Détails du quota d’instances managées](./media/quota-increase-request/quota-details-managed-instance.png)

Pour plus d’informations, consultez [Vue d’ensemble des limites de ressources Azure SQL Database Managed Instance](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Soumettre votre requête

La dernière étape consiste à renseigner les détails restants de votre demande de quota SQL Database. Ensuite, sélectionnez **Next: Vérifier + créer >>** . Vérifiez les détails de la demande, puis cliquez sur **Créer** pour envoyer la demande.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre demande envoyée, elle est examinée. Vous recevrez une réponse basée sur les informations que vous avez fournies dans le formulaire.

Pour plus d’informations sur les autres limites d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-resource-manager/management/azure-subscription-service-limits.md).
