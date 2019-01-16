---
title: Démarrage rapide - Créer un serveur Analysis Services à l’aide du portail Azure | Microsoft Docs
description: Apprenez à créer une instance de serveur Analysis Services dans Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4e9080cfc863b4253a4eb4d427f21df74bb08874
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186734"
---
# <a name="quickstart-create-a-server---portal"></a>Démarrage rapide : Créer un serveur - Portail

Ce démarrage rapide explique comment créer une ressource de serveur Analysis Services dans votre abonnement Azure à l’aide du portail.

## <a name="prerequisites"></a>Prérequis 

* **Abonnement Azure** : Visitez [Version d’évaluation gratuite d’Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pour créer un compte.
* **Azure Active Directory** : Votre abonnement doit être associé à un locataire Azure Active Directory. Vous devez également être connecté à Azure avec un compte dans cette instance d’Azure Active Directory. Pour en savoir plus, consultez [Authentification et autorisations utilisateur](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure. 

[Connectez-vous au portail](https://portal.azure.com)


## <a name="create-a-server"></a>Créer un serveur

1. Cliquez sur **+ Créer une ressource** > **Analytique** > **Analysis Services**.

    ![Portail](./media/analysis-services-create-server/aas-create-server-portal.png)

2. Dans **Analysis Services**, renseignez les champs requis, puis cliquez sur **Créer**.
   
   * **Nom du serveur** : Tapez un nom unique utilisé pour se référencer au serveur.
   * **Abonnement**: Sélectionnez l’abonnement à associer à ce serveur.
   * **Groupe de ressources** : Créez un groupe de ressources ou sélectionnez un groupe existant. Ces groupes de ressources sont conçus pour vous aider à gérer une collection de ressources Azure. Pour en savoir plus, consultez les [groupes de ressources](../azure-resource-manager/resource-group-overview.md).
   * **Emplacement** : Cet emplacement du centre de données Azure héberge le serveur. Choisissez l’emplacement le plus proche de votre plus grande base d’utilisateurs.
   * **Niveau de tarification** : Sélectionnez un niveau tarifaire. Si vous effectuez des tests et envisagez d’installer l’exemple de base de données model, sélectionnez le niveau gratuit **D1**. Pour en savoir plus, voir [Tarification d’Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administrateur** : Par défaut, il s’agit du compte avec lequel vous êtes connecté. Vous pouvez choisir un autre compte de votre annuaire Azure Active Directory.
   * **Paramètres du stockage de sauvegarde** : facultatif. Si vous possédez déjà un [compte de stockage](../storage/common/storage-introduction.md), vous pouvez le spécifier en tant que compte par défaut pour la sauvegarde de la base de données model. Vous pouvez également spécifier les paramètres de [sauvegarde et restauration](analysis-services-backup.md) ultérieurement.
   * **Date d’expiration de la clé de stockage** : facultatif. Spécifiez une période d’expiration pour la clé de stockage.

La création du serveur prend généralement moins d’une minute. Si vous avez sélectionné **Add to Portal** (Ajouter au portail), accédez au portail pour voir votre nouveau serveur. Ou, accédez à **Tous les services** > **Analysis Services** pour voir si votre serveur est prêt.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez votre serveur. Dans la page **Vue d’ensemble** de votre serveur, cliquez sur **Supprimer**. 

 ![Nettoyage](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris à créer un serveur dans votre abonnement Azure. Maintenant que vous avez serveur, vous pouvez le sécuriser en configurant un pare-feu de serveur (facultatif). Vous pouvez également ajouter un modèle de données d’exemple de base à votre serveur directement à partir du portail. Un exemple de modèle permet d’en savoir plus sur la configuration des rôles de base de données de modèle et le test des connexions client. Pour en savoir plus, passez au tutoriel sur l’ajout d’un exemple de modèle.

> [!div class="nextstepaction"]
> [Démarrage rapide : Configurer un pare-feu de serveur - Portail](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Didacticiel : Ajouter un exemple de modèle à votre serveur](analysis-services-create-sample-model.md)
