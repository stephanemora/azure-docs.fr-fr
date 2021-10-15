---
title: Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel à l’aide de l’interface utilisateur
description: Découvrez comment utiliser l’interface utilisateur d’Azure Data Factory Studio pour joindre un runtime d’intégration Azure-SSIS à un réseau virtuel Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d02a5d1025e470d8751d87816cbcc6395be41593
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403169"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-data-factory-studio-ui"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel à l’aide de l’interface utilisateur d’Azure Data Factory Studio

Cette section vous montre comment joindre un runtime d’intégration Azure-SSIS existant à un réseau virtuel (classique ou Azure Resource Manager) à l’aide du portail Azure et de l’interface utilisateur d’Azure Data Factory Studio. 

Vous devez configurer le réseau virtuel de façon appropriée avant d’y joindre votre runtime d’intégration Azure SSIS IR. Effectuez les étapes de la section appropriée en fonction du type de votre réseau virtuel (classique ou Azure Resource Manager). Ensuite, effectuez les étapes de la troisième section pour joindre votre runtime d’intégration Azure SSIS IR au réseau virtuel. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurer un réseau virtuel Azure Resource Manager

Utilisez le portail pour configurer un réseau virtuel Azure Resource Manager avant de tenter d’y joindre un runtime d’intégration Azure-SSIS IR.

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, seuls les navigateurs web prennent en charge l’interface utilisateur. 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels**. 

1. Filtrez et sélectionnez votre réseau virtuel dans la liste. 

1. Dans la page **Réseau virtuel**, sélectionnez **Propriétés**. 

1. Sélectionnez le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau virtuel dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers. 

1. Dans le menu de gauche, sélectionnez **Sous-réseaux**. Vérifiez que le nombre d’adresses disponibles est supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS IR. 

1. Vérifiez que le fournisseur Azure Batch est bien inscrit dans l’abonnement Azure qui contient le réseau virtuel. Si ce n’est pas le cas, inscrivez le fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch. (Si vous créez le runtime d’intégration Azure SSIS dans le portail Data Factory, le fournisseur Azure Batch est inscrit automatiquement pour vous.) 

   1. Dans le menu de gauche du Portail Azure, sélectionnez **Abonnements**. 

   1. Sélectionnez votre abonnement. 

   1. À gauche, sélectionnez **Fournisseurs de ressources**, puis confirmez que **Microsoft.Batch** est un fournisseur inscrit. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Confirmation de l’état &quot;Inscrit&quot;":::

   Si **Microsoft.Batch** ne figure pas dans la liste, il vous faut l’inscrire. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite. 

## <a name="configure-a-classic-virtual-network"></a>Configurer un réseau virtuel classique

Utilisez le portail pour configurer un réseau virtuel classique avant de tenter d’y joindre un runtime d’intégration Azure-SSIS. 

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, seuls les navigateurs web prennent en charge l’interface utilisateur. 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels (classiques)** . 

1. Filtrez et sélectionnez votre réseau virtuel dans la liste. 

1. Dans la page **Réseau virtuel (classique)** , sélectionnez **Propriétés**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="ID de ressource de réseau virtuel classique":::

1. Sélectionnez le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau classique dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers. 

1. Dans le menu de gauche, sélectionnez **Sous-réseaux**. Vérifiez que le nombre d’adresses disponibles est supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS IR. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="Nombre d’adresses disponibles sur le réseau virtuel":::

1. Joignez **MicrosoftAzureBatch** au rôle **Contributeur de machines virtuelles classiques** pour le réseau virtuel. 

   1. Dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** , puis sélectionnez l’onglet **Attributions de rôles**. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="Boutons &quot;Contrôle d’accès&quot; et &quot;Ajouter&quot;":::

   1. Sélectionnez **Ajouter une attribution de rôle**.

   1. Dans la page **Ajouter une attribution de rôle**, sélectionnez **Contributeur de machines virtuelles classiques** pour **Rôle**. Dans la zone **Sélectionner**, collez **ddbf3205-c6bd-46ae-8127-60eb93363864**, puis sélectionnez **Microsoft Azure Batch** dans la liste des résultats de recherche. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="Résultats de la recherche dans la page &quot;Ajouter une attribution de rôle&quot;":::

   1. Sélectionnez **Enregistrer** pour enregistrer les paramètres et fermer la page. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="Enregistrer les paramètres d’accès":::

   1. Confirmez que **Microsoft Azure Batch** apparaît bien dans la liste des contributeurs. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Confirmer l’accès à Azure Batch":::

1. Vérifiez que le fournisseur Azure Batch est bien inscrit dans l’abonnement Azure qui contient le réseau virtuel. Si ce n’est pas le cas, inscrivez le fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch. (Si vous créez le runtime d’intégration Azure SSIS dans le portail Data Factory, le fournisseur Azure Batch est inscrit automatiquement pour vous.) 

   1. Dans le menu de gauche du Portail Azure, sélectionnez **Abonnements**. 

   1. Sélectionnez votre abonnement. 

   1. À gauche, sélectionnez **Fournisseurs de ressources**, puis confirmez que **Microsoft.Batch** est un fournisseur inscrit. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Confirmation de l’état &quot;Inscrit&quot;":::

   Si **Microsoft.Batch** ne figure pas dans la liste, il vous faut l’inscrire. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite. 

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel

Après avoir configuré votre réseau virtuel Azure Resource Manager ou votre réseau virtuel classique, vous pouvez joindre le runtime d’intégration Azure-SSIS IR au réseau virtuel :

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, seuls les navigateurs web prennent en charge l’interface utilisateur. 

1. Dans le menu de gauche du [Portail Azure](https://portal.azure.com), sélectionnez **Fabriques de données**. Si vous ne voyez pas **Fabriques de données** dans le menu, sélectionnez **Autres services**, puis sélectionnez **Fabriques de données** dans la section **INTELLIGENCE + ANALYSE**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="Liste de fabriques de données":::

1. Dans la liste, sélectionnez votre fabrique de données avec Azure-SSIS IR. La page d’accueil de votre fabrique de données apparaît. Sélectionnez la vignette **Créer et surveiller**. L’interface utilisateur de Data Factory apparaît sous un onglet séparé. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="Page d’accueil Data Factory":::

1. Dans l’interface utilisateur de Data Factory, basculez vers l’onglet **Modifier**, sélectionnez **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="Onglet &quot;Runtimes d’intégration&quot;":::

1. Si votre runtime d’intégration Azure-SSIS IR est en cours d’exécution, dans la liste **Runtimes d’intégration**, au niveau de la colonne **Actions**, sélectionnez le bouton **Arrêter** pour votre runtime d’intégration Azure-SSIS IR. Vous ne pouvez pas modifier un runtime d’intégration Azure-SSIS IR tant que vous ne l’arrêtez pas. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="Arrêter le runtime d’intégration":::

1. Dans la liste **Runtimes d’intégration**, au niveau de la colonne **Actions**, sélectionnez le bouton **Modifier** pour votre runtime d’intégration Azure-SSIS IR. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="Modifier le runtime d’intégration":::

1. Dans le panneau de configuration du runtime d’intégration, passez les sections **Paramètres généraux** et **Paramètres SQL** en sélectionnant le bouton **Suivant**. 

1. Dans la section **Paramètres avancés** : 

   1. Activez la case à cocher **Sélectionner un réseau virtuel auquel joindre votre Azure-SSIS Integration Runtime, autoriser ADF à créer certaines ressources réseau, et éventuellement apporter vos propres adresses IP publiques statiques**. 

   1. Dans **Abonnement**, sélectionnez l’abonnement Azure possédant votre réseau virtuel.

   1. Pour **Emplacement**, sélectionnez le même emplacement que celui de votre runtime d’intégration.

   1. Pour **Type**, sélectionnez le type de votre réseau virtuel : classique ou Azure Resource Manager. Nous vous recommandons de sélectionner un réseau virtuel Azure Resource Manager, car les réseaux virtuels classiques seront bientôt dépréciés.

   1. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre réseau virtuel. Il doit être identique à celui utilisé pour SQL Database avec des points de terminaison de service de réseau virtuel ou SQL Managed Instance avec un point de terminaison privé pour héberger le catalogue SSISDB. Il peut également s’agir de celui connecté à votre réseau local. Sinon, vous pouvez utiliser n’importe quel réseau virtuel pour apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR.

   1. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau de votre réseau virtuel. Il doit être identique à celui utilisé pour SQL Database avec des points de terminaison de service de réseau virtuel pour héberger le catalogue SSISDB. Il peut également s'agir d'un autre sous-réseau que celui utilisé pour SQL Managed Instance avec un point de terminaison privé pour héberger le catalogue SSISDB. Sinon, vous pouvez utiliser n’importe quel sous-réseau pour apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR.

   1. Activez la case à cocher **Apporter des adresses IP publiques statiques pour votre Azure-SSIS Integration Runtime** pour choisir d’apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR, de manière à pouvoir les autoriser sur le pare-feu pour vos sources de données.

      Si vous cochez la case, effectuez les étapes suivantes.

      1. Pour **Première adresse IP publique statique**, sélectionnez la première adresse IP publique statique qui [répond aux exigences](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP) de votre Azure-SSIS IR. Si vous n’en avez pas, cliquez sur le lien **Créer** pour créer des adresses IP publiques statiques sur le portail Azure, puis cliquez sur le bouton Actualiser du portail pour pouvoir les sélectionner.
      
      1. Pour **Deuxième adresse IP publique statique**, sélectionnez la deuxième adresse IP publique statique qui [répond aux exigences](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP) de votre Azure-SSIS IR. Si vous n’en avez pas, cliquez sur le lien **Créer** pour créer des adresses IP publiques statiques sur le portail Azure, puis cliquez sur le bouton Actualiser du portail pour pouvoir les sélectionner.

   1. Sélectionnez **Validation de réseau virtuel**. Si la validation réussit, sélectionnez **Continuer**. 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Paramètres avancés avec un réseau virtuel":::

1. Dans la section **Résumé**, passez en revue tous les paramètres de votre runtime d’intégration Azure-SSIS IR. Sélectionnez ensuite **Mettre à jour**.

1. Démarrez votre Azure-SSIS IR en sélectionnant le bouton **Démarrer** situé dans la colonne **Actions** en regard de votre Azure-SSIS IR. Le démarrage de votre Azure-SSIS IR qui joint un réseau virtuel prend 20 à 30 minutes environ. 

## <a name="next-steps"></a>Étapes suivantes
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel – Présentation](join-azure-ssis-integration-runtime-virtual-network.md)
- [Détails de la configuration de réseau virtuel pour un runtime d’intégration Azure-SSIS](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel à l’aide d’Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 
- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](./tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise Azure SQL Database pour héberger le catalogue SSIS. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’Azure SQL Database avec des points de terminaison de service de réseau virtuel ou SQL Managed Instance dans un réseau virtuel pour héberger le catalogue SSIS. Il explique comment joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment obtenir des informations sur votre runtime d’intégration Azure-SSIS IR. Il fournit des descriptions sur l’état des informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out du runtime d’intégration Azure-SSIS en lui ajoutant des nœuds.
