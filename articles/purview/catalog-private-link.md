---
title: Utiliser des points de terminaison privés pour un accès sécurisé à Purview
description: Cet article décrit comment vous pouvez configurer un point de terminaison privé pour votre compte Purview
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 3193c5c00789b793a5b5beaf662f94ab9525888a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107032"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Utiliser des points de terminaison privés pour votre compte Purview

Vous pouvez utiliser des points de terminaison privés pour vos comptes Purview afin de permettre aux clients et utilisateurs d’un réseau virtuel (VNet) d’accéder en toute sécurité au catalogue via une liaison privée. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre compte Purview. Le trafic réseau entre les clients sur le réseau virtuel et le stockage Purview traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, éliminant ainsi l’exposition sur l’Internet public.

## <a name="create-purview-account-with-a-private-endpoint"></a>Créer un compte Purview avec un point de terminaison privé

1. Accédez au [portail Azure](https://portal.azure.com), puis à votre compte Purview.

1. Entrez les informations de base, puis, sous l’onglet **Mise en réseau**, définissez la méthode de connectivité sur Point de terminaison privé. Configurez vos points de terminaison privés d’ingestion en fournissant des détails sur **l’abonnement, le réseau virtuel et le sous-réseau** que vous souhaitez appairer avec votre point de terminaison privé.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Création de PE durant la création de compte":::

1. Vous pouvez également configurer une **zone DNS privée** pour chaque point de terminaison privé d’ingestion.

1. Cliquez sur Ajouter afin d’ajouter un point de terminaison privé pour votre compte Purview.

1. Dans le panneau Créer un point de terminaison privé, définissez la sous-ressource Purview sur **compte**, choisissez vos réseau virtuel et sous-réseau, puis sélectionnez la zone DNS privée dans laquelle le DNS sera inscrit (vous pouvez également utiliser vos serveurs DNS gagnés ou créer des enregistrements DNS à l’aide de fichiers hôtes sur vos machines virtuelles).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Création de PE durant la création de compte":::

1. Sélectionnez **OK**.

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page Vérifier + créer où Azure valide votre configuration.

1. Quand le message Validation réussie s’affiche, sélectionnez **Créer**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Validation réussie pour la création de compte":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Créer un point de terminaison privé pour le portail web Purview

1. Accédez au compte Purview que vous venez de créer, puis, dans la section Paramètres, sélectionnez les connexions de point de terminaison privée.
    
1. Cliquez sur +Point de terminaison privé pour créer un point de terminaison privé.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Créer un point de terminaison privé du portail":::

1. Entrez les informations de base.

1. Sous l’onglet Ressource, sélectionnez le type de ressource **Microsoft.Purview/account**.

1. Sélectionnez comme Ressource le compte Purview nouvellement créé, puis sélectionnez la sous-ressource cible **portail**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Détails du point de terminaison privé du portail":::

1. Sous l’onglet Configuration, sélectionnez le réseau virtuel et la zone DNS privée. Accédez à la page de résumé, puis cliquez sur **Créer** pour créer le point de terminaison privé du portail.

## <a name="enabling-access-to-azure-active-directory"></a>Activation de l’accès à Azure Active Directory

> [!NOTE]
> Si votre machine virtuelle, passerelle VPN ou passerelle de peering de réseau virtuel disposent d’un accès Internet public, elles peuvent accéder au portail et au compte Purview activés avec des points de terminaison privés, et vous n’êtes pas obligé de suivre le reste des instructions ci-dessous. Si votre réseau privé a des règles de groupe de sécurité réseau définies pour refuser tout trafic Internet public, vous devez ajouter des règles pour activer l’accès à AAD. Pour ce faire, suivez les instructions ci-dessous.

Les instructions ci-dessous ont trait à l’accès à Purview en toute sécurité à partir d’une machine virtuelle Azure. Vous devez suivre des étapes similaires si vous utilisez un VPN ou d’autres passerelles de peering de réseaux virtuels.

1. Accédez à votre machine virtuelle dans le portail Azure, puis, dans la section Paramètres, sélectionnez l’onglet Mise en réseau. Sélectionnez ensuite des règles de port de sortie, puis cliquez sur Ajouter une règle de port de sortie.

    :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Ajout d’une règle de trafic sortant":::

2. Dans le panneau Ajouter une règle de port de sortie, sélectionnez Étiquette de service en tant que *Destination*, **AzureActiveDirectory** en tant qu’Étiquette de service de destination, * en tant que Plages de ports de destination, autoriser en tant qu’Action, puis **La priorité doit être supérieure à la règle qui a refusé tout trafic Internet**. Créez la règle.

    :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Ajout de détails de règle de trafic sortant":::
 
3. Suivez les mêmes étapes pour créer une autre règle afin d’autoriser l’étiquette de service « **AzureResourceManager** ». Si vous avez besoin d’accéder au portail Azure, vous pouvez également ajouter une règle pour l’étiquette de service « *AzurePortal* ».

4. Connectez-vous à la machine virtuelle, ouvrez le navigateur, accédez à la console du navigateur (Ctrl + Maj + J), puis basculez vers l’onglet du réseau pour surveiller les demandes réseau. Entrez web.purview.azure.com dans la zone URL, puis essayez de vous connecter à l’aide de vos informations d’identification AAD. Il est probable que la connexion échoue et, sous l’onglet du réseau sur la console, vous voyiez AAD tenter d’accéder à aadcdn.msauth.net mais être bloqué.

    :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Détails d’échec de connexion":::
 
5. Dans ce cas, ouvrez une invite de commandes sur la machine virtuelle, effectuez un test ping de cette URL (aadcdn.msauth.net), récupérez son adresse IP, puis ajoutez une règle de port de sortie pour l’adresse IP dans les règles de sécurité réseau de la machine virtuelle. Définissez la Destination sur Adresse IP et définissez les Adresses IP de destination sur l’adresse IP d’aadcdn. En raison de l’équilibreur de charge et du gestionnaire de trafic, l’adresse IP du CDN AAD pourrait être dynamique. Une fois que vous avez obtenu son adresse IP, il est préférable de l’ajouter dans le fichier hôte de la machine virtuelle pour forcer le navigateur à visiter cette adresse IP afin d’obtenir le CDN AAD.

    :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Test ping":::

    :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Règle de CDN AAD":::
 
6. Une fois la nouvelle règle créée, revenez à la machine virtuelle et réessayez de vous connecter à l’aide de vos informations d’identification AAD. Si la connexion réussit, le portail Purview est prêt à être utilisé. Toutefois, dans certains cas, AAD opère une redirection vers d’autres domaines pour se connecter en fonction du type de compte du client. Par exemple, pour un compte live.com, AAD redirige vers live.com pour la connexion, puis ces demandes sont à nouveau bloquées. Pour des comptes d’employés Microsoft, AAD accède à msft.sts.microsoft.com pour les informations de connexion. Vérifiez les demandes de mise en réseau sous l’onglet de mise en réseau du navigateur pour afficher les demandes du domaine qui sont bloquées, recommencez l’étape précédente pour obtenir son adresse IP, puis ajoutez des règles de port de sortie dans le groupe de sécurité réseau afin d’autoriser les demandes pour cette adresse IP (si possible, ajoutez l’URL et l’adresse IP au fichier hôte de la machine virtuelle pour corriger la résolution DNS). Si vous connaissez les plages exactes d’adresses IP du domaine de connexion, vous pouvez également les ajouter directement dans les règles de mise en réseau.

7. La connexion à AAD devrait à présent réussir. Le portail Purview se chargera correctement, mais la liste de tous les comptes Purview ne fonctionnera pas, car il ne peut accéder qu’à un compte Purview spécifique. Entrez *web.purview.azure.com/resource/{NomComptePurview}* pour accéder directement au compte Purview pour lequel vous avez correctement configuré un point de terminaison privé.

##  <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Activer un point de terminaison privé sur des comptes Purview existants

Il existe deux façons d’ajouter des points de terminaison privés Purview après avoir créé votre compte Purview :
- Utilisation du portail Azure (compte Purview)
- Utilisation du Centre Private Link

### <a name="using-the-azure-portal-purview-account"></a>Utilisation du portail Azure (compte Purview)

1. Accédez au compte Purview à partir du portail Azure, sélectionnez les connexions de point de terminaison privés dans la section **Mise en réseau** de **Paramètres**.

:::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Créer un point de terminaison privé du portail":::
    
2. Cliquez sur +Point de terminaison privé pour créer un point de terminaison privé.

3. Entrez les informations de base.

4. Sous l’onglet Ressource, sélectionnez le type de ressource **Microsoft.Purview/account**.

5. Sélectionnez le compte Purview en tant que Ressource, puis **compte** en tant que sous-ressource cible.

6. Sous l’onglet Configuration, sélectionnez le **réseau virtuel** et la **zone DNS privée**. Accédez à la page de résumé, puis cliquez sur **Créer** pour créer le point de terminaison privé du portail.

> [!NOTE]
> Vous devrez suivre les mêmes étapes que ci-dessus pour la sous-ressource cible sélectionnée en tant que **Portail** .

### <a name="using-the-private-link-center"></a>Utilisation du Centre Private Link

1. Accédez au [portail Azure](https://portal.azure.com).

2. Dans la barre de recherche en haut de la page, recherchez « liaison privée » et accédez au panneau Liaison privée en cliquant sur la première option.

3. Cliquez sur « + Ajouter » et entrez les détails de base.

    :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Créer un PE à partir du Centre Private Link":::

4. Sélectionnez comme Ressource le compte Purview nouvellement créé, puis sélectionnez la sous-ressource cible **compte**.

5. Sous l’onglet Configuration, sélectionnez le réseau virtuel et la zone DNS privée. Accédez à la page de résumé, puis cliquez sur **Créer** pour créer le point de terminaison privé du compte.

> [!NOTE]
> Vous devrez suivre les mêmes étapes que ci-dessus pour la sous-ressource cible sélectionnée en tant que **Portail** .

## <a name="next-steps"></a>Étapes suivantes

- [Naviguer dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)  