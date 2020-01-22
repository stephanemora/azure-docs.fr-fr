---
title: Méthode d’installation du service Azure Private Link pour Azure Database pour PostgreSQL – Serveur unique (préversion) via le portail
description: Découvrez comment configurer Private Link pour Azure Database pour PostgreSQL – Serveur unique à partir du Portail Azure
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: dca271e745976f7797d3e911c2f1f6232fe5400d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898993"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-preview-using-portal"></a>Créer et gérer le service Azure Private Link pour Azure Database pour PostgreSQL – Serveur unique (préversion) à l’aide du Portail Azure

Private Endpoint est le composant fondamental de Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link.  Dans cet article, vous apprendrez à utiliser le Portail Azure pour créer une machine virtuelle dans un Réseau virtuel Microsoft Azure et un serveur unique Azure Database pour PostgreSQL avec un point de terminaison privé Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> Cette fonctionnalité est disponible dans toutes les régions Azure où Azure Database pour PostgreSQL Serveur unique prend en charge les niveaux tarifaires Usage général et Mémoire optimisée.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Création d’une machine virtuelle Azure

Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Azure Private Link (un serveur PostgreSQL dans Azure).

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel
Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource de liaison privée.

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
2. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Name | Entrez *MyVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.1.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Location | Sélectionnez **Europe Ouest**.|
    | Sous-réseau - Nom | Entrez *mySubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24*. |
    |||
3. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.

### <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

2. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.  |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm*. |
    | Région | Sélectionnez **Europe Ouest**. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter**. |
    | Size | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Conservez la valeur par défaut **Aucun**. |
    | **ÉCONOMISEZ DE L’ARGENT** |  |
    | Vous disposez déjà d’une licence Windows ? | Conservez la valeur par défaut **Non**. |
    |||

1. Sélectionnez **Suivant : Disques**.

1. Dans **Créer une machine virtuelle - Disks**, conservez les valeurs par défaut et sélectionnez **Suivant : Mise en réseau**.

1. Dans **Créer une machine virtuelle - Mise en réseau**, sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Réseau virtuel | Conservez la valeur par défaut, **MyVirtualNetwork**.  |
    | Espace d’adressage | Conservez la valeur par défaut, **10.1.0.0/24**.|
    | Subnet | Conservez la valeur par défaut, **mySubnet (10.1.0.0/24)** .|
    | Adresse IP publique | Conservez la valeur par défaut **(new) myVm-ip**. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**.|
    |||


1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.

1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Créer un serveur unique Azure Database pour PostgreSQL

Dans cette section, vous allez créer un serveur Azure Database pour PostgreSQL dans Azure. 

1. En haut à gauche de l’écran du Portail Azure, sélectionnez **Créer une ressource** > **Bases de données** > **Azure Database pour PostgreSQL**.

1. Dans l’**option de déploiement Azure Database pour PostgreSQL**, sélectionnez **Serveur unique** et fournissez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **Détails du serveur** |  |
    |Nom du serveur  | Entrez *myserver*. Si ce nom est utilisé, créez un nom unique.|
    | Nom d’utilisateur administrateur| Entrez le nom d’un administrateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 8 caractères et satisfaire aux exigences définies. |
    | Location | Sélectionnez une région Azure dans laquelle vous souhaitez que votre serveur PostgreSQL réside. |
    |Version  | Sélectionnez la version de base de données du serveur PostgreSQL qui est requise.|
    | Calcul + stockage| Sélectionnez le niveau tarifaire requis pour le serveur en fonction de la charge de travail. |
    |||
 
7. Sélectionnez **OK**. 
8. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
9. Quand le message Validation réussie s’affiche, sélectionnez **Créer**. 
10. Quand le message Validation réussie s’affiche, sélectionnez Créer. 

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

Dans cette section, vous allez créer un serveur PostgreSQL et lui ajouter un point de terminaison privé. 

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Mise en réseau** > **Centre de liaisons privées (préversion)** .
2. Dans **Centre de liaisons privées - Vue d’ensemble**, dans l’option permettant de **générer une connexion privée à un service**, sélectionnez **Démarrer**.

    ![Vue d’ensemble d’Azure Private Link](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Dans **Créer un point de terminaison privé (préversion) - Concepts de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **Détails de l’instance** |  |
    | Name | Entrez *myPrivateEndpoint*. Si ce nom est utilisé, créez un nom unique. |
    |Région|Sélectionnez **Europe Ouest**.|
    |||
5. Sélectionnez **Suivant : Ressource**.
6. Dans **Créer un point de terminaison privé - Ressource**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Méthode de connexion  | Sélectionnez Se connecter à une ressource Azure dans mon répertoire.|
    | Subscription| Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.DBforPostgreSQL/servers**. |
    | Ressource |Sélectionnez *myServer*|
    |Sous-ressource cible |Sélectionnez *postgresqlServer*.|
    |||
7. Sélectionnez **Suivant : Configuration**.
8. Dans **Créer un point de terminaison privé (préversion) - Configuration**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**MISE EN RÉSEAU**| |
    | Réseau virtuel| Sélectionnez *MyVirtualNetwork*. |
    | Subnet | Sélectionnez *mySubnet*. |
    |**INTÉGRATION À DNS PRIVÉ**||
    |Intégrer à une zone DNS privée |Sélectionnez **Oui**. |
    |Zone DNS privée |Sélectionnez *(New)privatelink.database.azure.com*. |
    |||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
2. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**. 

    ![Instance Azure Private Link créée](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Se connecter à une machine virtuelle à l’aide du Bureau à distance (RDP)


Après avoir créé **myVm**, connectez-vous à cette machine virtuelle à partir d’Internet comme suit : 

1. Dans la barre de recherche du portail, entrez *myVm*.

1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

1. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( *.rdp*) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier *downloaded.rdp*.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Accéder au serveur PostgreSQL en privé à partir de la machine virtuelle

1. Dans le Bureau à distance de  *myVM*, ouvrez PowerShell.

2. Entrez `nslookup mydemopostgresserver.database.azure.com`. 

    Vous recevez un message similaire à celui ci :
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.postgres.privatelink.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the PostgreSQL server using any available client. In the example below I have used [Azure Data studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) to do the operation.

4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **PostgreSQL**.|
    | Server name| Select *mydemopostgresserver.postgres.privatelink.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the PostgreSQL server creation. |
    |Password |Enter a password provided during the PostgreSQL server creation. |
    |SSL|Select **Required**.|
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the postgreSQL server.

8. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, PostgreSQL server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for PostgreSQL - Single server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the PostgreSQL server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).