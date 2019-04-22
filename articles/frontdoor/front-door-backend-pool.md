---
title: Pools de serveurs principaux et le serveur principal dans le Service de porte d’entrée Azure | Microsoft Docs
description: Cet article décrit les pools de serveurs principaux et du serveur principal sont devant la porte de la configuration.
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
ms.openlocfilehash: 2372f49c7280ee5c817f3d2f98cc80a196dae5f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879197"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Backends et pools de backends dans Azure Front Door Service
Cet article décrit les concepts sur la façon de mapper votre déploiement d’application avec le Service Azure porte d’entrée. Il explique également les différents termes de configuration porte autour des backends d’applications.

## <a name="backends"></a>Serveurs principaux
Un serveur principal est égal à l’instance de déploiement d’une application dans une région. Service de la porte d’entrée prend en charge Azure et les serveurs principaux non Azure, la région n’est pas limitée aux régions Azure. En outre, il peut être votre centre de données sur site ou une instance d’application dans un autre cloud.

Les serveurs principaux de Service de la porte d’entrée font référence au nom d’hôte ou adresse IP publique de votre application, qui peut traiter les demandes des clients. Les serveurs principaux ne doit pas être confondues avec votre niveau de base de données, niveau de stockage et ainsi de suite. Les serveurs principaux doivent être considérés comme le point de terminaison public de votre application principale. Lorsque vous ajoutez un serveur principal dans un pool principal de porte d’entrée, vous devez également ajouter les éléments suivants :

- **Type d’hôte principal**. Le type de ressource que vous souhaitez ajouter. Service de la porte d’entrée prend en charge la découverte automatique de vos serveurs principaux d’applications à partir d’app service, service cloud ou stockage. Si vous souhaitez une autre ressource dans Azure ou même un serveur principal non Azure, sélectionnez **hôte personnalisé**.

    >[!IMPORTANT]
    >Lors de la configuration, les API ne valident si le serveur principal n’est pas accessible à partir d’environnements de porte d’entrée. Assurez-vous que porte d’entrée peut atteindre votre serveur principal.

- **Nom d’hôte abonnement et le serveur principal**. Si vous n’avez pas sélectionné **hôte personnalisé** pour le type d’hôte de serveur principal, sélectionnez votre serveur principal en choisissant l’abonnement approprié et le nom d’hôte de serveur principal correspondant dans l’interface utilisateur.

- **En-tête de l’hôte principal**. La valeur d’en-tête d’hôte envoyée au serveur principal pour chaque demande. Pour plus d’informations, consultez [en-tête d’hôte principal](#hostheader).

- **Priorité**. Attribuez des priorités à vos serveurs principaux différents lorsque vous souhaitez utiliser un serveur principal de service principal pour tout le trafic. En outre, fournir des sauvegardes si le réplica principal ou les serveurs principaux de sauvegarde ne sont pas disponibles. Pour plus d’informations, consultez [priorité](front-door-routing-methods.md#priority).

- **Poids**. Affecter des poids à vos serveurs principaux différents pour répartir le trafic sur un ensemble de serveurs principaux, uniformément ou en fonction des coefficients de pondération. Pour plus d’informations, consultez [poids](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>En-tête de l’hôte backend

Les demandes transférées par la porte d’entrée vers un serveur principal incluent un champ d’en-tête hôte que le serveur principal utilise pour récupérer la ressource cible. La valeur de ce champ provient généralement de l’URI du backend et indique l’hôte et le port.

Par exemple, une demande effectuée pour www\.contoso.com aura l’en-tête d’hôte www\.contoso.com. Si vous utilisez le portail Azure pour configurer votre serveur principal, la valeur par défaut pour ce champ est le nom d’hôte du serveur principal. Si votre serveur principal est contoso-westus.azurewebsites.net, dans le portail Azure, la valeur de rempli automatiquement pour le serveur principal de l’en-tête d’hôte sera westus.azurewebsites.net de contoso. Toutefois, si vous utilisez des modèles Azure Resource Manager ou une autre méthode sans définir explicitement ce champ, porte Service envoie le nom d’hôte entrant comme valeur pour l’en-tête d’hôte. Si la demande a été faite pour www\.contoso.com et votre serveur principal est contoso-westus.azurewebsites.net qui a un champ d’en-tête vide, Service de la porte d’entrée sera l’en-tête d’hôte en tant que www\.contoso.com.

La plupart des backends d’applications (Azure Web Apps, stockage d’objets Blob et Services Cloud) nécessitent l’en-tête d’hôte pour correspondre au domaine du serveur principal. Cependant, l’hôte de serveur frontal achemine vers votre serveur principal utilise un nom d’hôte différent, tel que www\.contoso.azurefd.net.

Si votre serveur principal nécessite l’en-tête d’hôte doit correspondre au nom d’hôte de serveur principal, assurez-vous que l’en-tête d’hôte principal inclut le backend de nom d’hôte.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configuration du champ En-tête de l’hôte backend pour le backend

Pour configurer le **en-tête d’hôte principal** field pour un serveur principal dans la section de pool principal :

1. Ouvrez votre ressource porte d’entrée et sélectionnez le pool principal avec le serveur principal à configurer.

2. Ajouter un serveur principal si vous n’avez pas fait, ou modifiez un.

3. Définir des champ d’en-tête de l’hôte principal sur une valeur personnalisée ou la laisser vide. Le nom d’hôte pour la demande entrante sera utilisé en tant que valeur d’en-tête hôte.

## <a name="backend-pools"></a>Pools principaux
Un pool principal dans Front porte Service désigne l’ensemble des serveurs principaux qui reçoivent le trafic similaire pour son application. En d’autres termes, il est un regroupement logique de vos instances de l’application dans le monde entier qui reçoivent le même trafic et de répondre avec le comportement attendu. Ces serveurs principaux sont déployés dans différentes régions ou dans la même région. Tous les serveurs principaux peuvent être en mode de déploiement actif/actif ou ce qui est défini comme actif/passif.

Un pool principal définit comment les serveurs principaux différents doivent être évalués par le biais de sondes d’intégrité. Il définit également comment l’équilibrage de charge entre eux se produit.

### <a name="health-probes"></a>Sondes d’intégrité
Porte d’entrée Service envoie des demandes de sonde HTTP/HTTPS périodiques à chacun de vos serveurs principaux configuré. Demandes d’analyse de déterminer la proximité et l’intégrité de chaque service principal pour charger équilibrent vos demandes de l’utilisateur final. Paramètres de sonde d’intégrité pour un pool principal définissent comment nous interroger l’état d’intégrité des serveurs principaux d’application. Les paramètres suivants sont disponibles pour l’équilibrage de charge de configuration :

- **Chemin d’accès**. L’URL utilisée pour les demandes d’analyse pour tous les serveurs principaux dans le pool principal. Par exemple, si un de vos serveurs principaux est contoso-westus.azurewebsites.net et le chemin d’accès est défini sur /probe/test.aspx, puis des environnements de Service de la porte d’entrée, en supposant que le protocole est défini sur HTTP, envoie les demandes de sonde d’intégrité HTTP\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocole**. Définit s’il faut envoyer les demandes de sonde d’intégrité du Service de la porte d’entrée vers vos serveurs principaux avec le protocole HTTP ou HTTPS.

- **Intervalle (secondes)**. Définit la fréquence des sondes d’intégrité à vos serveurs principaux, ou les intervalles dans laquelle chacun des environnements porte d’entrée envoie une sonde.

    >[!NOTE]
    >Pour accélérer les basculements, définissez l’intervalle sur une valeur inférieure. Faible valeur, plus le volume de la sonde d’intégrité de vos serveurs principaux réception. Par exemple, si l’intervalle est défini sur 30 secondes avec 90 des environnements de porte d’entrée ou les points de présence dans le monde entier, chaque service principal reçoit sur les demandes d’analyse de 3 à 5 par seconde.

Pour plus d’informations, consultez [sondes d’intégrité](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Paramètres de l’équilibrage de charge
Paramètres de l’équilibrage de charge pour le pool principal définissent comment nous évaluons les sondes d’intégrité. Ces paramètres déterminent si le serveur principal est intègre ou pas. Ils vérifient également comment équilibrer la charge du trafic entre différents serveurs principaux dans le pool principal. Les paramètres suivants sont disponibles pour l’équilibrage de charge de configuration :

- **Taille de l’échantillon**. Identifie le nombre d’échantillons de sondes d’intégrité que nous devons prendre en compte pour l’évaluation d’intégrité de serveur principal.

- **Taille d’échantillon réussie**. Définit la taille d’échantillon comme mentionnée précédemment, le nombre d’échantillons réussies nécessaires pour appeler le serveur principal sain. Par exemple, supposons un intervalle de sonde d’intégrité porte d’entrée est de 30 secondes, la taille de l’échantillon est de 5 secondes et la taille d’échantillon réussie est de 3 secondes. Chaque fois que nous évaluer l’intégrité des sondes pour votre serveur principal, nous allons examiner les cinq derniers échantillons de plus de 150 secondes (5 x 30). Au moins trois sondes réussi sont requis pour déclarer le serveur principal comme étant saine.

- **Sensibilité de latence (latence supplémentaire)**. Définit si vous souhaitez que la porte d’entrée pour envoyer la demande aux serveurs principaux dans la plage de sensibilité de mesure de latence ou de transférer la demande vers le serveur principal le plus proche.

Pour plus d’informations, consultez [latence la plus faible en fonction de la méthode de routage](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un profil de la porte d’entrée](quickstart-create-front-door.md)
- [Fonctionnement de la porte d’entrée](front-door-routing-architecture.md)