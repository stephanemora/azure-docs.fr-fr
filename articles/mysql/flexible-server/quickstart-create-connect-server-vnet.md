---
title: Se connecter au serveur flexible Azure Database pour MySQL avec accès privé dans le portail Azure
description: Cet article vous explique comment utiliser le portail Azure pour créer un serveur flexible Azure Database pour MySQL et s’y connecter en accès privé.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/18/2021
ms.openlocfilehash: cf69321045a3c79e2803445964ff7f09a5e522e7
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114732047"
---
# <a name="connect-azure-database-for-sql-flexible-server-with-private-access-connectivity-method"></a>Connecter un serveur flexible Azure Database pour SQL avec la méthode de connectivité d’accès privé

Le serveur flexible Azure Database pour MySQL est un service managé qui vous permet d’exécuter, de gérer et de mettre à l’échelle des serveurs MySQL hautement disponibles dans le cloud. Ce guide de démarrage rapide explique comment créer un serveur flexible dans un réseau virtuel à l’aide du portail Azure.

> [!IMPORTANT]
> Le serveur flexible Azure Database pour MySQL est actuellement en préversion publique.

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Accédez au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Créer un serveur flexible Azure Database pour MySQL

Vous créez un serveur flexible avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-storage.md). Vous créez ce serveur dans un [groupe de ressources Azure](../../azure-resource-manager/management/overview.md).

Pour créer un serveur flexible, procédez comme suit :

1. Dans le portail, cherchez et sélectionnez **Serveurs Azure Database pour MySQL** :

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="Capture d’écran montrant une recherche de serveurs Azure Database pour MySQL." lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. Sélectionnez **Ajouter**.

3. Dans la page **Sélectionner une option de déploiement Azure Database pour MySQL**, sélectionnez **Serveur flexible** comme option de déploiement :

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="Capture d’écran montrant l’option Serveur flexible." lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. Dans l’onglet **Général**, entrez **l’abonnement**, le **groupe de ressources**, la **région**, le **nom d’utilisateur de l’administrateur** et le **mot de passe de l’administrateur**.  Avec les valeurs par défaut, cela entraîne le provisionnement d’un serveur MySQL version 5.7 avec référence SKU Burstable, utilisant 1 vCore, 2 Gio de mémoire et un stockage de 32 Gio. La rétention des sauvegardes est de 7 jours. Vous pouvez modifier la configuration.

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png" alt-text="Capture d’écran montrant l’onglet De base de la page Serveur flexible." lightbox="/media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png":::

   > [!TIP]
   > Pour accélérer le chargement des données pendant la migration, il est recommandé de définir le nombre d'IOPS par seconde sur la taille maximale prise en charge par la taille de calcul et de le remettre ultérieurement à l'échelle pour réduire les coûts.

5.  Accédez à l’onglet **Mise en réseau**, sélectionnez **Accès privé**. Vous ne pouvez pas modifier la méthode de connectivité après avoir créé le serveur. Sélectionnez **Créer un réseau virtuel** pour créer un nouveau réseau virtuel **vnetenvironment1**.

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png" alt-text="Capture d’écran montrant l’onglet Mise en réseau avec le nouveau réseau virtuel." lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png":::

6. Sélectionnez **OK** une fois que vous avez fourni le nom du réseau virtuel et les informations du sous-réseau.
    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/show-server-vnet-information.png" alt-text="consulter les informations sur le réseau virtuel":::

7. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.

8. Sélectionnez **Créer** pour approvisionner le serveur. L'approvisionnement peut prendre quelques minutes.

9. Patientez jusqu’à ce que le déploiement soit effectué et réussi.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="Capture d’écran montrant l’onglet Paramètres de mise en réseau avec le nouveau réseau virtuel." lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  Sélectionnez **Accéder à la ressource** pour voir la page **Vue d’ensemble** qui s’affiche.

## <a name="create-azure-linux-virtual-machine"></a>Créer une machine virtuelle Azure Linux

Étant donné que le serveur se trouve dans un réseau virtuel, vous pouvez uniquement vous connecter au serveur à partir d’autres services Azure dans le même réseau virtuel que le serveur. Pour vous connecter et gérer le serveur, nous allons créer une machine virtuelle Linux. La machine virtuelle doit être créée dans la **même région** et dans le **même abonnement**. La machine virtuelle Linux peut être utilisée en tant que tunnel SSH pour gérer votre serveur de base de données. 

1. Accédez à votre groupe de ressources dans lequel le serveur a été créé. Sélectionnez **Ajouter**.
2. Sélectionnez **Ubuntu Server 18.04 LTS**
3. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis choisissez **Créer** pour créer un groupe de ressources. Tapez *myResourceGroup* comme nom.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="Capture d’écran de la section Détails du projet montrant où vous sélectionnez l’abonnement Azure et le groupe de ressources pour la machine virtuelle" lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png"::: 

2. Sous **Détails de l’instance**, tapez *myVM* comme **Identité de machine virtuelle**, choisissez la même **Région** que pour votre serveur de base de données.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="Capture d’écran de la section Détails de l’instance où vous spécifiez un nom pour la machine virtuelle et où vous sélectionnez sa région, son image et sa taille." lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. Sous **Compte Administrateur**, sélectionnez **Clé publique SSH**.

4. Dans **Nom d’utilisateur**, tapez *azureuser*.

5. Pour **Source de clé publique SSH**, laissez la valeur par défaut **Générer une nouvelle paire de clés**, puis tapez *myKey* comme **Nom de la paire de clés**.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="Capture d’écran de la section Compte d’administrateur où vous sélectionnez un type d’authentification et où fournissez les informations d’identification de l’administrateur" lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. Sous **Règles des ports d’entrée** > **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** et **HTTP (80)** dans la liste déroulante.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="Capture d’écran de la section Règles des ports d’entrée où vous sélectionnez les ports sur lesquels les connexions entrantes sont autorisées" lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. Sélectionnez la page **Mise en réseau** pour configurer le réseau virtuel. Pour réseau virtuel, choisissez le réseau **vnetenvironment1** créé pour le serveur de base de données.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="Capture d’écran de la sélection d’un réseau virtuel existant du serveur de base de données" lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. Sélectionnez **Gérer la configuration du sous-réseau** pour créer un sous-réseau pour le serveur.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="Capture d’écran de la gestion de sous-réseau" lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. Ajoutez un nouveau sous-réseau pour la machine virtuelle.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="Capture d’écran montrant l’ajout d’un nouveau sous-réseau pour la machine virtuelle" lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png"::: 

10. Une fois que le sous-réseau a été créé avec succès, fermez la page.
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png" alt-text="Capture d’écran montrant l’ajout réussi d’un nouveau sous-réseau pour la machine virtuelle" lightbox="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png":::

11. Sélectionnez **Vérifier + créer**.
12. Sélectionnez **Create** (Créer). Quand la fenêtre **Générer une nouvelle paire de clés** s’ouvre, sélectionnez **Télécharger la clé privée et créer une ressource**. Votre fichier de clé sera téléchargé en tant que **myKey.pem**.

   >[!IMPORTANT]
   > Notez où le fichier `.pem` a été téléchargé ; vous aurez besoin du chemin à l’étape suivante.

13. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-create-success.png" alt-text="Capture d’écran du déploiement réussi" lightbox="./media/quickstart-create-connect-server-vnet/vm-create-success.png":::

11. Dans la page de votre nouvelle machine virtuelle, sélectionnez l’adresse IP publique et copiez-la dans le presse-papiers.
   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="Capture d’écran montrant comment copier l’adresse IP pour la machine virtuelle" lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-mysql-client-tools"></a>Installer les outils clients MySQL

Créez une connexion SSH à la machine virtuelle à l’aide de Bash ou de PowerShell. À l’invite, ouvrez une connexion SSH à votre machine virtuelle. Remplacez l’adresse IP par celle de votre machine virtuelle, et remplacez le chemin de `.pem` par l’emplacement où le fichier de clé a été téléchargé.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Vous pourrez utiliser la clé SSH que vous avez créée la prochaine fois que vous créerez une machine virtuelle dans Azure. Il vous suffira de sélectionner **Utiliser une clé stockée dans Azure** pour **Source de la clé publique SSH** la prochaine fois que vous créerez une machine virtuelle. Vous disposerez déjà de la clé privée sur votre ordinateur, et n’aurez donc pas besoin de télécharger quoi que ce soit.

Vous devez installer l’outil mysql-client pour pouvoir vous connecter au serveur.

```bash
sude apt-getupdate
sudo apt-get install mysql-client
```

Les connexions à la base de données s’effectuent avec le protocole SSL. Vous devez donc télécharger le certificat SSL public.

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Se connecter au serveur à partir de la machine virtuelle Linux Azure
Avec l’outil client [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) installé, nous pouvons maintenant nous connecter au serveur à partir de votre environnement local.

```bash
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>Nettoyer les ressources
Vous venez de créer un serveur flexible Azure Database pour MySQL dans un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources ou en supprimant simplement le serveur MySQL. Pour supprimer le groupe de ressources, effectuez ces étapes :

1. Depuis le portail Azure, recherchez et sélectionnez **Groupes de ressources**.
1. Dans la liste des groupes de ressources, sélectionnez le nom de votre groupe de ressources.
1. Dans la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web PHP (Laravel) avec MySQL](tutorial-php-database-app.md)
