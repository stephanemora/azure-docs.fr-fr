---
title: Activer les liaisons privées Private Link - Azure Time Series Insights Gen2 | Microsoft Docs
description: Découvrez comment activer l’accès privé pour les solutions Azure Time Series Insights Gen2 avec Private Link, à l’aide du portail Azure.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 21a6c529aaa086fa75aef9e665cbbdce882baf5a
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129056693"
---
# <a name="enable-private-access-for-tsi-with-private-link-preview"></a>Activer l’accès privé pour TSI avec Private Link (préversion)

Cet article explique comment [activer Private Link avec un point de terminaison privé pour un environnement Azure Time Series Insights Gen2](concepts-private-links.md) (en préversion). La configuration d’un point de terminaison privé pour votre environnement Azure Time Series Insights Gen2 vous permet de sécuriser votre environnement Azure Time Series Insights, d’éliminer l’exposition publique et d’éviter l’exfiltration de données à partir de votre [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Cet article décrit le processus d’utilisation du [**portail Azure**](https://portal.azure.com).

Les étapes décrites dans cet article sont les suivantes : 
1. Activer Private Link et configurer un point de terminaison privé pour un environnement Time Series Insights Gen2.
1. Désactiver ou activer les indicateurs d’accès réseau public pour limiter l’accès aux connexions Private Link uniquement.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer un point de terminaison privé, vous avez besoin d’un [**réseau virtuel Azure (VNet)**](../virtual-network/virtual-networks-overview.md) où le point de terminaison peut être déployé. Si vous ne disposez pas déjà d’un réseau virtuel, vous pouvez suivre l’un des [démarrages rapides](../virtual-network/quick-create-portal.md) de réseau virtuel Azure pour en configurer un.

## <a name="add-a-private-endpoint-for-an-azure-time-series-insights-gen2-environment"></a>Ajouter un point de terminaison privé pour un environnement Azure Time Series Insights Gen2 

Quand vous utilisez le [portail Azure](https://portal.azure.com), vous pouvez activer Private Link avec un point de terminaison privé pour un environnement Azure Time Series Insights Gen2 dans le cadre de la configuration initiale de l’environnement, ou vous pouvez l’activer plus tard dans un environnement qui existe déjà. 

L’une ou l’autre de ces méthodes de création donne les mêmes options de configuration et le même résultat final pour votre environnement. Cette section montre comment effectuer chacune de ces opérations.

>[!TIP]
> Vous pouvez également configurer un point de terminaison Private Link via le service Private Link au lieu d’utiliser votre environnement Azure Time Series Insights Gen2. Vous obtenez également les mêmes options de configuration et le même résultat final.
>
> Pour plus d’informations sur la configuration des ressources de liaison privée, consultez la documentation sur les liaisons privées pour le [portail Azure](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-environment-creation"></a>Ajouter un point de terminaison privé durant la création de l’environnement

Dans cette section, vous allez activer Private Link avec un point de terminaison privé dans un environnement Azure Time Series Insights Gen2 en cours de création. Cette section se concentre sur l’étape de configuration réseau du processus de création. Pour une procédure pas à pas complète décrivant la création d’un environnement Azure Time Series Insights Gen2, consultez [*Procédure : Configurer un environnement*](tutorial-set-up-environment.md).

Les options relatives à Private Link se trouvent sous l’onglet **Réseau** de la configuration de l’environnement.

Dans cet onglet, vous pouvez activer des points de terminaison privés en sélectionnant l’option **Point de terminaison privé** comme **méthode de connectivité**.

Cette étape ajoute une section appelée **Connexions des points de terminaison privés**, dans laquelle vous pouvez configurer les détails de votre point de terminaison privé. Sélectionnez le bouton **Ajouter** pour continuer.

:::image type="content" source="media/private-links/create-instance-networking.png" alt-text="Capture d’écran du portail Azure montrant l’onglet Réseau de la boîte de dialogue Créer une ressource pour Time Series Insights Gen2. Le nom de l’onglet, l’option de point de terminaison privé pour la méthode de connectivité ainsi que le bouton + Ajouter permettant de créer une connexion de point de terminaison privé sont mis en évidence." lightbox="media/private-links/create-instance-networking.png":::

Une page s’ouvre, permettant d’entrer les détails d’un nouveau point de terminaison privé.

:::image type="content" source="media/private-links/create-private-endpoint.png" alt-text="Capture d’écran du portail Azure, affichant la page Créer un point de terminaison privé. Cette page contient les champs décrits ci-dessous.":::

1. Spécifiez les détails de votre **abonnement** et de votre **groupe de ressources**. Définissez l’option **Emplacement** sur le même emplacement que celui du réseau virtuel que vous allez utiliser. Choisissez le **nom** du point de terminaison, puis pour **Sous-ressources cibles**, sélectionnez *environment* ou *tsiExplorer*.

1. Sélectionnez ensuite le **réseau virtuel** et le **sous-réseau** que vous souhaitez utiliser pour déployer le point de terminaison.

1. Pour terminer, indiquez si vous souhaitez **intégrer à une zone DNS privée**. Vous pouvez utiliser la valeur par défaut **Oui** ou, pour obtenir de l’aide sur cette option, vous pouvez suivre le lien du portail pour [en savoir plus sur l’intégration à une zone DNS privée](../private-link/private-endpoint-overview.md#dns-configuration).

Après avoir défini les options de configuration, cliquez sur **OK** pour terminer.

Cela vous permet de retourner à l’onglet **Réseau** de la configuration de l’environnement Azure Time Series Insights Gen2, où votre nouveau point de terminaison doit être visible sous **Connexions de point de terminaison privé**.

Vous pouvez ensuite utiliser les boutons de navigation du bas pour passer au reste de la configuration de l’environnement.

### <a name="add-a-private-endpoint-to-an-existing-environment"></a>Ajouter un point de terminaison privé à un environnement existant

Dans cette section, vous allez activer Private Link avec un point de terminaison privé pour un environnement Azure Time Series Insights Gen2 déjà existant.

1. Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur. Affichez votre environnement Azure Time Series Insights Gen2 en recherchant son nom dans la barre de recherche du portail.

1. Sélectionnez **Mise en réseau (préversion)** dans le menu de gauche.

1. Basculez vers l’onglet **Connexions des points de terminaison privés**.

1. Sélectionnez **Point de terminaison privé** pour ouvrir la boîte de dialogue **Créer un point de terminaison privé**.

    :::image type="content" source="media/private-links/add-private-endpoint.png" alt-text="Capture d’écran du portail Azure montrant la page Réseau (préversion) d’un environnement Azure Time Series Insights Gen2. L’onglet Connexions de point de terminaison privé et le bouton + Point de terminaison privé sont mis en évidence." lightbox="media/private-links/add-private-endpoint.png":::

1. Dans l’onglet  **De base** , entrez ou sélectionnez l’**abonnement** et le **groupe de ressources** de votre projet, puis le **nom** et la **région** de votre point de terminaison. La région doit être la même que celle du réseau virtuel que vous utilisez.

    :::image type="content" source="media/private-links/create-private-endpoint-2.png" alt-text="Capture d’écran du portail Azure, affichant le premier onglet (De base) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Quand vous avez terminé, sélectionnez le bouton **Suivant : Bouton Ressource >** pour accéder à l’onglet suivant.

1. Dans l’onglet **Ressource**, entrez ou sélectionnez ces informations : 
    * **Méthode de connexion** : sélectionnez **Se connecter à une ressource Azure dans mon annuaire** pour rechercher votre environnement Azure Time Series Insights Gen2.
    * **Abonnement**: Entrez votre abonnement.
    * **Type de ressource** : sélectionnez **Microsoft.TimeSeriesInsights/environments**
    * **Ressource** : sélectionnez le nom de votre environnement Azure Time Series Insights Gen2.
    * **Sous-ressource cible** : sélectionnez **environment** ou **tsiExplorer**.

    :::image type="content" source="media/private-links/create-private-endpoint-3.png" alt-text="Capture d’écran du portail Azure, affichant le deuxième onglet (Ressource) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Quand vous avez terminé, sélectionnez le bouton **Suivant : Bouton Configuration >** pour accéder à l’onglet suivant.    

1. Dans l’onglet  **Configuration**, entrez ou sélectionnez les informations suivantes :
    * **Réseau virtuel** : Sélectionnez votre réseau virtuel.
    * **Sous-réseau** : Choisissez un sous-réseau de votre réseau virtuel.
    * **Intégrer à une zone DNS privée** : Indiquez si vous souhaitez **intégrer à une zone DNS privée**. Vous pouvez utiliser la valeur par défaut **Oui** ou, pour obtenir de l’aide sur cette option, vous pouvez suivre le lien du portail pour [en savoir plus sur l’intégration à une zone DNS privée](../private-link/private-endpoint-overview.md#dns-configuration).
    Si vous sélectionnez **Oui**, vous pouvez conserver les informations de configuration par défaut.

    :::image type="content" source="media/private-links/create-private-endpoint-4.png" alt-text="Capture d’écran du portail Azure, affichant le troisième onglet (Configuration) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Lorsque vous avez terminé, vous pouvez sélectionner le bouton **Vérifier + créer** pour terminer l’installation. 

1. Dans l’onglet **Vérifier + créer**, passez en revue vos sélections, puis cliquez sur le bouton  **Créer**. 

Une fois le déploiement du point de terminaison achevé, celui-ci doit apparaître dans les connexions de point de terminaison privé de votre environnement Azure Time Series Insights Gen2.

>[!TIP]
> Le point de terminaison privé peut également s’afficher dans le centre de liaisons privées du portail Azure.

## <a name="disable--enable-public-network-access-flags"></a>Activer/désactiver les indicateurs d’accès au réseau public

Vous pouvez configurer votre environnement Azure Time Series Insights Gen2 pour refuser toutes les connexions publiques et autoriser uniquement les connexions établies via des points de terminaison privés afin d’améliorer la sécurité du réseau. Cette action s’effectue à l’aide d’un **indicateur d’accès public au réseau**. 

Cette stratégie vous permet de limiter l’accès aux connexions Private Link uniquement. Quand l’indicateur d’accès réseau public a la valeur *Désactivé*, tous les appels d’API REST au plan de données de l’environnement Azure Time Series Insights Gen2 à partir du cloud public retournent `403, Unauthorized`. Par ailleurs, lorsque la stratégie est définie sur *désactivé* et qu’une demande est effectuée via un point de terminaison privé, l’appel d’API échoue.

Pour désactiver ou activer l’accès réseau public dans le [portail Azure](https://portal.azure.com), ouvrez le portail, puis accédez à votre environnement Azure Time Series Insights Gen2.

1. Sélectionnez **Mise en réseau (préversion)** dans le menu de gauche.

1. Sous l’onglet **Accès public**, dans **Autoriser l’accès public au réseau**, sélectionnez **Désactivé** ou **Tous les réseaux**.

    :::row:::
        :::column:::
            :::image type="content" source="media/private-links/network-flag-portal.png" alt-text="Capture d’écran du portail Azure montrant la page Réseau (préversion) d’un environnement Azure Time Series Insights Gen2. L’onglet Accès public et l’option permettant d’autoriser ou non l’accès réseau public sont mis en évidence." lightbox="media/private-links/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les liaisons privées pour Azure : 
* [*Qu’est-ce que le service Azure Private Link ?*](../private-link/private-link-service-overview.md)