---
title: Création et gestion d’un serveur Azure Database pour MySQL à l’aide du portail Azure
description: Cet article explique comment créer rapidement un serveur Azure Database pour MySQL et gérer le serveur avec le portail Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fdcb302d3a14b02ea86fb92c8dbf822ef3f42177
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142298"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Création et gestion d’un serveur Azure Database pour MySQL à l’aide du portail Azure
Cette rubrique explique comment créer rapidement un nouveau serveur Azure Database pour MySQL. Il comprend également des informations sur la gestion du serveur à l’aide du Portail Azure, notamment l’affichage des détails du serveur et des bases de données, la réinitialisation du mot de passe, la mise à l’échelle des ressources et la suppression du serveur.

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure.
Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL
Suivez ces étapes pour créer un serveur Azure Database pour MySQL nommé « mydemoserver ».

1. Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2. Sur la page Nouveau, sélectionnez **Bases de données**, puis, sur la page Bases de données, sélectionnez **Azure Database pour MySQL**.

    > Un serveur Azure Database pour MySQL est créé avec un ensemble défini de [ressources de calcul et de stockage](./concepts-pricing-tiers.md). La base de données est créée dans un groupe de ressources Azure et dans un serveur Azure Database pour MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Remplissez le formulaire Azure Database pour MySQL avec les informations suivantes :

    | **Champ de formulaire** | **Description du champ** |
    |----------------|-----------------------|
    | *Nom du serveur* | mydemoserver (le nom du serveur est globalement unique) |
    | *Abonnement* | mysubscription (sélectionnez dans le menu déroulant) |
    | *Groupe de ressources* | myresourcegroup (créez un nouveau groupe de ressources ou utilisez un groupe existant) |
    | *Sélectionner une source* | Vide (créez un serveur MySQL vide) |
    | *Connexion d’administrateur du serveur* | myadmin (nom du compte administrateur de l’installation) |
    | *Mot de passe* | définissez le mot de passe du compte administrateur |
    | *Confirmer le mot de passe* | confirmez le mot de passe du compte administrateur |
    | *Lieu* | Asie Sud-Est (choisissez entre Europe Nord et USA Ouest) |
    | *Version* | 5.7 (choisissez la version du serveur Azure Database pour MySQL) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances de votre nouveau serveur. Sélectionnez l’onglet **Usage général**. *Gen 5*, *2 vCores*, *5 Go* et *7 jours* sont les valeurs par défaut pour **Génération de calcul**, **vCore**, **Stockage** et la **Période de conservation des sauvegardes**. Vous pouvez laisser ces curseurs en l’état. Pour activer les sauvegardes de votre serveur dans le stockage géoredondant, sélectionnez **Géographiquement redondant** dans les **Options de redondance de sauvegarde**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Cliquez sur **Créer** pour approvisionner le serveur. Le provisionnement prend quelques minutes.

    > Sélectionnez l’option **Épingler au tableau de bord** pour faciliter le suivi de vos déploiements.

## <a name="update-an-azure-database-for-mysql-server"></a>Mise à jour d’un serveur Azure Database pour MySQL
Une fois le nouveau serveur provisionné, l’utilisateur dispose de plusieurs options pour configurer le serveur existant, notamment réinitialiser le mot de passe administrateur, changer le niveau tarifaire, et effectuer un scale-up ou un scale-down du serveur en changeant les cœurs virtuels ou le stockage.

### <a name="change-the-administrator-user-password"></a>Modification du mot de passe administrateur
1. Dans **Vue d’ensemble** du serveur, cliquez sur **Réinitialiser le mot de passe** pour afficher la fenêtre de réinitialisation du mot de passe.

   ![Vue d’ensemble](./media/howto-create-manage-server-portal/overview.png)

2. Entrez un nouveau mot de passe et confirmez-le dans la fenêtre de la façon suivante :

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Cliquez sur **OK** pour enregistrer le nouveau mot de passe.

### <a name="change-the-pricing-tier"></a>Changer le niveau tarifaire
> [!NOTE]
> La mise à l’échelle est possible seulement entre les niveaux de service Usage général et Mémoire optimisée (et inversement). Notez que le passage vers ou depuis le niveau tarifaire De base n’est pas pris en charge après la création du serveur dans Azure Database pour MySQL.
> 
1. Cliquez sur **Niveau tarifaire**, sous **Paramètres**.
2. Cliquez sur le **Niveau tarifaire** que vous voulez utiliser.

    ![change-pricing-tier](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Cliquez sur **OK** pour enregistrer les modifications. 

### <a name="scale-vcores-updown"></a>Augmenter/diminuer le nombre de vCores

1. Cliquez sur **Niveau tarifaire**, sous **Paramètres**.

2. Modifiez le paramètre **vCore** en déplaçant le curseur vers la valeur souhaitée.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. Cliquez sur **OK** pour enregistrer les modifications.

### <a name="scale-storage-up"></a>Augmenter la capacité de stockage

1. Cliquez sur **Niveau tarifaire**, sous **Paramètres**.

2. Modifiez le paramètre **Stockage** en déplaçant le curseur vers la valeur souhaitée.

    ![scale-storage](./media/howto-create-manage-server-portal/scale-storage.png)

3. Cliquez sur **OK** pour enregistrer les modifications.

## <a name="delete-an-azure-database-for-mysql-server"></a>Suppression d’un serveur Azure Database pour MySQL

1. Dans la **Vue d’ensemble** du serveur, cliquez sur le bouton **Supprimer** pour ouvrir l’invite de confirmation de suppression.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Tapez le nom du serveur dans la zone d’entrée pour une double confirmation.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Cliquez sur le bouton **Supprimer** pour confirmer la suppression du serveur. Attendez que la fenêtre contextuelle « Le serveur MySQL a été supprimé » s’affiche dans la barre de notification.

## <a name="list-the-azure-database-for-mysql-databases"></a>Liste des bases de données Azure pour MySQL
Dans la **Vue d’ensemble** du serveur, faites défiler l’écran vers le bas jusqu’à la vignette des bases de données. Toutes les bases de données contenues dans le serveur sont répertoriées dans le tableau.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Affichage des détails d’un serveur Azure Database pour MySQL
Cliquez sur **Propriétés** en dessous de **Paramètres** pour afficher des informations détaillées sur le serveur.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Créer un serveur Azure Database pour MySQL à l'aide du portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)