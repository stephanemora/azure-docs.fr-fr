---
title: Test de disponibilité privé - Azure Monitor Application Insights
description: Découvrez comment utiliser des tests de disponibilité sur des serveurs internes qui s’exécutent derrière un pare-feu avec des tests privés.
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: dca75f6497567c5c6855fc1b5b12aa17a41b3dec
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103949"
---
# <a name="private-testing"></a>Tests privés

Si vous souhaitez utiliser des tests de disponibilité sur des serveurs internes qui s’exécutent derrière un pare-feu, il existe deux solutions possibles : l’activation du test ping public et les scénarios déconnectés/sans entrée.

## <a name="public-ping-test-enablement"></a>Activation du test ping public

> [!NOTE]
> Si vous ne souhaitez pas autoriser l’entrée dans votre environnement, utilisez la méthode dans la section [Scénarios déconnectés ou sans entrée](#disconnected-or-no-ingress-scenarios).

 Vérifiez que vous disposez d’un enregistrement DNS public pour votre site web interne. Le test échoue si le DNS ne peut pas être résolu. [Créez un nom de domaine personnalisé pour l’application interne.](../../cloud-services/cloud-services-custom-domain-name-portal.md#add-an-a-record-for-your-custom-domain)

Configurez votre pare-feu pour autoriser les demandes entrantes de notre service.

- Les [balises de service](../../virtual-network/service-tags-overview.md) sont un moyen simple d’activer les services Azure sans avoir à autoriser des adresses IP individuelles ou à maintenir une liste à jour. Les balises de service peuvent être utilisées sur le pare-feu Azure et les groupes de sécurité réseau pour permettre l’accès au service. **ApplicationInsightsAvailability** est la balise de service dédiée à notre service de test ping.
    1. Si vous utilisez des [groupes de sécurité réseau Azure](../../virtual-network/network-security-groups-overview.md), accédez à votre ressource de groupe de sécurité réseau et sélectionnez **Règles de sécurité de trafic entrant** sous *Paramètres*, puis sélectionnez **Ajouter**.

         :::image type="content" source="media/availability-private-test/add.png" alt-text="Capture d’écran de l’onglet Règles de sécurité de trafic entrant dans la ressource Groupe de sécurité réseau.":::

    1. Ensuite, sélectionnez *Balise de service* comme source et *ApplicationInsightsAvailability* comme balise de service source. Utilisez les ports ouverts 80 (http) et 443 (https) pour le trafic entrant à partir de l’étiquette de service.

        :::image type="content" source="media/availability-private-test/service-tag.png" alt-text="Capture d’écran de l’onglet Ajouter des règles de sécurité de trafic entrant avec une source Balise de service.":::

- Si vos points de terminaison sont hébergés en dehors d’Azure ou si les balises de service ne sont pas disponibles pour votre scénario, vous devez autoriser individuellement les [adresses IP de nos agents de test web](ip-addresses.md). Vous pouvez interroger les plages d’adresses IP directement à partir de PowerShell, Azure CLI ou d’un appel REST à l’aide de l’[API de balise de service](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). Vous pouvez également télécharger un [fichier JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) pour obtenir la liste des balises de service actuelles avec les détails des adresses IP.
    1. Dans votre ressource de groupe de sécurité réseau, sélectionnez **Règles de sécurité de trafic entrant** sous *Paramètres*, puis **Ajouter**.
    1. Ensuite, sélectionnez *Adresses IP* comme source, puis ajoutez vos adresses IP dans une liste séparée par des virgules dans les plages d’adresses IP source/CIDR.

         :::image type="content" source="media/availability-private-test/ip-addresses.png" alt-text="Capture d’écran de l’onglet Ajouter des règles de sécurité de trafic entrant avec une source Adresses IP.":::

## <a name="disconnected-or-no-ingress-scenarios"></a>Scénarios déconnectés ou sans entrée

Votre serveur de test doit avoir un accès sortant au point de terminaison d’ingestion Application Insights, ce risque de sécurité étant beaucoup plus faible que l’alternative, qui consiste à autoriser les demandes entrantes. Les résultats apparaîtront dans l’onglet des tests web de disponibilité avec une expérience simplifiée de ce qui est disponible pour le test créé via le portail Azure. Les tests de disponibilité personnalisés s’affichent également comme résultats de disponibilité dans Analytics, Search et Metrics.

1. Connectez votre ressource Application Insights et votre environnement déconnecté à l’aide d’[Azure Private Link](../logs/private-link-security.md)
1. Écrivez du code personnalisé pour tester régulièrement votre serveur ou vos points de terminaison internes. Vous pouvez exécuter le code à l’aide d’[Azure Functions](availability-azure-functions.md) ou d’un processus en arrière-plan sur un serveur de test situé derrière votre pare-feu. Votre processus de test peut envoyer ses résultats à Application Insights à l’aide de l’API de `TrackAvailability()` du package SDK principal.

## <a name="troubleshooting"></a>Dépannage

Consultez l’[article dédié au dépannage](troubleshoot-availability.md).

## <a name="next-steps"></a>Étapes suivantes

* [Azure Private Link](../logs/private-link-security.md)
* [Availability alerts](availability-alerts.md) (Alertes de disponibilité)
* [Tests des URL](monitor-web-app-availability.md)
* [Créer et exécuter des tests de disponibilité personnalisés avec Azure Functions](availability-azure-functions.md)