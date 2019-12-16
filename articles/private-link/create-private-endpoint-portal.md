---
title: Démarrage rapide - Gérer les points de terminaison privés dans Azure
description: Découvrez dans ce guide de démarrage rapide comment créer un point de terminaison privé avec le portail Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 22614b28023a0628fb12c170e934aaab4d1ddfe4
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74899860"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Démarrage rapide : Créer un point de terminaison privé au moyen du portail Azure

Un point de terminaison privé est le composant fondamental d’une liaison privée dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link. Dans ce guide de démarrage rapide, vous allez découvrir comment créer une machine virtuelle sur un réseau virtuel Azure, un serveur SQL Database avec un point de terminaison privé Azure à l’aide du portail Azure. Ensuite, vous pouvez accéder de façon sécurisée au serveur SQL Database à partir de la machine virtuelle.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


> [!NOTE]
> Les points de terminaison privés ne peuvent pas être utilisés conjointement à des points de terminaison de service au sein d’un même sous-réseau.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-vm"></a>Créer une machine virtuelle
Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource de liaison privée (un serveur SQL dans Azure pour cet exemple).

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel


Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource de liaison privée.

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *MyVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.1.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Location | Sélectionnez **WestCentralUS**.|
    | Sous-réseau - Nom | Entrez *mySubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24*. |
    |||
1. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.


### <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

1. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.  |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm*. |
    | Région | Sélectionnez **WestCentralUS**. |
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

1. Dans **Créer une machine virtuelle - Mise en réseau**, entrez ou sélectionnez ces informations :

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

## <a name="create-a-sql-database-server"></a>Créer un serveur de base de données SQL
Dans cette section, vous allez créer un serveur de base de données SQL dans Azure. 

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Bases de données** > **Base de données SQL**.

1. Dans **Créer une base de données SQL - Concepts de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails de la base de données** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la base de données  | Entrez *mydatabase*. Si ce nom est utilisé, créez un nom unique. |
    |||
5. Dans **Serveur**, sélectionnez **Créer**. 
6. Dans **Nouveau serveur**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Nom du serveur  | Entrez *myserver*. Si ce nom est utilisé, créez un nom unique.|
    | Connexion d’administrateur serveur| Entrez le nom d’un administrateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 8 caractères et satisfaire aux exigences définies. |
    | Location | Sélectionnez une région Azure dans laquelle vous souhaitez que votre serveur SQL Server réside. |
    
7. Sélectionnez **OK**. 
8. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
9. Quand le message Validation réussie s’affiche, sélectionnez **Créer**. 
10. Quand le message Validation réussie s’affiche, sélectionnez Créer. 

## <a name="create-a-private-endpoint"></a>Créer un point de terminaison privé

Dans cette section, vous allez créer un serveur SQL Server et lui ajouter un point de terminaison privé. 

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Mise en réseau** > **Centre de liaisons privées (préversion)** .
2. Dans **Centre de liaisons privées - Vue d’ensemble**, dans l’option permettant de **générer une connexion privée à un service**, sélectionnez **Démarrer**.
1. Dans **Créer un point de terminaison privé (préversion) - Concepts de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom | Entrez *myPrivateEndpoint*. Si ce nom est utilisé, créez un nom unique. |
    |Région|Sélectionnez **WestCentralUS**.|
    |||
5. Sélectionnez **Suivant : Ressource**.
6. Dans **Créer un point de terminaison privé - Ressource**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Méthode de connexion  | Sélectionnez Se connecter à une ressource Azure dans mon répertoire.|
    | Subscription| Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.Sql/servers**. |
    | Ressource |Sélectionnez *myServer*|
    |Sous-ressource cible |Sélectionnez *sqlServer*|
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
    |Zone DNS privée |Sélectionnez *(New)privatelink.database.windows.net* |
    |||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
2. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**. 
 
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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Accéder au serveur de base de données SQL en privé à partir de la machine virtuelle

1. Dans le Bureau à distance de  *myVM*, ouvrez PowerShell.

2. Entrez  `nslookup myserver.database.windows.net`. 

    Vous recevez un message similaire à celui ci :
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Installez [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. Dans **Se connecter au serveur**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Type de serveur| Sélectionnez **Moteur de base de données**.|
    | Nom du serveur| Sélectionnez *myserver.database.windows.net*. |
    | Nom d'utilisateur | Entrez le nom d’utilisateur au format username@servername qui est fourni lors de la création du serveur SQL. |
    |Mot de passe |Entrez le mot de passe fourni lors de la création du serveur SQL. |
    |Se souvenir du mot de passe|Sélectionnez **Oui**.|
    |||
1. Sélectionnez **Connecter**.
2. Parcourez les bases de données dans le menu de gauche.
3. (Facultatif) Créez ou interrogez des données dans mydatabase.
4. Fermez la connexion Bureau à distance avec  *myVm*. 

## <a name="clean-up-resources"></a>Supprimer des ressources 
Lorsque vous avez fini d’utiliser le point de terminaison privé, le serveur SQL et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient : 
1. Entrez  *myResourceGroup* dans la zone **Rechercher** en haut du portail, puis sélectionnez  *myResourceGroup* dans les résultats de la recherche. 
2. Sélectionnez **Supprimer le groupe de ressources**. 
3. Entrez myResourceGroup dans **TAPER LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une machine virtuelle sur un réseau virtuel, un serveur SQL Database et un point de terminaison privé pour un accès privé. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez communiqué de façon sécurisée avec le serveur de base de données SQL via une liaison privée. Pour plus d’informations sur les points de terminaison privés, consultez [Qu’est-ce qu’Azure Private Endpoint ?](private-endpoint-overview.md).
