---
title: Activer l’accès privé avec Private Link (préversion) - Portail
titleSuffix: Azure Digital Twins
description: Découvrez comment activer l’accès privé pour les solutions Azure Digital Twins avec Private Link, à l’aide du portail Azure.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68c76c92f37ae424bd7e8bc85557e6c204ba742e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108734198"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Activer l’accès privé avec Private Link (préversion) : Portail Azure

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Cet article décrit les différentes façons d'[activer une liaison privée avec un point de terminaison privé pour une instance Azure Digital Twins](concepts-security.md#private-network-access-with-azure-private-link-preview) (actuellement en préversion). La configuration d’un point de terminaison privé pour votre instance Azure Digital Twins vous permet de sécuriser cette instance et d’éliminer l’exposition publique, tout en évitant l’exfiltration de données à partir de votre [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Cet article décrit le processus d’utilisation du [portail Azure](https://portal.azure.com).

Les étapes décrites dans cet article sont les suivantes : 
1. Activer la liaison privée et configurer un point de terminaison privé pour une instance Azure Digital Twins.
1. Désactiver ou activer des indicateurs d’accès au réseau public pour limiter l’accès de l’API aux connexions de liaison privées uniquement.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer un point de terminaison privé, vous avez besoin d’un [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md)  où le point de terminaison peut être déployé. Si vous ne disposez pas déjà d’un réseau virtuel, vous pouvez suivre l’un des [démarrages rapides](../virtual-network/quick-create-portal.md) de réseau virtuel Azure pour en configurer un.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Ajouter un point de terminaison privé pour une instance Azure Digital Twins 

Quand vous utilisez le [portail Azure](https://portal.azure.com), vous pouvez activer Private Link avec un point de terminaison privé pour une instance d’Azure Digital Twins dans le cadre de la configuration initiale de l’instance, ou vous pouvez l’activer plus tard sur une instance qui existe déjà. 

L’une ou l’autre de ces méthodes de création donnera les mêmes options de configuration et le même résultat final pour votre instance. Cette section montre comment effectuer chacune de ces opérations.

>[!TIP]
> Vous pouvez également configurer un point de terminaison de liaison privé via le service de liaison privée, plutôt que par le biais de votre instance Azure Digital Twins. Vous obtenez également les mêmes options de configuration et le même résultat final.
>
> Pour plus d’informations sur la configuration des ressources de liaison privée, consultez la documentation sur les liaisons privées pour le [portail Azure](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Ajouter un point de terminaison privé pendant la création de l’instance

Dans cette section, vous allez activer Private Link avec un point de terminaison privé sur une instance d’Azure Digital Twins en cours de création. Cette section se concentre sur l’étape de mise en réseau du processus de création. Pour une procédure pas à pas complète de création d’une instance Azure Digital Twins, consultez [Guide pratique : Configurer une instance et l’authentification](how-to-set-up-instance-portal.md).

Les options de liaison privée se trouvent dans l’onglet **Mise en réseau** de la configuration de l’instance.

Dans cet onglet, vous pouvez activer des points de terminaison privés en sélectionnant l’option **Point de terminaison privé** comme **méthode de connectivité**.

Cette étape ajoute une section appelée **Connexions des points de terminaison privés**, dans laquelle vous pouvez configurer les détails de votre point de terminaison privé. Sélectionnez le bouton **Ajouter** pour continuer.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Capture d’écran du portail Azure, montrant l’onglet Mise en réseau de la boîte de dialogue Créer une ressource pour Azure Digital Twins. Une mise en surbrillance est appliquée au nom de l’onglet, à l’option Point de terminaison privé pour la méthode de connectivité, et au bouton Ajouter pour créer une connexion de point de terminaison privé." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Une page s’ouvre, permettant d’entrer les détails d’un nouveau point de terminaison privé.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Capture d’écran du portail Azure, affichant la page Créer un point de terminaison privé. Cette page contient les champs décrits ci-dessous.":::

1. Spécifiez les détails de votre **abonnement** et de votre **groupe de ressources**. Définissez l’option **Emplacement** sur le même emplacement que celui du réseau virtuel que vous allez utiliser. Choisissez le **nom** du point de terminaison puis, pour **Sous-ressources cibles**, sélectionnez *API*.

1. Sélectionnez ensuite le **réseau virtuel** et le **sous-réseau** que vous souhaitez utiliser pour déployer le point de terminaison.

1. Pour terminer, indiquez si vous souhaitez **intégrer à une zone DNS privée**. Vous pouvez utiliser la valeur par défaut **Oui** ou, pour obtenir de l’aide sur cette option, vous pouvez suivre le lien du portail pour [en savoir plus sur l’intégration à une zone DNS privée](../private-link/private-endpoint-overview.md#dns-configuration).

Après avoir défini les options de configuration, sélectionnez **OK** pour terminer.

Vous revenez alors à l’onglet **Mise en réseau** de la configuration de l’instance Azure Digital Twins, où votre nouveau point de terminaison doit être visible sous **Connexions des points de terminaison privés.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Capture d’écran du portail Azure, montrant l’onglet Mise en réseau de la boîte de dialogue Créer une ressource pour Azure Digital Twins. Une mise en surbrillance est appliquée à la nouvelle connexion de point de terminaison privé et aux boutons de navigation (Vérifier+ créer, Précédent, Suivant : Avancé)." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Vous pouvez ensuite utiliser les boutons de navigation inférieurs pour continuer le reste de la configuration de l’instance.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Ajouter un point de terminaison privé à une instance existante

Dans cette section, vous allez activer Private Link avec un point de terminaison privé pour une instance d’Azure Digital Twins qui existe déjà.

1. Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur. Affichez votre instance Azure Digital Twins en recherchant son nom dans la barre de recherche du portail.

1. Sélectionnez **Mise en réseau (préversion)** dans le menu de gauche.

1. Basculez vers l’onglet **Connexions des points de terminaison privés**.

1. Sélectionnez **Point de terminaison privé** pour ouvrir la boîte de dialogue **Créer un point de terminaison privé**.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Capture d’écran du portail Azure, montrant la page Mise en réseau (préversion) pour une instance Azure Digital Twins. L’onglet Connexions des points de terminaison privés est mis en surbrillance, tout comme le bouton Point de terminaison privé." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Dans l’onglet  **De base** , entrez ou sélectionnez l’**abonnement** et le **groupe de ressources** de votre projet, puis le **nom** et la **région** de votre point de terminaison. La région doit être la même que celle du réseau virtuel que vous utilisez.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Capture d’écran du portail Azure, affichant le premier onglet (De base) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Quand vous avez terminé, sélectionnez le bouton **Suivant : Bouton Ressource >** pour accéder à l’onglet suivant.

1. Dans l’onglet **Ressource**, entrez ou sélectionnez ces informations : 
    * **Méthode de connexion** : Sélectionnez **Se connecter à une ressource Azure dans mon répertoire** pour rechercher votre instance Azure Digital Twins.
    * **Abonnement**: Entrez votre abonnement.
    * **Type de ressource** : Sélectionnez **Microsoft.DigitalTwins/digitalTwinsInstances**
    * **Ressource** : Sélectionnez le nom de votre instance Azure Digital Twins.
    * **Sous-ressource cible** : Sélectionnez **l’API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Capture d’écran du portail Azure, affichant le deuxième onglet (Ressource) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Quand vous avez terminé, sélectionnez le bouton **Suivant : Bouton Configuration >** pour accéder à l’onglet suivant.    

1. Dans l’onglet  **Configuration**, entrez ou sélectionnez les informations suivantes :
    * **Réseau virtuel** : Sélectionnez votre réseau virtuel.
    * **Sous-réseau** : Choisissez un sous-réseau de votre réseau virtuel.
    * **Intégrer à une zone DNS privée** : Indiquez si vous souhaitez **intégrer à une zone DNS privée**. Vous pouvez utiliser la valeur par défaut **Oui** ou, pour obtenir de l’aide sur cette option, vous pouvez suivre le lien du portail pour [en savoir plus sur l’intégration à une zone DNS privée](../private-link/private-endpoint-overview.md#dns-configuration).
    Si vous sélectionnez **Oui**, vous pouvez conserver les informations de configuration par défaut.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Capture d’écran du portail Azure, affichant le troisième onglet (Configuration) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Lorsque vous avez terminé, vous pouvez sélectionner le bouton **Vérifier + créer** pour terminer l’installation. 

1. Dans l’onglet **Vérifier + créer**, passez en revue vos sélections, puis cliquez sur le bouton  **Créer**. 

Une fois le déploiement du point de terminaison terminé, le point de terminaison devrait apparaître dans les connexions des points de terminaison privés de votre instance Azure Digital Twins.

>[!TIP]
> Le point de terminaison privé peut également s’afficher dans le centre de liaisons privées du portail Azure.

## <a name="disable--enable-public-network-access-flags"></a>Activer/désactiver les indicateurs d’accès au réseau public

Vous pouvez configurer une instance Azure Digital Twins pour refuser toutes les configurations publiques et n’autoriser que les connexions établies par le biais de points de terminaison privés afin d’améliorer la sécurité réseau. Cette action s’effectue à l’aide d’un **indicateur d’accès public au réseau**. 

Cette stratégie vous permet de limiter l’accès de l’API aux connexions de liaison privées uniquement. Lorsque l’indicateur d’accès public au réseau est défini sur *désactivé*, tous les appels de l’API REST vers le plan de données de l’instance Azure Digital Twins à partir du cloud public retournent `403, Unauthorized`. Par ailleurs, lorsque la stratégie est définie sur *désactivé* et qu’une demande est effectuée via un point de terminaison privé, l’appel d’API échoue.

Cet article montre comment mettre à jour la valeur de l’indicateur réseau à l’aide du [portail Azure](https://portal.azure.com). Pour savoir comment y parvenir à l’aide d’Azure CLI ou de l’outil en ligne de commande ARMClient, consultez la [version dédiée à CLI](how-to-enable-private-link-cli.md) de cet article.

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Pour désactiver ou activer l’accès public au réseau dans le [portail Azure](https://portal.azure.com), ouvrez le portail, puis accédez à votre instance Azure Digital Twins.

1. Sélectionnez **Mise en réseau (préversion)** dans le menu de gauche.

1. Sous l’onglet **Accès public**, dans **Autoriser l’accès public au réseau**, sélectionnez **Désactivé** ou **Tous les réseaux**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Capture d’écran du portail Azure, montrant la page Mise en réseau (préversion) pour une instance Azure Digital Twins. L’onglet Accès public est mis en surbrillance, et l’option permettant de choisir d’autoriser ou non l’accès public au réseau est également mise en surbrillance." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les liaisons privées pour Azure : 
* [Qu’est-ce que le service Azure Private Link ?](../private-link/private-link-service-overview.md)