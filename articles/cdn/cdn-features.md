---
title: Comparer les caractéristiques du produit de réseau de distribution de contenu (CDN) Azure | Microsoft Docs
description: Apprenez-en davantage sur les fonctionnalités prises en charge par chaque produit de réseau de distribution de contenu Azure (Content Delivery Network, CDN).
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0e57ae691bf4b07b8161bc343929510d6be041a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "81260511"
---
# <a name="compare-azure-cdn-product-features"></a>Comparer les caractéristiques du produit CDN Azure

Azure Content Delivery Network (CDN) comprend quatre produits : **Azure CDN Standard fourni par Microsoft**, **Azure CDN Standard fourni par Akamai**, **Azure CDN Standard fourni par Verizon** et **Azure CDN Premium fourni par Verizon**. Pour plus d’informations sur la migration d’un profil **Azure CDN Standard fourni par Verizon** vers **Azure CDN Premium fourni par Verizon**, consultez [Migrer un profil Azure CDN Verizon Standard vers Verizon Premium](cdn-migrate.md). Notez que s’il existe bel et bien un chemin de mise à niveau de Verizon Standard vers Verizon Premium, il n’existe actuellement aucun mécanisme de conversion entre les autres produits.

Le tableau suivant répertorie les fonctionnalités disponibles avec chaque produit.

| **Fonctionnalités de performance et optimisations** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Accélération de site dynamique](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Offerte par le biais d’[Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accélération de site dynamique - Compression d’image adaptative](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accélération de site dynamique - Récupération de l’objet](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optimisation de la livraison web générale](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** , Sélectionnez ce type d’optimisation si la taille de fichier moyenne est inférieure à 10 Mo  | **&#x2713;** |  **&#x2713;** |
| [Optimisation du streaming vidéo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via la livraison web générale | **&#x2713;**  | via la livraison web générale |  via la livraison web générale |
| [Optimisation des fichiers volumineux](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via la livraison web générale | **&#x2713;** , Sélectionnez ce type d’optimisation si la taille de fichier moyenne est supérieure à 10 Mo   | via la livraison web générale |  via la livraison web générale |
| Modifier le type d’optimisation | |**&#x2713;** | | |
| Port de l’origine |Tous les ports TCP |[Ports d’origine autorisés](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Tous les ports TCP |Tous les ports TCP |
| [Équilibrage de charge du serveur global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Purge rapide](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , Le vidage totale et le vidage de caractère générique ne sont pas pris en charge par avec Azure CDN par Akamai actuellement |**&#x2713;** |**&#x2713;** |
| [Préchargement de ressources](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Paramètres du cache/des en-têtes (à l’aide des [règles de mise en cache](cdn-caching-rules.md))  |**&#x2713;** à l’aide du [moteur de règles standard](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Moteur de distribution de contenu personnalisable et basé sur des règles |**&#x2713;** à l’aide du [moteur de règles standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** à l’aide du [moteur de règles](cdn-rules-engine.md) |
| Paramètres du cache/de l’en-tête  |**&#x2713;** à l’aide du [moteur de règles standard](cdn-standard-rules-engine.md) | | |**&#x2713;** à l’aide du [moteur de règles Premium](cdn-rules-engine.md) |
| Redirection/Réécriture d’URL |**&#x2713;** à l’aide du [moteur de règles standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** à l’aide du [moteur de règles Premium](cdn-rules-engine.md) |
| Règles pour appareils mobiles  |**&#x2713;** à l’aide du [moteur de règles standard](cdn-standard-rules-engine.md) | | |**&#x2713;** à l’aide du [moteur de règles Premium](cdn-rules-engine.md) |
| [Mise en cache des chaînes de requête](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Double pile IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Assistance HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Sécurité** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Prise en charge HTTPS avec un point de terminaison CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS sur un domaine personnalisé](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , Nécessite un CNAME direct pour l’activation |**&#x2713;** |**&#x2713;** |
| [Prise en charge du nom de domaine personnalisé](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrage géographique](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jeton d’authentification](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Protection DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Apportez votre propre certificat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Versions prises en charge de TLS | TLS 1.2, TLS 1.0/1.1 - [Configurable](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analytique et création de rapports** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Journaux de diagnostic Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Rapports principaux de Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Rapports personnalisés de Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Rapports HTTP avancés](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiques en temps réel](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Performances de nœuds Edge](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alertes en temps réel](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Simplicité d’utilisation** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Intégration facile à divers services Azure tels que [Stockage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) et [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestion via [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) ou [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Types MIME de compression](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Par défaut uniquement |Configurable |Configurable  |Configurable  |
| Encodages de compression  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






