---
title: Se connecter au serveur flexible Azure Database pour PostgreSQL avec accès privé au Portail Azure
description: Cet article explique comment créer et se connecter à un serveur flexible Azure Database pour PostgreSQL avec accès privé ou réseau virtuel à l’aide du Portail Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 07/29/2021
ms.openlocfilehash: 772cd5c647c3e03773bd6c3e7f57616093b4c7ce
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694144"
---
# <a name="connect-azure-database-for-postgresql-flexible-server-with-the-private-access-connectivity-method"></a>Connecter un serveur flexible Azure Database pour PostgreSQL avec la méthode de connectivité d’accès privé

Le serveur flexible Azure Database pour PostgreSQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des serveurs PostgreSQL hautement disponibles dans le cloud. Ce guide de démarrage rapide explique comment créer un serveur flexible dans un réseau virtuel à l’aide du portail Azure.

> [!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est actuellement en préversion publique.

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Accédez au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-an-azure-database-for-postgresql-flexible-server"></a>Créer un serveur flexible Azure Database pour PostgreSQL

Vous créez un serveur flexible avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-storage.md). Vous créez ce serveur dans un [groupe de ressources Azure](../../azure-resource-manager/management/overview.md).

Pour créer un serveur flexible, procédez comme suit :

1. Dans le portail, cherchez et sélectionnez **Serveurs Azure Database pour PostgreSQL** :

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="Capture d’écran montrant une recherche de serveurs Azure Database pour PostgreSQL." lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. Sélectionnez **Ajouter**.

3. Dans la page **Sélectionner une option de déploiement Azure Database pour PostgreSQL**, sélectionnez **Serveur flexible** comme option de déploiement :

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="Capture d’écran montrant l’option Serveur flexible." lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. Dans l’onglet **Informations de base**, entrez l’**abonnement**, le **groupe de ressources**, la **région** et le **nom du serveur**.  Avec les valeurs par défaut, cela permet de configurer un serveur PostgreSQL de version 12 avec un niveau tarifaire à usage général à l’aide de 2 vCores, 8 Gio de RAM et 28 Gio de stockage. La rétention des sauvegardes est de **sept** jours. Vous pouvez utiliser la charge de travail **Développement** pour un niveau tarifaire à faible coût.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png" alt-text="Capture d’écran montrant l’onglet Informations de base de la page Serveur flexible postgres." lightbox="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png":::

5. Dans l’onglet **Informations de base**, entrez un **nom d’utilisateur administrateur** et un **mot de passe administrateur**.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/db-administrator-account.png" alt-text="Capture d’écran montrant la page d’informations utilisateur de l’administrateur." lightbox="./media/quickstart-create-connect-server-vnet/db-administrator-account.png":::

6.  Accédez à l’onglet **Mise en réseau** et sélectionnez **accès privé**. Vous ne pouvez pas modifier la méthode de connectivité après avoir créé le serveur. Sélectionnez **Créer un réseau virtuel** pour créer un nouveau réseau virtuel **vnetenvironment1**. Sélectionnez **OK** une fois que vous avez fourni le nom du réseau virtuel et les informations du sous-réseau.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png" alt-text="Capture d’écran montrant l’onglet Mise en réseau avec le nouveau réseau virtuel." lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png":::

7. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.

8. Sélectionnez **Créer** pour approvisionner le serveur. L'approvisionnement peut prendre quelques minutes.

9. Patientez jusqu’à ce que le déploiement soit effectué et réussi.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="Capture d’écran montrant l’onglet Paramètres de mise en réseau avec le nouveau réseau virtuel." lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  Sélectionnez **Accéder à la ressource** pour voir la page **Vue d’ensemble** qui s’affiche.

## <a name="create-an-azure-linux-virtual-machine"></a>Créer une machine virtuelle Azure Linux

Étant donné que le serveur se trouve dans un réseau virtuel, vous pouvez uniquement vous connecter au serveur à partir d’autres services Azure dans le même réseau virtuel que le serveur. Pour vous connecter et gérer le serveur, nous allons créer une machine virtuelle Linux. La machine virtuelle doit être créée dans la **même région** et dans le **même abonnement**. La machine virtuelle Linux peut être utilisée en tant que tunnel SSH pour gérer votre serveur de base de données. 

1. Accédez à votre groupe de ressources dans lequel le serveur a été créé. Sélectionnez **Ajouter**.
2. Sélectionnez **Ubuntu Server 18.04 LTS**.
3. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis choisissez **Créer** pour créer un groupe de ressources. Tapez *myResourceGroup* comme nom.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="Capture d’écran de la section Détails du projet montrant où vous sélectionnez l’abonnement Azure et le groupe de ressources pour la machine virtuelle." lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png"::: 

2. Sous **Détails de l’instance**, tapez *myVM* comme **Nom de machine virtuelle**, choisissez la même **Région** que pour votre serveur de base de données.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="Capture d’écran de la section Détails de l’instance où vous spécifiez un nom pour la machine virtuelle et où vous sélectionnez sa région, son image et sa taille." lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. Sous **Compte Administrateur**, sélectionnez **Clé publique SSH**.

4. Dans **Nom d’utilisateur**, tapez *azureuser*.

5. Pour **Source de clé publique SSH**, laissez la valeur par défaut **Générer une nouvelle paire de clés**, puis tapez *myKey* comme **Nom de la paire de clés**.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="Capture d’écran de la section Compte d’administrateur où vous sélectionnez un type d’authentification et où fournissez les informations d’identification de l’administrateur." lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. Sous **Règles des ports d’entrée** > **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** et **HTTP (80)** dans la liste déroulante.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="Capture d’écran de la section Règles des ports d’entrée où vous sélectionnez les ports sur lesquels les connexions entrantes sont autorisées." lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. Sélectionnez la page **Mise en réseau** pour configurer le réseau virtuel. Pour réseau virtuel, choisissez le réseau **vnetenvironment1** créé pour le serveur de base de données.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="Capture d’écran de la sélection d’un réseau virtuel existant du serveur de base de données." lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. Sélectionnez **Gérer la configuration du sous-réseau** pour créer un sous-réseau pour le serveur.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="Capture d’écran de la gestion de sous-réseau." lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. Ajoutez un nouveau sous-réseau pour la machine virtuelle.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="Capture d’écran montrant l’ajout d’un nouveau sous-réseau pour la machine virtuelle." lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png"::: 

10. Une fois que le sous-réseau a été créé avec succès, fermez la page.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnet-create-success.png" alt-text="Capture d’écran montrant l’ajout réussi d’un nouveau sous-réseau pour la machine virtuelle." lightbox="./media/quickstart-create-connect-server-vnet/subnet-create-success.png":::

11. Sélectionnez **Vérifier + créer**.
12. Sélectionnez **Create** (Créer). Quand la fenêtre **Générer une nouvelle paire de clés** s’ouvre, sélectionnez **Télécharger la clé privée et créer une ressource**. Votre fichier de clé sera téléchargé sous **myKey.pem**.

    >[!IMPORTANT]
    > Assurez-vous de savoir où a été téléchargé le fichier `.pem`. Vous en aurez besoin de suivre le chemin d’accès à l’étape suivante.

13. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource** pour afficher la page **Vue d’ensemble** de la machine virtuelle.

14. Sélectionnez l’adresse IP publique et copiez-la dans le presse-papiers.

    :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="Capture d’écran montrant comment copier l’adresse IP pour la machine virtuelle." lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-postgresql-client-tools"></a>Installer des outils clients PostgreSQL

Créez une connexion SSH à la machine virtuelle à l’aide de Bash ou de PowerShell. À l’invite, ouvrez une connexion SSH à votre machine virtuelle. Remplacez l’adresse IP par celle de votre machine virtuelle, et remplacez le chemin de `.pem` par l’emplacement où le fichier de clé a été téléchargé.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Vous pourrez utiliser la clé SSH que vous avez créée la prochaine fois que vous créerez une machine virtuelle dans Azure. Il vous suffira de sélectionner **Utiliser une clé stockée dans Azure** pour **Source de la clé publique SSH** la prochaine fois que vous créerez une machine virtuelle. Vous disposerez déjà de la clé privée sur votre ordinateur, et n’aurez donc pas besoin de télécharger quoi que ce soit.

Vous devez installer l’outil postgresql-client pour pouvoir vous connecter au serveur.

```bash
sudo apt-getupdate
sudo apt-get install postgresql-client
```

Les connexions à la base de données s’effectuent avec le protocole SSL. Vous devez donc télécharger le certificat SSL public.

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Se connecter au serveur à partir de la machine virtuelle Linux Azure
Avec l’outil client **psql** installé, nous pouvons maintenant nous connecter au serveur à partir de votre environnement local.

```bash
psql --host=mydemoserver-pg.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres --set=sslmode=require --set=sslrootcert=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>Nettoyer les ressources
Vous venez de créer un serveur flexible Azure Database pour PostgreSQL dans un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources ou en supprimant simplement le serveur PostgreSQL. Procédez comme suit pour supprimer le groupe de ressources :

1. Depuis le portail Azure, recherchez et sélectionnez **Groupes de ressources**.
1. Dans la liste des groupes de ressources, sélectionnez le nom de votre groupe de ressources.
1. Dans la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter avec l’application Python](connect-python.md)
