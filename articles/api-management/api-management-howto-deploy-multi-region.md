---
title: Déployer des services Gestion des API Azure dans plusieurs régions Azure
titleSuffix: Azure API Management
description: Découvrez comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442662"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure

La Gestion des API Azure prend en charge le déploiement sur plusieurs régions, ce qui permet aux éditeurs d’API de ne distribuer qu’un seul service de Gestion des API Azure sur un nombre de régions Azure prises en charge. Cette fonctionnalité sur plusieurs régions permet de réduire la latence de la demande telle qu’elle est perçue par les utilisateurs distribués de l’API. La disponibilité du service est améliorée si une région est mise hors connexion.

Un nouveau service de Gestion des API Azure contient initialement une seule [unité][unit] dans une seule région Azure, la Région primaire. Des régions supplémentaires peuvent être ajoutées aux régions primaires ou secondaires. Un composant de passerelle de gestion des API est déployé sur chaque région principale et secondaire sélectionnée. Les requêtes d’API entrantes sont dirigées automatiquement vers la région la plus proche. Si une région est déconnectée, les requêtes d’API sont acheminées automatiquement autour de la région défaillante vers la passerelle suivante la plus proche.

> [!NOTE]
> Seul le composant passerelle de la gestion des API est déployé dans toutes les régions. Le composant de gestion des services et le portail des développeurs sont hébergés uniquement dans la région principale. Par conséquent, en cas de panne de la région primaire, l’accès au portail des développeurs et la possibilité de modifier la configuration (par exemple, l’ajout d’API et l’application de stratégies) seront altérés jusqu’à ce que la région primaire soit de nouveau en ligne. Lorsque la région primaire n’est pas en ligne, les régions secondaires disponibles continuent de servir le trafic d’API à l’aide de la dernière configuration disponible.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Déployer une instance de service Gestion des API dans une nouvelle région

> [!NOTE]
> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][create an api management service instance].

Dans le portail Azure, accédez à la page **Mise à l’échelle et tarification** de votre instance de service de Gestion des API.

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour procéder au déploiement vers une nouvelle région, cliquez sur **+ Ajouter une région** dans la barre d’outils.

![Ajouter une région][api-management-add-region]

Sélectionnez l’emplacement dans la liste déroulante et définissez le nombre d’unités à l’aide du curseur.

![Spécifier les unités][api-management-select-location-units]

Cliquez sur **Ajouter** pour placer votre sélection dans la table des emplacements.

Répétez cette procédure jusqu’à ce que vous ayez configuré tous les emplacements, puis cliquez sur **Enregistrer** dans la barre d’outils pour démarrer le déploiement.

## <a name="remove-region"> </a>Supprimer une instance de service Gestion des API d’un emplacement

Dans le portail Azure, accédez à la page **Mise à l’échelle et tarification** de votre instance de service de Gestion des API.

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour l’emplacement que vous souhaitez supprimer, ouvrez le menu contextuel à l’aide du bouton **...** situé à l’extrême droite du tableau. Sélectionnez l’option **Supprimer**.

Confirmez la suppression, puis cliquez sur **Enregistrer** pour appliquer les modifications.

## <a name="route-backend"> </a>Acheminer les appels d’API à des services back-end régionaux

La Gestion des API Azure ne comprend qu’une seule URL de service principal. Même s’il existe des instances de la Gestion des API Azure dans différentes régions, la passerelle d’API transférera toujours les demandes vers le même service principal, qui est déployé dans une seule région. Dans ce cas, le gain de performances ne proviendra que des réponses mises en cache dans la Gestion des API Azure dans une région spécifique à la demande, mais contacter le serveur principal dans le monde entier pourra toujours entraîner une latence élevée.

Pour tirer pleinement parti de la distribution géographique de votre système, vous devez disposer des services principaux déployés dans les régions mêmes en tant qu’instances de la Gestion des API Azure. Ensuite, à l’aide de stratégies et de la propriété `@(context.Deployment.Region)`, vous pouvez acheminer le trafic vers des instances locales de votre serveur principal.

1. Accédez à votre instance de Gestion des API Azure et cliquez sur **API** dans le menu de gauche.
2. Sélectionnez l’API souhaitée.
3. Cliquez sur **Éditeur de Code** dans la flèche de la liste déroulante dans le **Traitement entrant**.

    ![Éditeur de code API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Utilisez le `set-backend` combiné avec les stratégies `choose` conditionnelles pour construire une stratégie de routage appropriée dans la section `<inbound> </inbound>` du fichier.

    Par exemple, le fichier XML ci-dessous fonctionnerait pour les régions USA Ouest et Asie Est :

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Vous pouvez également proposer des services back-end avec [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), diriger les appels d’API vers Traffic Manager et le laisser résoudre le routage automatiquement.

## <a name="custom-routing"> </a>Utiliser le routage personnalisé vers des passerelles régionales du service Gestion des API

Le service Gestion des API route les demandes vers une _passerelle_ régionale selon la [plus faible latence](../traffic-manager/traffic-manager-routing-methods.md#performance). Même s’il n’est pas possible de remplacer ce paramètre dans le service Gestion des API, vous pouvez utiliser votre propre Traffic Manager avec des règles de routage personnalisées.

1. Créez votre propre [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Si vous utilisez un domaine personnalisé, [utilisez-le avec Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) plutôt qu’avec le service Gestion des API.
1. [Configurez les points de terminaison régionaux du service Gestion des API dans Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Les points de terminaison régionaux suivent le modèle d’URL `https://<service-name>-<region>-01.regional.azure-api.net`, par exemple `https://contoso-westus2-01.regional.azure-api.net`.
1. [Configurez les points de terminaison d’état régionaux du service Gestion des API dans Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Les points de terminaison d’état régionaux suivent le modèle d’URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, par exemple `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Spécifiez [la méthode de routage](../traffic-manager/traffic-manager-routing-methods.md) de Traffic Manager.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
