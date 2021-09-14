---
title: Utiliser des points de terminaison de service de réseau virtuel avec le service Speech
titleSuffix: Azure Cognitive Services
description: Cet article explique comment utiliser le service Speech avec un point de terminaison de service de réseau virtuel Azure.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 903ce0888aa04e7005468ca4f8aec9cf977ead5d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114490211"
---
# <a name="use-speech-service-through-a-virtual-network-service-endpoint"></a>Utiliser le service Speech par le biais d’un point de terminaison de service de réseau virtuel

Les [points de terminaison de service](../../virtual-network/virtual-network-service-endpoints-overview.md) de [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md) contribuent à fournir une connectivité sécurisée et directe aux services Azure sur une route optimisée du réseau principal Azure. Les points de terminaison permettent de sécuriser vos ressources critiques du service Azure pour vos réseaux virtuels uniquement. Les points de terminaison de service permettent aux adresses IP privées du réseau virtuel d’atteindre le point de terminaison d’un service Azure sans qu’une adresse IP publique soit nécessaire sur le réseau virtuel.

Cet article explique comment configurer et utiliser des points de terminaison de service de réseau virtuel avec le service Speech dans Azure Cognitive Services.

> [!NOTE]
> Avant de commencer, consultez l’article qui explique [comment utiliser des réseaux virtuels avec Cognitive Services](../cognitive-services-virtual-networks.md).

Cet article explique également [comment supprimer des points de terminaison de service de réseau virtuel tout en continuant à utiliser la ressource Speech](#use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks).

Afin de configurer une ressource Speech pour des scénarios de point de terminaison de service de réseau virtuel, vous devez :
1. [Créer un nom de domaine personnalisé pour la ressource Speech](#create-a-custom-domain-name)
1. [Configurer des réseaux virtuels et des paramètres réseau pour la ressource Speech](#configure-virtual-networks-and-the-speech-resource-networking-settings)
1. [Ajuster les solutions et applications existantes](#adjust-existing-applications-and-solutions)

> [!NOTE]
> La configuration et l’utilisation de points de terminaison de service de réseau virtuel pour le service Speech sont similaires à la configuration et à l’utilisation de points de terminaison privés. Dans cet article, nous faisons référence aux sections correspondantes de l’[article sur l’utilisation de points de terminaison privés](speech-services-private-link.md) quand les procédures sont identiques.

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints.md)]

Cet article explique comment utiliser des points de terminaison de service de réseau virtuel avec le service Speech. Pour plus d’informations sur les points de terminaison privés, consultez [Utiliser le service Speech via un point de terminaison privé](speech-services-private-link.md).

## <a name="create-a-custom-domain-name"></a>Créer un nom de domaine personnalisé

Les points de terminaison de service de réseau virtuel nécessitent un [nom de sous-domaine personnalisé pour Cognitive Services](../cognitive-services-custom-subdomains.md). Créez un domaine personnalisé en suivant les [instructions](speech-services-private-link.md#create-a-custom-domain-name) de l’article sur l’utilisation d’un point de terminaison privé. Tous les avertissements figurant dans cette section s’appliquent également aux points de terminaison de service de réseau virtuel.

## <a name="configure-virtual-networks-and-the-speech-resource-networking-settings"></a>Configurer des réseaux virtuels et les paramètres réseau de la ressource Speech

Vous devez ajouter tous les réseaux virtuels qui sont autorisés à accéder aux propriétés réseau de la ressource Speech par le biais du point de terminaison de service.

> [!NOTE]
> Pour accéder à une ressource Speech par le biais du point de terminaison de service de réseau virtuel, vous devez activer le type de point de terminaison de service `Microsoft.CognitiveServices` pour les sous-réseaux nécessaires de votre réseau virtuel. Ceci permet de router tout le trafic de sous-réseau lié à Cognitive Services par le biais du réseau principal privé. Si vous envisagez d’accéder à d’autres ressources Cognitive Services à partir du même sous-réseau, veillez à ce que ces ressources soient configurées pour autoriser votre réseau virtuel. 
>
> Un réseau virtuel qui n’est pas ajouté comme *autorisé* dans les propriétés réseau de la ressource Speech n’aura pas accès à cette ressource par le biais du point de terminaison de service, même si le point de terminaison de service `Microsoft.CognitiveServices` est activé pour le réseau virtuel. Par ailleurs, si le point de terminaison de service est activé, mais que le réseau virtuel n’est pas autorisé, la ressource Speech n’est pas accessible pour le réseau virtuel par le biais d’une adresse IP publique, quels que soient les autres paramètres de sécurité réseau de la ressource Speech. C’est parce que l’activation du point de terminaison `Microsoft.CognitiveServices` route tout le trafic lié à Cognitive Services par le biais du réseau principal privé et que, dans ce cas, le réseau virtuel doit être explicitement autorisé à accéder à la ressource. Cette indication ne s’applique pas seulement aux ressources Speech, mais à toutes les ressources Cognitive Services.  
  
1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte Azure.
1. Sélectionnez la ressource Speech.
1. Dans le groupe **Gestion des ressources** dans le volet gauche, sélectionnez **Réseau**.
1. Sous l’onglet **Pare-feux et réseaux virtuels**, sélectionnez **Réseaux et points de terminaison privés sélectionnés**. 

   > [!NOTE]
   > Pour utiliser des points de terminaison de service de réseau virtuel, vous devez sélectionner l’option de sécurité réseau **Réseaux et points de terminaison privés sélectionnés**. Aucune autre option n’est prise en charge. Si votre scénario nécessite l’option **Tous les réseaux**, envisagez d’utiliser des [points de terminaison privés](speech-services-private-link.md), qui prennent en charge les trois options de sécurité réseau.

5. Sélectionnez **Ajouter un réseau virtuel existant** ou **Ajouter un nouveau réseau virtuel** et indiquez les paramètres requis. Sélectionnez **Ajouter** pour ajouter un réseau virtuel existant ou **Créer** pour en ajouter un nouveau. Si vous ajoutez un réseau virtuel existant, le point de terminaison de service `Microsoft.CognitiveServices` est automatiquement activé pour les sous-réseaux sélectionnés. Cette opération peut prendre jusqu’à 15 minutes. Consultez également la note au début de cette section.

### <a name="enabling-service-endpoint-for-an-existing-virtual-network"></a>Activer un point de terminaison de service pour un réseau virtuel existant 

Comme décrit dans la section précédente, quand vous configurez un réseau virtuel comme *autorisé* pour la ressource Speech, le point de terminaison de service `Microsoft.CognitiveServices` est automatiquement activé. Si vous le désactivez par la suite, vous devez le réactiver manuellement pour restaurer l’accès du point de terminaison de service à la ressource Speech (et à d’autres ressources Cognitive Services) :

1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte Azure.
1. Sélectionnez le réseau virtuel.
1. Dans le groupe **Paramètres** dans le volet de gauche, sélectionnez **Sous-réseaux**.
1. Sélectionnez le sous-réseau nécessaire.
1. Un nouveau panneau s’affiche sur le côté droit de la fenêtre. Dans ce panneau, dans la section **Points de terminaison de service**, sélectionnez `Microsoft.CognitiveServices` dans la liste **Services**.
1. Sélectionnez **Enregistrer**.

## <a name="adjust-existing-applications-and-solutions"></a>Ajuster les solutions et applications existantes

Une ressource Speech avec un domaine personnalisé activé interagit avec le service Speech d’une manière différente. C’est le cas pour une ressource Speech avec un domaine personnalisé activé, que des points de terminaison de service soient ou non configurés. Les informations contenues dans cette section s’appliquent aux deux scénarios.

### <a name="use-a-speech-resource-that-has-a-custom-domain-name-and-allowed-virtual-networks"></a>Utiliser une ressource Speech avec un nom de domaine personnalisé et des réseaux virtuels autorisés 

Dans ce scénario, l’option **Réseaux et points de terminaison privés sélectionnés** est sélectionnée dans les paramètres réseau de la ressource Speech et au moins un réseau virtuel est autorisé. Ce scénario correspond à l’[utilisation d’une ressource Speech qui a un nom de domaine personnalisé et un point de terminaison privé activé](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint).


### <a name="use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks"></a>Utiliser une ressource Speech avec un nom de domaine personnalisé, mais sans réseaux virtuels autorisés

Dans ce scénario, aucun point de terminaison privé n’est activé et l’une des conditions suivantes se vérifie :

- L’option **Réseaux et points de terminaison privés sélectionnés** est sélectionnée dans les paramètres réseau de la ressource Speech, mais aucun réseau virtuel autorisé n’est configuré.
- L’option **Tous les réseaux** est sélectionnée dans les paramètres réseau de la ressource Speech.

Ce scénario correspond à l’[utilisation d’une ressource Speech qui a un nom de domaine personnalisé, mais n’a pas de point de terminaison privé](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-without-private-endpoints).


[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints-simultaneously.md)]


## <a name="learn-more"></a>En savoir plus

* [Utiliser le service Speech via un point de terminaison privé](speech-services-private-link.md)
* [Points de terminaison de service de réseau virtuel Azure](../../virtual-network/virtual-network-service-endpoints-overview.md)
* [Azure Private Link](../../private-link/private-link-overview.md)
* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk.md)
* [API REST de reconnaissance vocale](rest-speech-to-text.md)
* [API REST de synthèse vocale](rest-text-to-speech.md)
