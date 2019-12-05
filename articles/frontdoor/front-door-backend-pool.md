---
title: Serveurs principaux et pools principaux dans Azure Front Door Service | Microsoft Docs
description: Cet article décrit les serveurs principaux et pools principaux dans la configuration de Front Door.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: b764799d3f40cef24a0412ac950026af650d4ec7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229028"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Backends et pools de backends dans Azure Front Door Service
Cet article décrit des concepts sur la façon de mapper votre déploiement d’application en utilisant Azure Front Door Service. Il explique également les différents termes utilisés pour la configuration de Front Door relatifs aux serveurs principaux d’applications.

## <a name="backends"></a>Serveurs principaux
Un serveur principal correspond à l’instance de déploiement d’une application dans une zone géographique, ou « région ». Le service Front Door prend en charge les serveurs principaux Azure et non Azure. Sa région n’est donc pas limitée aux régions Azure. De plus, elle peut être votre centre de données local ou une instance d’application dans un autre cloud.

Les serveurs principaux du service Front Door font référence au nom d’hôte ou à l’adresse IP publique de votre application, qui peut traiter les demandes des clients. Attention, il ne faut pas confondre les serveurs principaux avec votre niveau de base de données, de stockage, etc. Les serveurs principaux doivent être considérés comme le point de terminaison public de votre application principale. Lorsque vous ajoutez un serveur principal dans un pool principal Front Door, vous devez également ajouter les éléments suivants :

- **Type d’hôte principal**. Le type de ressource que vous souhaitez ajouter. Le service Front Door prend en charge la détection automatique de vos serveurs principaux d’application s’ils sont issus d’App Service, du service cloud ou de Stockage. Si vous voulez une autre ressource Azure, voire un serveur principal non Azure, sélectionnez **Hôte personnalisé**.

    >[!IMPORTANT]
    >Pendant la configuration, les API ne valident pas si le serveur principal est inaccessible depuis les environnements Front Door. Assurez-vous que Front Door peut atteindre votre serveur principal.

- **Abonnement et nom d’hôte de serveur principal**. Si vous n’avez pas sélectionné **Hôte personnalisé** pour le type d’hôte de serveur principal, sélectionnez votre serveur principal en choisissant l’abonnement approprié et le nom d’hôte de serveur principal correspondant dans l’interface utilisateur.

- **En-tête de l’hôte de serveur principal**. La valeur d’en-tête de l’hôte envoyée au serveur principal pour chaque requête. Pour plus d’informations, veuillez consulter [En-tête d’hôte de serveur principal](#hostheader).

- **Priorité**. Attribuez des priorités à vos serveurs principaux lorsque vous souhaitez utiliser un serveur principal de service principal pour tout le trafic. Fournissez également des sauvegardes si le principal ou les serveurs principaux de sauvegarde ne sont pas disponibles. Pour plus d'informations, veuillez consulter [Priorité](front-door-routing-methods.md#priority).

- **Poids**. Attribuez des poids à vos différents serveurs principaux si vous voulez répartir le trafic entre un ensemble de serveurs principaux, que ce soit de façon équitable ou selon des coefficients de pondération. Pour plus d’informations, veuillez consulter [Poids](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>En-tête de l’hôte backend

Les demandes transférées par Front Door à un serveur principal incluent un champ En-tête de l’hôte dont se sert le serveur principal pour récupérer la ressource cible. La valeur de ce champ provient généralement de l’URI du backend et indique l’hôte et le port.

Par exemple, une requête effectuée pour www\.contoso.com aura l’en-tête d’hôte www\.contoso.com. Si vous utilisez le portail Microsoft Azure pour configurer votre serveur principal, la valeur par défaut pour ce champ sera le nom d’hôte du serveur principal. Si votre serveur principal est contoso-westus.azurewebsites.net, dans le portail Microsoft Azure, la valeur remplie automatiquement pour l’en-tête d’hôte de serveur principal sera contoso-westus.azurewebsites.net. Toutefois, si vous utilisez des modèles Azure Resource Manager ou une autre méthode sans définir explicitement ce champ, Front Door transmettra le nom d’hôte entrant comme valeur pour l’en-tête d’hôte. Si la requête a été faite pour www\.contoso.com et que votre serveur principal est contoso-westus.azurewebsites.net, dont le champ d’en-tête est vide, alors le service Front Door définira l’en-tête de l’hôte en tant que www\.contoso.com.

La plupart des serveurs principaux d’application (comme Azure Web Apps, Azure Stockage Blob et Azure Cloud Services) exigent une correspondance entre l’en-tête de l’hôte et le domaine du serveur principal. Cependant, l’hôte frontend qui achemine vers votre serveur principal aura un nom d’hôte différent, comme www\.contoso.azurefd.net.

Si votre serveur principal requiert une correspondance entre l’en-tête de l’hôte et le nom de l’hôte du serveur principal, veuillez vous assurer que l’en-tête de l’hôte principal inclut le serveur principal du nom de l’hôte.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configuration du champ En-tête de l’hôte backend pour le backend

Pour configurer le champ **En-tête de l’hôte de serveur principal** pour un serveur principal dans la section Pool principal :

1. Ouvrez votre ressource Front Door et sélectionnez le pool principal contenant le serveur principal à configurer.

2. Si vous ne l’avez pas déjà fait, ajoutez un serveur principal, ou modifiez-en un.

3. Définissez le champ d’en-tête de l’hôte principal sur une valeur personnalisée ou laissez-le vide. Le nom d’hôte pour la requête entrante sera utilisé en tant que valeur d’en-tête hôte.

## <a name="backend-pools"></a>Pools principaux
Dans Front Door Service, un pool principal désigne l’ensemble des serveurs principaux qui reçoivent un trafic similaire pour leur application. En d’autres termes, il s’agit d’un regroupement logique des instances de votre application dans le monde entier, qui reçoivent le même trafic et répondent avec un comportement attendu. Ces serveurs principaux sont généralement déployés dans différentes régions ou au sein d’une même région. Tous les serveurs principaux peuvent être en mode de déploiement actif/actif (ou ce qui est défini en tant que configuration active/passive).

Un pool principal définit comment différents serveurs principaux doivent être évalués par le biais de sondes d’intégrité. Il définit également comment l’équilibrage de charge entre eux se produit.

### <a name="health-probes"></a>Sondes d’intégrité
Le Front Door Service envoie des requêtes de sonde HTTP/HTTPS périodiques à chacun de vos serveurs principaux configuré. Les requêtes de sondes déterminent la proximité et l’intégrité de chaque serveur principal, afin d’équilibrer la charge des requêtes de vos utilisateurs finaux. Les paramètres de sonde d’intégrité d’un pool principal définissent la façon dont nous sondons l’étant d’intégrité des serveurs principaux de l’application. Les paramètres de configuration de l’équilibrage de charge disponibles sont les suivants :

- **Chemin d’accès**. L’URL utilisée pour les requêtes de sondage pour tous les serveurs principaux du pool principal. Par exemple, si un de vos serveurs principaux est contoso-westus.azurewebsites.net et que le chemin d’accès est défini sur /probe/test.aspx, alors les environnements Service Front Door, en supposant que le protocole est défini sur HTTP, envoient les requêtes de sondes d’intégrité au chemin d’accès http\://contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocole**. Détermine si les requêtes de sondes d’intégrité de Service Front Door vers vos serveurs principaux sont envoyées via le protocole HTTP ou HTTPS.

- **Intervalle (en secondes)** . Définit la fréquence des sondes d’intégrité de vos serveurs principaux, ou les intervalles dans lequel chacun des environnements Front Door envoie une sonde.

    >[!NOTE]
    >Pour accélérer les basculements, baissez la valeur de l’intervalle. Plus la valeur est faible, plus le nombre de sondes d’intégrité de vos serveurs principaux augmente. Par exemple, si l’intervalle est défini sur 30 secondes avec 90 environnements ou points de présence (POP) Front Door dans le monde entier, chaque serveur principal reçoit sur entre 3 et 5 requêtes d’intégrité par seconde.

Pour plus d’informations, veuillez consulter la section [Sondes d’intégrité](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Paramètres d’équilibrage de charge
Les paramètres d’équilibrage de charge du pool principal définissent la façon dont nous évaluons les sondes d’intégrité. Ces paramètres déterminent si le serveur principal est sain ou pas. Ils vérifient également comment équilibrer la charge du trafic entre différents serveurs principaux dans le pool principal. Les paramètres de configuration de l’équilibrage de charge disponibles sont les suivants :

- **Taille d’échantillon** : Cette propriété identifie le nombre d’échantillons de sondes d’intégrité à prendre en compte pour évaluer l’intégrité d’un serveur principal.

- **Taille d’échantillon réussi**. Définit la taille d’échantillon comme mentionnée précédemment, le nombre d’échantillons réussis nécessaires pour appeler le serveur principal sain. Par exemple, imaginons le cas d’un intervalle de sonde d’intégrité Front Door de 30 secondes, dont la taille d’échantillon est de 5, et la taille d’échantillon réussi de 3. Chaque fois que nous évaluons les sondes d’intégrité pour votre serveur principal, nous examinons les 5 derniers échantillons pendant 150 secondes (5 x 30). Au moins 3 sondages réussis sont requis pour déclarer que le serveur principal est sain.

- **Sensibilité de latence (latence supplémentaire)** . Définit si vous souhaitez que Front Door envoie la requête aux serveurs principaux dans la plage de sensibilité de mesure de latence ou transfère la requête vers le serveur principal le plus proche.

Pour plus d’informations, consultez [Routage du trafic basé sur les latences les plus faibles](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un profil Front Door](quickstart-create-front-door.md)
- [Fonctionnement de Front Door](front-door-routing-architecture.md)