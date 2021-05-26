---
title: Créer une base de données avec le registre activé
description: Créez une base de données dans Azure SQL Database avec le registre activé en utilisant le portail Azure.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: quickstart
author: JasonMAnderson
ms.author: janders
ms.reviewer: vanto
ms.date: 05/25/2021
ms.openlocfilehash: d03d104a5b0d9ad9d83cbecdeeab8614cf0ee728
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386364"
---
# <a name="quickstart-create-an-azure-sql-database-with-ledger-enabled"></a>Démarrage rapide : créer une base de données Azure SQL Database avec le registre activé

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le registre Azure SQL Database est actuellement en **préversion publique**.

Dans ce démarrage rapide, vous allez créer une [base de données de registre](ledger-overview.md#ledger-database) dans Azure SQL Database, et configurer un [stockage de synthèse automatique avec le service Stockage Blob Azure](ledger-digest-management-and-database-verification.md#automatic-generation-and-storage-of-database-digests) en utilisant le portail Azure. Pour plus d’informations sur le registre, consultez [Registre Azure SQL Database](ledger-overview.md).

## <a name="prerequisite"></a>Configuration requise

- Un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).

## <a name="create-a-ledger-database-and-configure-digest-storage"></a>Créer une base de données de registre et configurer un stockage de synthèse

Créez une base de données de registre dans le [niveau de calcul serverless](serverless-tier-overview.md), et configurez le chargement de synthèses de registre dans un compte de stockage Azure.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour créer une base de données dans le portail Azure, ce démarrage rapide commence dans la page Azure SQL.

1. Accédez à la page [Sélectionner l’option de déploiement SQL](https://portal.azure.com/#create/Microsoft.AzureSQL).

1. Sous **Bases de données SQL**, laissez **Type de ressource** défini sur **Base de données unique**, puis sélectionnez **Créer**.

   ![Ajouter à Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. Sous l’onglet **De base** du formulaire **Créer une base de données SQL**, sous **Détails du projet**, sélectionnez l’**Abonnement** Azure souhaité.

1. Pour **Groupe de ressources**, sélectionnez **Créer**, entrez *myResourceGroup*, puis sélectionnez **OK**.

1. Pour **Nom de la base de données**, entrez *démo*.

1. Pour **Serveur**, sélectionnez **Créer**, puis remplissez le formulaire **Nouveau serveur** avec les valeurs suivantes :
   - **Nom du serveur** : entrez *mysqlserver* et ajoutez quelques caractères pour l’unicité. Nous ne pouvons pas fournir un nom de serveur exact à utiliser, car les noms de serveur doivent être globalement uniques pour tous les serveurs dans Azure, et non pas seulement dans un abonnement. Par conséquent, entrez un nom tel que mysqlserver12345. Le portail vous indique s’il est disponible ou non.
   - **Connexion administrateur au serveur** : entrez *azureuser*.
   - **Mot de passe** : entrez un mot de passe qui répond aux exigences, puis réentrez-le dans le champ **Confirmer le mot de passe**.
   - **Emplacement** : sélectionnez un emplacement dans la liste déroulante.
   - Sélectionnez l’option **Autoriser les services Azure à accéder à ce serveur** pour activer l’accès au stockage de synthèse.
   
   Sélectionnez **OK**.
   
1. Laissez **Vous souhaitez utiliser un pool élastique SQL ?** avec la valeur **Non**.

1. Sous **Calcul + stockage**, sélectionnez **Configurer la base de données**.

1. Comme ce guide de démarrage rapide utilise une base de données serverless, sélectionnez **Serverless**, puis **Appliquer**. 

      ![configurer une base de données serverless](./media/single-database-create-quickstart/configure-database.png)

1. Sous l’onglet **Réseau**, pour **Méthode de connectivité**, sélectionnez **Point de terminaison public**.
1. Pour **Règles de pare-feu**, affectez la valeur **Oui** à **Ajouter l’adresse IP actuelle du client**. Laissez **Autoriser les services et les ressources Azure à accéder à ce serveur** avec la valeur **Non**.
1. Sélectionnez **Suivant : Sécurité** en bas de la page.

   :::image type="content" source="media/ledger/ledger-create-database-networking-tab.png" alt-text="Onglet Mise en réseau de Créer une base de données dans le portail Azure":::

1. Sous l’onglet **Sécurité**, dans la section **Registre**, sélectionnez l’option **Configurer un registre**.

    :::image type="content" source="media/ledger/ledger-configure-ledger-security-tab.png" alt-text="Configurer un registre sous l’onglet Sécurité du portail Azure":::

1. Dans le volet **Configurer un registre**, dans la section **Registre**, activez la case à cocher **Activer pour toutes les futures tables dans cette base de données**. Ce paramètre garantit que toutes les futures tables dans la base de données seront des tables de registre, ce qui signifie que toutes les données dans la base de données seront infalsifiables. Par défaut, les nouvelles tables seront créées en tant que tables de registre pouvant être mises à jour, même si vous ne spécifiez pas `LEDGER = ON` dans [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql). Vous pouvez également laisser cette option désactivée, ce qui vous oblige à activer la fonctionnalité de registre pour chaque table lors de la création de tables à l’aide de Transact-SQL.

1. Dans la section **Stockage de synthèse**, l’option **Activer le stockage de synthèse automatique** sera automatiquement sélectionnée. Cela aura pour effet de créer un compte de stockage Azure et un conteneur dans lequel vos synthèses seront stockées.

1. Cliquez sur le bouton **Appliquer**.

    :::image type="content" source="media/ledger/ledger-configure-ledger-pane.png" alt-text="Configurer le volet du registre dans le portail Azure":::

1. Au bas de la page, sélectionnez **Vérifier + créer** :

    :::image type="content" source="media/ledger/ledger-review-security-tab.png" alt-text="Examiner et créer une base de données de registre sous l’onglet Sécurité du portail Azure":::

1. Dans la page **Vérifier + créer**, après vérification, sélectionnez **Créer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Conservez le groupe de ressources, le serveur et la base de données unique pour passer aux étapes suivantes et découvrir comment utiliser la fonctionnalité de registre de votre base de données avec différentes méthodes.

Lorsque vous avez terminé d’utiliser ces ressources, vous pouvez supprimer le groupe de ressources que vous avez créé, ce qui supprimera également le serveur et la base de données unique qu’il contient.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour supprimer **myResourceGroup** et toutes ses ressources à l’aide du portail Azure :

1. Dans le portail Azure, recherchez puis sélectionnez **Groupes de ressources**, puis sélectionnez **myResourceGroup** dans la liste.
1. Sur la page Groupe de ressources, sélectionnez **Supprimer un groupe de ressources**.
1. Sous **Tapez le nom du groupe de ressources**, entrez *myResourceGroup*, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Connectez-vous à votre base de données et interrogez ses données à l’aide de différents outils et langages :

- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md)
- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md) 
