---
title: Installer le client Chef à partir du portail Azure
description: Découvrez comment déployer et configurer le client Chef à partir du portail Azure
keywords: azure, chef, devops, client, installation, portail
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: f8707c2fe39fb794381af298c24d27704b1ec255
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158264"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installer le client Chef à partir du portail Azure
Vous pouvez ajouter l’extension du client Chef directement sur une machine Linux ou Windows à partir du portail Azure. Cet article vous guide tout au long du processus, qui utilise une machine virtuelle Linux.

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Chef** : Si vous n’avez pas de compte Chef actif, inscrivez-vous pour [un essai gratuit de Hosted Chef](https://manage.chef.io/signup). Pour suivre les instructions de cet article, vous aurez besoin des informations suivantes concernant votre compte Chef :
  - clé organization_validation
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installer l’extension Chef sur une nouvelle machine virtuelle Linux
Dans cette section, vous allez utiliser le portail Azure pour créer une machine Linux. Au cours de ce processus, vous allez également apprendre à installer l’extension Chef sur la nouvelle machine virtuelle.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Dans le menu de gauche, sélectionnez l’option **Machines virtuelles**. Si l’option **Machines virtuelles** ne s’affiche pas, sélectionnez **Tous les services**, puis **Machines virtuelles**.

1. Sous l’onglet **Machines virtuelles**, sélectionnez **Ajouter**.

    ![Ajouter une nouvelle machine virtuelle dans le portail Azure](./media/chef-extension-portal/add-vm.png)

1. Sous l’onglet **Compute**, sélectionnez le système d’exploitation souhaité. Pour cette démonstration, **Serveur Ubuntu** est sélectionné.

1. Sous l’onglet **Serveur Ubuntu**, sélectionnez **Ubuntu Server 16.04 LTS**.

    ![Quand vous créez une machine virtuelle Ubuntu, spécifiez la version dont vous avez besoin.](./media/chef-extension-portal/ubuntu-server-version.png)

1. Sous l’onglet **Ubuntu Server 16.04 LTS**, sélectionnez **Créer**.

    ![Ubuntu fournit des informations supplémentaires sur ses produits.](./media/chef-extension-portal/create-vm.png)

1. Sous l’onglet **Créer une machine virtuelle**, sélectionnez **De base**.

1. Sous l’onglet **De base**, spécifiez les valeurs suivantes, puis sélectionnez **OK**.

   - **Nom** : Entrez le nom de la nouvelle machine virtuelle.
   - **Type de disque de machine virtuelle** : spécifiez **SSD** ou **HDD** pour le type de disque de stockage. Pour plus d’informations sur les types de disques de machine virtuelle dans Azure, consultez l’article [Sélectionner un type de disque](../virtual-machines/windows/disks-types.md).
   - **Nom d’utilisateur** : entrez le nom d’un utilisateur qui dispose de privilèges d’administrateur sur la machine virtuelle.
   - **Authentication type (Type d’authentification)** : sélectionnez **Password (Mot de passe)** . Vous pouvez également sélectionner **Clé publique SSH** et fournir une valeur pour cette clé. Pour cette démonstration (et dans les captures d’écran), **Mot de passe** est sélectionné.
   - **Mot de passe** et **Confirmer le mot de passe** : entrez un mot de passe pour l’utilisateur.
   - **Se connecter avec Azure Active Directory** : sélectionnez **Désactivé**.
   - **Abonnement** : sélectionnez l’abonnement Azure que vous souhaitez utiliser, si vous en avez plusieurs.
   - **Groupe de ressources** : entrez un nom pour votre groupe de ressources.
   - **Location (Emplacement)** : sélectionnez **USA Est**.

     ![Onglet De base pour la création d’une machine virtuelle](./media/chef-extension-portal/add-vm-basics.png)

1. Sous l’onglet **Choisir une taille**, sélectionnez une taille de machine virtuelle, puis sélectionnez **Sélectionner**.

1. Sous l’onglet **Paramètres**, la plupart des valeurs sont renseignées automatiquement, selon les valeurs que vous avez sélectionnées sous les onglets précédents. Sélectionnez **Extensions**.

     ![Des extensions sont ajoutées aux machines virtuelles via l’onglet Paramètres](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Sous l’onglet **Extensions**, sélectionnez **Ajouter une extension**.

     ![Sélection de l’option Ajouter une extension pour ajouter une extension à une machine virtuelle](./media/chef-extension-portal/add-vm-add-extension.png)

1. Sous l’onglet **Nouvelle ressource**, sélectionnez **Linux Chef Extension (1.2.3)** .

     ![Chef comprend des extensions pour les machines virtuelles Linux et Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Sous l’onglet **Linux Chef Extension**, sélectionnez **Créer**.

1. Sous l’onglet **Installer l’extension**, spécifiez les valeurs suivantes, puis sélectionnez **OK**.

    - **URL du serveur Chef** : entrez l’URL du serveur Chef qui comprend le nom de l’organisation, par exemple, *https://api.chef.io/organization/mycompany* .
    - **Chef Node Name** (Nom du nœud Chef) : entrez le nom du nœud Chef. Vous pouvez utiliser n’importe quelle valeur.
    - **Run List** (Liste d’exécution) : entrez la liste d’exécution Chef qui est ajoutée à la machine. Cela champ peut être vide.
    - **Validation Client Name** (Nom du client de validation) : entrez le nom du client de validation Chef. Par exemple, *tarcher-validator*.
    - **Validation Key** (Clé de validation) : sélectionnez un fichier contenant la clé de validation utilisée lors de l’amorçage de vos machines.
    - **Client Configuration File** (Fichier de configuration du client) : sélectionnez un fichier de configuration pour le client Chef. Cela champ peut être vide.
    - **Chef Client version** (Version du client Chef) : entrez la version du client Chef à installer. Cela champ peut être vide. Une valeur vide installe la dernière version.
    - **SSL Verification Mode** (Mode de vérification SSL) : sélectionnez **None** (Aucun) ou **Peer** (Pair). *None* a été sélectionné pour la démonstration.
    - **Chef Environment** (Environnement Chef) : entrez l’environnement Chef dont ce nœud doit faire partie. Cela champ peut être vide.
    - **Encrypted Databag Secret** (Secret du conteneur de données chiffrées) : sélectionnez un fichier contenant le secret du conteneur de données chiffrées auquel cet ordinateur doit avoir accès. Cela champ peut être vide.
    - **Chef Server SSL Certificate** (Certificat SSL du serveur Chef) : sélectionnez le certificat SSL attribué à votre serveur Chef. Cela champ peut être vide.

      ![Installation du serveur Chef sur une machine virtuelle Linux](./media/chef-extension-portal/install-extension.png)

1. De retour sous l’onglet **Extensions**, sélectionnez **OK**.

1. De retour sous l’onglet **Settings** (Paramètres), sélectionnez **OK**.

1. De retour sous l’onglet **Créer** (qui récapitule les options que vous avez sélectionnées et entrées), vérifiez les informations, ainsi que les **conditions d’utilisation**, puis sélectionnez **Créer**.

Une fois que le processus de création et de déploiement de la machine virtuelle avec l’extension Chef est terminé, une notification indique la réussite ou l’échec de l’opération. De plus, la page de ressources de la nouvelle machine virtuelle s’ouvre automatiquement dans le portail Azure une fois que celle-ci a été créée.

![Installation du serveur Chef sur une machine virtuelle Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer une machine virtuelle Windows dans Azure avec Chef](/azure/virtual-machines/windows/chef-automation)
