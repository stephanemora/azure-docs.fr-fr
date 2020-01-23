---
title: Crée un point de terminaison privé pour une connexion sécurisée
titleSuffix: Azure Cognitive Search
description: Configurer un point de terminaison privé dans un réseau virtuel pour une connexion sécurisée à un service Recherche cognitive Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945819"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Créer un point de terminaison privé pour une connexion sécurisée à Recherche cognitive Azure (version préliminaire)

Dans cet article, vous découvrirez comment utiliser le portail pour créer une instance de service Recherche cognitive Azure qui n’est pas accessible via une adresse IP publique. Ensuite, vous configurerez une machine virtuelle Azure dans le même réseau virtuel et l’utiliserez pour accéder au service Search via un point de terminaison privé.

> [!Important]
> La prise en charge des points de terminaison privés de Recherche cognitive Azure est disponible [à la demande](https://aka.ms/SearchPrivateLinkRequestAccess) comme version préliminaire à accès limité. Les fonctionnalités d’évaluation sont fournies sans contrat de niveau de service et sont déconseillées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Une fois que vous avez l’autorisation d’accéder à la version préliminaire, vous pourrez configurer des points de terminaison privés pour votre service à l’aide du portail Azure ou de la [version préliminaire 2019-10-06 de l’API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Pourquoi utiliser un point de terminaison privé pour sécuriser l’accès ?

Les [points de terminaison privés](../private-link/private-endpoint-overview.md)de Recherche cognitive Azure permettent à un client d’un réseau virtuel d’accéder en toute sécurité aux données d’un index de recherche grâce à une [liaison privée](../private-link/private-link-overview.md). Ils utilisent une adresse IP de [l’espace d’adressage du réseau virtuel](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) pour votre service Search. Le trafic entre le client et le service Search traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine l’exposition sur l’Internet public. Pour obtenir la liste des autres services PaaS qui prennent en charge la liaison privée, consultez la [section disponibilité](../private-link/private-link-overview.md#availability) dans la documentation du produit.

Les points de terminaison privés de votre service Search vous permettent de :

- Bloquer toutes les connexions sur le point de terminaison public de votre service Search.
- Renforcer la sécurité du réseau virtuel en assurant le blocage de l’exfiltration des données à partir du réseau virtuel.
- Vous connecter en toute sécurité à votre service Search à partir de réseaux locaux qui se connectent au réseau virtuel à l’aide de [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [d’ExpressRoutes](../expressroute/expressroute-locations.md) avec le peering privé.

> [!NOTE]
> La version préliminaire comprend certaines limites que vous devez connaître :
> * Disponible uniquement pour les services Search sur le niveau **de base**. 
> * Disponible dans les régions USA Ouest 2, USA Centre-Ouest, USA Est, USA Centre Sud, Australie Est et Australie Sud-Est.
> * Lorsque le point de terminaison de service est privé, certaines fonctionnalités du portail sont désactivées. Vous pouvez voir et gérer les informations au niveau du service, mais l'accès du portail aux données d'index et aux divers composants de ce service, comme les définitions d'index, d'indexeur et d'ensemble de compétences, est limité pour des raisons de sécurité.
> * Lorsque le point de terminaison de service est privé, vous devez utiliser [l’API REST de Recherche](https://docs.microsoft.com/rest/api/searchservice/) pour charger des documents dans l’index.
> * Utilisez le lien suivant pour voir l’option de prise en charge du point de terminaison privé dans le portail Azure : https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Demander l'accès 

Cliquez sur [demander l’accès](https://aka.ms/SearchPrivateLinkRequestAccess) pour vous inscrire à cette fonctionnalité d'évaluation. Le formulaire vous demandera de fournir des informations sur vous, votre entreprise et la topologie de réseau générale. Après avoir examiné votre demande, nous vous enverrons un e-mail de confirmation contenant des instructions supplémentaires.

## <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder au point de terminaison privé de votre service Search.

1. Dans l’onglet Accueil du portail Azure, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.

1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Name | Entrez *MyVirtualNetwork* |
    | Espace d’adressage | Entrez *10.1.0.0/16* |
    | Subscription | Sélectionnez votre abonnement|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et cliquez sur **OK** |
    | Location | Sélectionnez **USA Ouest** ou la région que vous utilisez|
    | Sous-réseau - Nom | Entrez *mySubnet* |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24* |
    |||

1. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Créer un service Search avec un point de terminaison privé

Dans cette section, vous allez créer un service Recherche cognitive Azure avec un point de terminaison privé. 

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Web** > **Recherche cognitive Azure**.

1. Dans **Nouveau service Search – Concepts de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **DÉTAILS DE L’INSTANCE** |  |
    | URL | Entrez un nom unique. |
    | Location | Sélectionnez la région que vous avez indiquée lors de la demande d’accès à cette fonctionnalité d'évaluation. |
    | Niveau tarifaire | Sélectionnez **Modifier le niveau tarifaire**, puis **De base**. Ce niveau est requis pour la version préliminaire. |
    |||
  
1. Sélectionnez **Suivant : Mise à l'échelle**.

1. Conservez les valeurs par défaut, puis sélectionnez **Suivant : Mise en réseau**.

1. Dans **Nouveau service Search – Mise en réseau**, sélectionnez **Privé** pour la **Connectivité du point de terminaison (données)** .

1. Dans **Nouveau service Search – Mise en réseau**, sélectionnez **+ Ajouter** sous **Point de terminaison privé**. 

1. Dans **Créer un point de terminaison privé**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | Location | Sélectionnez **USA Ouest**.|
    | Name | Entrez *myPrivateEndpoint*.  |
    | Sous-ressource cible | Conservez le **searchService** par défaut. |
    | **MISE EN RÉSEAU** |  |
    | Réseau virtuel  | Sélectionnez  *MyVirtualNetwork* dans le groupe de ressources *myResourceGroup*. |
    | Subnet | Sélectionnez *mySubnet*. |
    | **INTÉGRATION À DNS PRIVÉ** |  |
    | Intégrer à une zone DNS privée  | Conservez la valeur par défaut **Oui**. |
    | Zone DNS privée  | Conservez la valeur par défaut ** privatelink.search.windows.net**. |
    |||

1. Sélectionnez **OK**. 

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 

1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**. 

1. Une fois la configuration de votre nouveau service terminée, accédez à la ressource que vous venez de créer.

1. Sélectionnez **Clés** dans le menu de contenu de gauche.

1. Copiez la **clé d’administration principale** pour plus tard, lors de la connexion au service.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

1. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.  |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm*. |
    | Région | Sélectionnez **USA Ouest** ou la région que vous utilisez. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter**. |
    | Size | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Conservez la valeur par défaut **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Conservez la valeur par défaut **RDP (3389)** . |
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
    ||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.

1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**. 


## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Procédez au téléchargement, puis connectez-vous à la machine virtuelle *myVm* comme suit :

1. Dans la barre de recherche du portail, entrez *myVm*.

1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

1. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( *.rdp*) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier .rdp* téléchargé.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.  


## <a name="test-connections"></a>Tester les connexions

Dans cette section, vous vérifierez l’accès au réseau privé du service Search et vous connecter en privé à l’aide du point de terminaison privé.

Rappelez-vous que nous avons lors de l’introduction que toutes les interactions avec le service Search requièrent [l’API REST de Recherche](https://docs.microsoft.com/rest/api/searchservice/). Le portail et le kit de développement logiciel (SDK) .NET ne sont pas pris en charge dans cette version préliminaire.

1. Dans le Bureau à distance de  *myVM*, ouvrez PowerShell.

1. Entrez « nslookup [nom du service Search].search.windows.net »

    Vous recevez un message similaire à celui ci :
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. À partir de la machine virtuelle, connectez-vous au service Search et créez un index. Vous pouvez suivre ce [démarrage rapide](search-get-started-postman.md) pour créer un index de recherche dans votre service dans Postman à l’aide de l’API REST. La configuration des demandes à partir de Postman nécessite le point de terminaison du service Search (https://[nom du service Search].search.windows.net) et la clé API de l’administrateur que vous avez copiée à l’étape précédente.

1. En effectuant le démarrage rapide à partir de la machine virtuelle, vous confirmez que le service est pleinement opérationnel.

1. Fermez la connexion Bureau à distance avec  *myVM*. 

1. Pour vérifier que votre service n’est pas accessible sur un point de terminaison public, ouvrez Postman sur votre station de travail locale et essayez d’effectuer les premières tâches du démarrage rapide. Si vous recevez un message d’erreur indiquant que le serveur distant n’existe pas, vous avez correctement configuré un point de terminaison privé pour votre service Search.

## <a name="clean-up-resources"></a>Nettoyer les ressources 
Lorsque vous avez fini d’utiliser le point de terminaison privé, le service Search et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient :
1. Entrez *myResourceGroup* dans la zone **Rechercher** en haut du portail, puis sélectionnez *myResourceGroup* dans les résultats de la recherche. 
1. Sélectionnez **Supprimer le groupe de ressources**. 
1. Entrez *myResourceGroup* pour **TAPEZ LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
Grâce à cet article, vous avez créé une machine virtuelle sur un réseau virtuel et un service Search avec un point de terminaison privé. Vous vous êtes connecté à la machine virtuelle à partir d’Internet et avez communiqué en toute sécurité avec le service Search à l’aide de la liaison privée. Pour en savoir plus sur le point de terminaison privé, consultez [Qu’est-ce qu’Azure Private Endpoint ?](../private-link/private-endpoint-overview.md).
