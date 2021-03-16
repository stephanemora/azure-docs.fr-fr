---
title: Filtrer le trafic réseau - Tutoriel - Portail Azure
titlesuffix: Azure Virtual Network
description: Dans ce tutoriel, vous allez apprendre à filtrer le trafic réseau vers un sous-réseau, avec un groupe de sécurité réseau, à l’aide du portail Azure.
services: virtual-network
author: KumudD
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 746e44c85d4dd9a662556a73f1e4ab0701d31400
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435902"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutoriel : Filtrer le trafic réseau avec un groupe de sécurité réseau à l’aide du portail Azure

Vous pouvez utiliser un groupe de sécurité réseau pour filtrer le trafic réseau entrant et sortant d’un sous-réseau de réseau virtuel.

Les groupes de sécurité réseau contiennent des règles de sécurité qui filtrent le trafic réseau par adresse IP, port et protocole. Les règles de sécurité sont appliquées aux ressources déployées dans un sous-réseau. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un groupe de sécurité réseau et les règles associées
> * Créer un réseau virtuel et associer un groupe de sécurité réseau à un sous-réseau
> * Déployer des machines virtuelles dans un sous-réseau
> * Tester les filtres de trafic

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

1. Sélectionnez **Créer une ressource** en haut à gauche du portail.

2. Dans la zone de recherche, entrez **Réseau virtuel**. Dans les résultats de la recherche, sélectionnez **Réseau virtuel**.

3. Dans la page **Réseau virtuel**, sélectionnez **Créer**.

4. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**.  </br> Entrez **myResourceGroup**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myVNet**. |
    | Région | Sélectionnez **(États-Unis) USA Est**. |

5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

6. Sélectionnez **Create** (Créer).

## <a name="create-application-security-groups"></a>Créer des groupes de sécurité d’application

Un groupe de sécurité d’application vous permet de regrouper des serveurs avec des fonctions similaires, tels que des serveurs Web.

1. Sélectionnez **Créer une ressource** en haut à gauche du portail.

2. Dans la zone de recherche, entrez **Groupe de sécurité d’application**. Dans les résultats de la recherche, sélectionnez **Groupe de sécurité d’application**.

3. Dans la page **Groupe de sécurité d’application**, sélectionnez **Créer**.

4. Dans **Créer un groupe de sécurité d’application**, entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**Détails du projet** |  |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. |
    | **Détails de l’instance** |  |
    | Nom | Entrez **myAsgWebServers**. |
    | Région | Sélectionnez **(États-Unis) USA Est**. | 

5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

6. Sélectionnez **Create** (Créer).

7. Répétez à nouveau l’étape 4 en spécifiant les valeurs suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**Détails du projet** |  |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. |
    | **Détails de l’instance** |  |
    | Nom | Entrez **myAsgMgmtServers**. |
    | Région | Sélectionnez **(États-Unis) USA Est**. |

8. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

9. Sélectionnez **Create** (Créer).

## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Un groupe de sécurité réseau sécurise le trafic réseau dans votre réseau virtuel.

1. Sélectionnez **Créer une ressource** en haut à gauche du portail.

2. Dans la zone de recherche, entrez **Groupe de sécurité réseau**. Dans les résultats de la recherche, sélectionnez **Groupe de sécurité réseau**.

3. Dans la page **Groupe de sécurité réseau**, sélectionnez **Créer**.

4. Dans **Créer un groupe de sécurité réseau**, entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myNSG**. |
    | Emplacement | Sélectionnez **(États-Unis) USA Est**. | 

5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

6. Sélectionnez **Create** (Créer).

## <a name="associate-network-security-group-to-subnet"></a>Associer le groupe de sécurité réseau au sous-réseau

Dans cette section, nous allons associer le groupe de sécurité réseau au sous-réseau du réseau virtuel que nous avons créé.

1. Dans le champ **Rechercher des ressources, services et documents** en haut du portail, commencez à taper **myNsg**. Quand le nom **myNsg** apparaît dans les résultats de la recherche, sélectionnez-le.

2. Dans la page de vue d’ensemble de **myNSG**, sélectionnez **Sous-réseaux** dans **Paramètres**.

3. Dans la page **Paramètres**, sélectionnez **Associer** :

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Associer un groupe NSG à un sous-réseau." border="true":::

3. Sous **Associer un sous-réseau**, sélectionnez **Réseau virtuel**, puis **myVNet**. 

4. Sélectionnez **Sous-réseau**, sélectionnez **par défaut**, puis sélectionnez **OK**.

## <a name="create-security-rules"></a>Créer des règles de sécurité

1. Dans **Paramètres** de **myNSG**, sélectionnez **Règles de sécurité de trafic entrant**.

2. Dans **Règles de sécurité de trafic entrant**, sélectionnez **+ Ajouter** :

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Ajouter une règle de sécurité de trafic entrant." border="true":::

3. Créez une règle de sécurité qui autorise les ports 80 et 443 dans le groupe de sécurité d’application **myAsgWebServers**. Dans **Ajouter une règle de sécurité de trafic entrant**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Source | Conservez la valeur par défaut **Tous**. |
    | Source port ranges | Conservez la valeur par défaut **(*)** |
    | Destination | Sélectionnez **Groupe de sécurité d’application**. |
    | Groupe de sécurité d’application de destination | Sélectionnez **myAsgWebServers**. |
    | Service | Conservez la valeur par défaut **Personnalisé**. |
    | Plages de ports de destination | Entrez **80,443**. |
    | Protocol | Sélectionnez **TCP**. |
    | Action | Conservez la valeur par défaut **Autoriser**. |
    | Priority | Conservez la valeur par défaut **100**. |
    | Nom | Entrez **Allow-Web-All**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Règle de sécurité de trafic entrant." border="true":::

3. Répétez l’étape 2 en utilisant les valeurs suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Source | Conservez la valeur par défaut **Tous**. |
    | Source port ranges | Conservez la valeur par défaut **(*)** |
    | Destination | Sélectionnez **Groupe de sécurité d’application**. |
    | Groupe de sécurité d’application de destination | Sélectionnez **myAsgMgmtServers**. |
    | Service | Conservez la valeur par défaut **Personnalisé**. |
    | Plages de ports de destination | Entrez **3389**. |
    | Protocol | Sélectionnez **TCP**. |
    | Action | Conservez la valeur par défaut **Autoriser**. |
    | Priority | Conservez la valeur par défaut **110**. |
    | Nom | Entrez **Allow-RDP-All**. |

    > [!CAUTION]
    > Dans cet article, le protocole RDP (port 3389) est exposé à Internet pour la machine virtuelle affectée au groupe de sécurité d’application **myAsgMgmtServers**. 
    >
    > Pour les environnements de production, au lieu d’exposer le port 3389 sur Internet, il est recommandé de vous connecter aux ressources Azure à gérer via un réseau VPN ou Azure Bastion.
    >
    > Pour plus d'informations sur Azure Bastion, consultez [Présentation d'Azure Bastion](../bastion/bastion-overview.md).

Une fois que vous avez terminé les étapes 1 à 3, passez en revue les règles que vous avez créées. Votre liste doit ressembler à la liste de l’exemple suivant :

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Règles de sécurité." border="true":::

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel.

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

1. Sélectionnez **Créer une ressource** en haut à gauche du portail.

2. Sélectionnez **Calcul**, puis **Machine virtuelle**.

3. Dans **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |  |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | Entrez **myVMWeb**. |
    | Région | Sélectionnez **(États-Unis) USA Est**. |
    | Options de disponibilité | Conservez la valeur par défaut indiquant qu’aucune redondance n’est nécessaire. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1**. |
    | Instance Azure Spot | Conservez la valeur par défaut (case non cochée). |
    | Taille | Sélectionnez **Standard_D2s_V3**. |
    | **Compte administrateur** |   |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. |
    | Mot de passe | Entrez un mot de passe. |
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **Règles des ports d’entrée** |   |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

4. Sélectionnez l’onglet **Réseau**.

5. Sous l’onglet **Mise en réseau**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Interface réseau** |   |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Sélectionnez **par défaut (10.0.0.0/24)** . |
    | Adresse IP publique | Conservez la valeur par défaut indiquant une nouvelle IP publique. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Aucun**. | 

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

7. Sélectionnez **Create** (Créer).

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

Réeffectuez les étapes 1 à 7, mais à l’étape 3, nommez la machine virtuelle **myVMMgmt**. Le déploiement de la machine virtuelle ne nécessite que quelques minutes. 

Ne passez pas à l’étape suivante tant que la machine virtuelle n’est pas déployée.

## <a name="associate-network-interfaces-to-an-asg"></a>Associer des interfaces réseau à un groupe de sécurité d’application

Lorsque le portail a créé les machines virtuelles, il a créé une interface réseau pour chaque machine virtuelle, et attaché l’interface réseau à la machine virtuelle. 

Ajoutez l’interface réseau de chaque machine virtuelle à l’un des groupes de sécurité d’application que vous avez créés précédemment :

1. Dans la zone **Rechercher dans les ressources, services et documents** en haut du portail, commencez à taper **myVMWeb**. Quand la machine virtuelle **myVMWeb** apparaît dans les résultats de la recherche, sélectionnez-la.

2. Sous **Paramètres**, sélectionnez **Mise en réseau**.  

3. Sélectionnez l’onglet **Groupes de sécurité d’application**, puis sélectionnez **Configurer les groupes de sécurité d’application**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Configurer les groupes de sécurité d’application." border="true":::

4. Dans **Configurer les groupes de sécurité d’application**, sélectionnez **myAsgWebServers**. Sélectionnez **Enregistrer**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Sélectionner les groupes de sécurité d’application." border="true":::

5. Répétez les étapes 1 et 2, en recherchant la machine virtuelle **myVMMgmt** et en sélectionnant le groupe de sécurité d’application (ASG) **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Tester les filtres de trafic

1. Connectez-vous à la machine virtuelle **myVMMgmt**. Entrez **myVMMgmt** dans la zone de recherche en haut du portail. Quand **myVMMgmt** apparaît dans les résultats de la recherche, sélectionnez cette machine virtuelle. Sélectionnez le bouton **Connexion**.

2. Sélectionnez **Télécharger le fichier RDP**.

3. Ouvrez le fichier RDP téléchargé et sélectionnez **Connecter**. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

4. Sélectionnez **OK**.

5. Vous pouvez éventuellement recevoir un avertissement lié au certificat pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion.

    La connexion s’établit correctement, car le trafic entrant en provenance d’Internet est autorisé sur le port 3389 à destination du groupe de sécurité d’application **myAsgMgmtServers**. 
    
    L’interface réseau de **myVMMgmt** est associée au groupe de sécurité d’application **myAsgMgmtServers** et autorise la connexion.

6. Ouvrez une session PowerShell sur **myVMMgmt**. Connectez-vous à **myVMWeb** à l’aide de l’exemple suivant : 

    ```powershell
    mstsc /v:myVmWeb
    ```

    La connexion RDP de **myVMMgmt** à **myVMWeb** s’établit correctement, car les machines virtuelles du même réseau peuvent communiquer entre elles sur n’importe quel port par défaut.
    
    Vous ne pouvez pas créer de connexion RDP à la machine virtuelle **myVMWeb** à partir d’Internet. La règle de sécurité de **myAsgWebServers** empêche les connexions entrantes en provenance d’Internet sur le port 3389. Par défaut, le trafic entrant en provenance d’Internet est refusé pour toutes les ressources.

7. Pour installer Microsoft IIS sur la machine virtuelle **myVMWeb**, entrez la commande suivante à partir d’une session PowerShell sur la machine virtuelle **myVMWeb** :

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Une fois l’installation d’IIS effectuée, déconnectez-vous de la machine virtuelle **myVMWeb**. Cela vous laisse ensuite uniquement la connexion Bureau à distance de la machine virtuelle **myVMMgmt**.

9. Déconnectez-vous de la machine virtuelle **myVMMgmt**.

10. Dans la zone **Rechercher dans les ressources, services et documents** en haut du portail Azure, commencez à taper **myVMWeb** à partir de votre ordinateur. Quand **myVMWeb** apparaît dans les résultats de la recherche, sélectionnez cette machine virtuelle. Notez l’**adresse IP publique** de votre machine virtuelle. L’adresse affichée dans l’exemple suivant est 23.96.39.113, mais votre adresse est différente :

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Adresse IP publique." border="true":::
    
11. Pour vérifier si vous pouvez accéder au serveur web **myVMWeb** depuis Internet, ouvrez un navigateur Internet sur votre ordinateur et accédez à `http://<public-ip-address-from-previous-step>`. 

Vous voyez s’afficher l’écran de bienvenue d’IIS, car le trafic entrant en provenance d’Internet est autorisé sur le port 80 à destination du groupe de sécurité d’application **myAsgWebServers**. 

L’interface réseau attachée à **myVMWeb** est associée au groupe de sécurité d’application **myAsgWebServers** et autorise la connexion. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le, ainsi que toutes les ressources qu’il contient :

1. Entrez **myResourceGroup** dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez **myResourceGroup** dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

* Vous avez créé un groupe de sécurité réseau, et vous l’avez associé à un sous-réseau de réseau virtuel. 
* Vous avez créé des groupes de sécurité d’application pour le web et la gestion.
* Vous avez créé deux machines virtuelles.
* Vous avez testé le filtrage réseau du groupe de sécurité d’application.

Pour en savoir plus sur les groupes de sécurité réseau, consultez [Vue d’ensemble d’un groupe de sécurité réseau](./network-security-groups-overview.md) et [Gérer un groupe de sécurité réseau](manage-network-security-group.md).

Azure achemine par défaut le trafic entre les sous-réseaux. À la place, vous pouvez choisir par exemple d’acheminer le trafic entre les sous-réseaux via une machine virtuelle, agissant comme un pare-feu. 

Pour apprendre à créer une table de routage, passez au didacticiel suivant.
> [!div class="nextstepaction"]
> [Créer une table de routage](./tutorial-create-route-table-portal.md)