---
title: 'Démarrage rapide : Créer un serveur flexible Azure DB pour MySQL - Portail Azure'
description: Cet article explique comment utiliser le portail Azure pour créer un serveur flexible Azure Database pour MySQL en quelques minutes.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 4d4d65f9ad04ca6bf99375647684a75e8662bb4d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944530"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Démarrage rapide : Utiliser le portail Azure pour créer un serveur flexible Azure Database pour MySQL

Azure Database pour MySQL - Serveur flexible est un service géré qui vous permet d'exécuter, de gérer et de mettre à l'échelle des serveurs MySQL hautement disponibles dans le cloud. Ce guide de démarrage rapide explique comment créer un serveur flexible à l'aide du portail Azure.

> [!IMPORTANT] 
> La fonctionnalité Serveur flexible Azure Database pour MySQL est actuellement disponible en préversion publique

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Ouvrez votre navigateur Web, puis accédez au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Qu'est-ce qu'un serveur flexible Azure Database pour MySQL ?

Vous créez un serveur flexible avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-storage.md). Vous créez ce serveur dans un [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

Pour créer un serveur flexible, procédez comme suit :

1. Sélectionnez **Créer une ressource** (+) dans l’angle supérieur gauche du portail.

2. Sélectionnez **Bases de données** > **Azure Database pour MySQL**. Vous pouvez également entrer **MySQL** dans la zone de recherche pour localiser le service.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="Option Azure Database pour MySQL":::

3. Sélectionnez **Serveur flexible** comme option de déploiement.
     
    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Choisir une option de déploiement":::    

4. Remplissez le formulaire **Informations de base** avec les informations suivantes : 

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Créer un formulaire de serveur"::: 
                                    
    |**Paramètre**|**Valeur suggérée**|**Description**|
    |---|---|---|
    Abonnement|Votre nom d’abonnement|Abonnement Azure que vous souhaitez utiliser pour votre serveur. Si vous disposez de plusieurs abonnements, choisissez celui dans lequel vous souhaitez que la ressource soit facturée.|
    Resource group|*myresourcegroup*| Un nouveau nom de groupe de ressources ou un nom de groupe existant dans votre abonnement.|
    Nom du serveur |*mydemoserver*|Nom unique qui identifie votre serveur flexible. Le nom de domaine *mysql.database.azure.com* est ajouté au nom de serveur que vous fournissez. Le serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit contenir entre 3 et 63 caractères.|
    Nom d’utilisateur administrateur |*mydemouser*| Votre propre compte de connexion en tant que compte à utiliser lorsque vous vous connectez au serveur. Le nom de connexion de l'administrateur ne doit pas être **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.|
    Mot de passe |Votre mot de passe| Un nouveau mot de passe pour le compte Administrateur du serveur. Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).|
    Region|La région la plus proche de vos utilisateurs| L’emplacement géographique le plus proche de vos utilisateurs.|
    Version|5.7| Version principale de MySQL.|
    Calcul + stockage | **Modulable**, **Standard_B1ms**, **10 Gio**, **7 jours** | Les configurations de calcul, de stockage et de sauvegarde pour votre nouveau serveur. Sélectionnez **Configurer le serveur**. *Modulable*, *Standard_B1ms*, *10 Gio* et *7 jours* sont les valeurs par défaut des options **Niveau de calcul**, **Taille de calcul**, **Stockage** et **Période de rétention des sauvegardes**. Vous pouvez laisser ces curseurs en l’état ou les ajuster. Enregistrez cette sélection de calcul et de stockage en sélectionnant **Enregistrer** afin de conserver les configurations. La capture d'écran ci-dessous présente les options de calcul et de stockage.|
    
    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Calcul + stockage":::

5. Configurer les options de mise en réseau

    Sous l'onglet Mise en réseau, vous pouvez choisir la façon dont votre serveur est accessible. La fonctionnalité Serveur flexible Azure Database pour MySQL propose deux options pour se connecter à votre serveur : *Accès public (adresses IP autorisées)* et *Accès privé (intégration au réseau virtuel)* . Avec l'option *Accès public (adresses IP autorisées)* , l'accès à votre serveur est limité aux adresses IP autorisées ajoutées à une règle de pare-feu. Cette option empêche les applications et les outils externes de se connecter au serveur et à toute base de données du serveur, sauf si vous créez une règle pour ouvrir le pare-feu à une adresse IP ou à une plage d'adresses IP spécifique. Avec l'option *Accès privé (intégration au réseau virtuel)* , l'accès à votre serveur est limité à votre réseau virtuel. Dans ce guide de démarrage rapide, nous vous expliquons comment activer l'accès public pour vous connecter au serveur. Pour en savoir plus sur les méthodes de connectivité, consultez l'[article consacré aux concepts](./concepts-networking.md).

    > [!NOTE]
    > Une fois le serveur créé, la méthode de connectivité ne peut pas être modifiée. Par exemple, si vous avez sélectionné *Accès public (adresses IP autorisées)* lors de la création du serveur, vous ne pouvez pas passer à *Accès privé (intégration au réseau virtuel)* à l'issue de cette création. Nous vous recommandons vivement de sélectionner l'Accès privé lors de la création d'un serveur afin de pouvoir y accéder en toute sécurité à l'aide de l'intégration au réseau virtuel. Pour en savoir plus sur l'accès privé, consultez l'[article consacré aux concepts](./concepts-networking.md).

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Configurer la mise en réseau":::  

    <!--:::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Select "Add current client IP address"":::-->

6. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.

7. Sélectionnez **Créer** pour approvisionner le serveur. L'approvisionnement peut prendre quelques minutes.

8. Dans la barre d’outils, sélectionnez **Notifications** (icône de cloche) pour surveiller le processus de déploiement. Au terme du déploiement, vous pouvez sélectionner **Épingler au tableau de bord**. Une vignette est alors créée pour ce serveur flexible dans le tableau de bord du portail Azure et celle-ci fait office de raccourci vers la page **Vue d'ensemble** du serveur. En sélectionnant **Accéder à la ressource**, ouvrez la page **Vue d’ensemble** du serveur.

Par défaut, les bases de données suivantes sont créées sous votre serveur : **information_schema**, **mysql**, **performance_schema**, et **sys**.

> [!NOTE]
> Vérifiez que votre réseau autorise le trafic sortant sur le port 3306 que le Serveur flexible Azure Database pour MySQL utilise afin d'éviter les problèmes de connectivité.  

## <a name="connect-to-using-mysql-command-line-client"></a>Se connecter à l'aide du client de ligne de commande mysql

Si vous avez créé votre serveur flexible avec l'option *Accès privé (intégration au réseau virtuel)* , vous devez vous connecter à votre serveur à partir d'une ressource qui se trouve au sein du même réseau virtuel que votre serveur. Vous pouvez créer une machine virtuelle et l'ajouter au réseau virtuel créé avec votre serveur flexible.

Si vous avez créé votre serveur flexible avec l'option *Accès public (adresses IP autorisées)* , vous pouvez ajouter votre adresse IP locale à la liste des règles de pare-feu sur votre serveur.

Vous pouvez choisir [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) pour vous connecter au serveur à partir de votre environnement local. 

Avec mysql.exe, connectez-vous à l'aide de la commande ci-dessous. Remplacez les valeurs par le nom et le mot de passe réels de votre serveur. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Nettoyer les ressources
Vous avez réussi à créer un serveur flexible Azure Database pour MySQL dans un groupe de ressources.  Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources ou en supprimant simplement le serveur MySQL. Pour supprimer le groupe de ressources, suivez ces étapes :

1. Depuis le portail Azure, recherchez et sélectionnez **Groupes de ressources**.
1. Dans la liste des groupes de ressources, choisissez le nom de votre groupe de ressources.
1. Dans la page Vue d’ensemble de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

Pour supprimer le serveur, vous pouvez cliquer sur le bouton **Supprimer** dans la page **Vue d’ensemble** de votre serveur, comme indiqué ci-dessous :

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Supprimer vos ressources":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web PHP (Laravel) avec MySQL](tutorial-php-database-app.md)
