---
title: Déployer des services Gestion des API Azure sur plusieurs régions Azure | Microsoft Docs
description: Découvrez comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058037"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure

La Gestion des API Azure prend en charge le déploiement sur plusieurs régions, ce qui permet aux éditeurs d’API de ne distribuer qu’un seul service de Gestion des API Azure sur un nombre de régions Azure au choix. Ceci permet de réduire la latence de la demande telle qu’elle est perçue par les utilisateurs distribués de l’API. La disponibilité du service est également améliorée si une région est mise hors connexion.

Un nouveau service de Gestion des API Azure contient initialement une seule [unité][unit] dans une seule région Azure, la Région primaire. D’autres régions peuvent être facilement ajoutées via le portail Azure. Un serveur de passerelle de gestion des API est déployé dans chaque région et le trafic d’appel est routé vers la passerelle la plus proche en termes de latence. Si une région est déconnectée, le trafic est automatiquement redirigé vers la passerelle suivante la plus proche.

> [!NOTE]
> La Gestion des API Azure réplique uniquement le composant de la passerelle API dans les différentes régions. Le composant du service de gestion est hébergé uniquement dans la région primaire. En cas de panne dans la région primaire, il est impossible d’appliquer les modifications de configuration sur une instance de service de Gestion des API Azure, y compris pour les paramètres et les mises à jour de stratégies.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"></a>Déploiement d’une instance de service Gestion des API sur une nouvelle région

> [!NOTE]
> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][Create an API Management service instance].

Dans le portail Azure, accédez à la page **Mise à l’échelle et tarification** de votre instance de service de Gestion des API. 

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour procéder au déploiement vers une nouvelle région, cliquez sur **+ Ajouter une région** dans la barre d’outils.

![Ajouter une région][api-management-add-region]

Sélectionnez l’emplacement dans la liste déroulante et définissez le nombre d’unités à l’aide du curseur.

![Spécifier les unités][api-management-select-location-units]

Cliquez sur **Ajouter** pour placer votre sélection dans la table des emplacements. 

Répétez cette procédure jusqu’à ce que vous ayez configuré tous les emplacements, puis cliquez sur **Enregistrer** dans la barre d’outils pour démarrer le déploiement.

## <a name="remove-region"></a>Suppression d’une instance de service Gestion des API dans un emplacement

Dans le portail Azure, accédez à la page **Mise à l’échelle et tarification** de votre instance de service de Gestion des API. 

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour l’emplacement que vous souhaitez supprimer, ouvrez le menu contextuel à l’aide du bouton **...** situé à l’extrême droite du tableau. Sélectionnez l’option **Supprimer**.

Confirmez la suppression, puis cliquez sur **Enregistrer** pour appliquer les modifications.

## <a name="route-backend"> </a>Acheminez les appels d’API à des services principaux régionaux

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
> Vous pouvez également des serveurs frontaux vos services back-end avec [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), diriger les appels d’API à Traffic Manager et il permettent de résoudre le routage automatiquement.

## <a name="custom-routing"> </a>Utiliser le routage personnalisé à des passerelles régionales de gestion des API

Gestion des API achemine les demandes vers un régionales *passerelle* selon [la plus faible latence](../traffic-manager/traffic-manager-routing-methods.md#performance). Bien qu’il n’est pas possible de remplacer ce paramètre dans la gestion des API, vous pouvez utiliser votre propre Traffic Manager avec des règles de routage personnalisés.

1. Créer votre propre [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Si vous utilisez un domaine personnalisé, [utilisez-le avec Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) plutôt qu’au service de gestion des API.
1. [Configurer les points de terminaison régionales de gestion des API dans Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Les points de terminaison régionales suivent le modèle de l’URL de `https://<service-name>-<region>-01.regional.azure-api.net`, par exemple `https://contoso-westus2-01.regional.azure-api.net`.
1. [Configurer les points de terminaison régional état gestion des API dans Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Les points de terminaison régional d’état suivent le modèle de l’URL de `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, par exemple `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Spécifiez [la méthode de routage](../traffic-manager/traffic-manager-routing-methods.md) de Traffic Manager.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
