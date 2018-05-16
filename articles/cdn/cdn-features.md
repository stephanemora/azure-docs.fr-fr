---
title: Fonctionnalités du produit de réseau de distribution de contenu (CDN) Azure | Microsoft Docs
description: Apprenez-en davantage sur les fonctionnalités prises en charge par chaque produit de réseau de distribution de contenu Azure (Content Delivery Network, CDN).
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/25/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: ae57d4e9feb23f67bb4f05148d87fec96f055a7f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cdn-product-features"></a>Caractéristiques de produit Azure CDN

Azure Content Delivery Network (CDN) englobe quatre produits : **CDN Azure Standard fourni par Microsoft**, **CDN Azure Standard fourni par Akamai**, **CDN Azure Standard fourni par Verizon** et **CDN Azure Premium fourni par Verizon**. Le tableau suivant répertorie les fonctionnalités disponibles avec chaque produit.

| **Fonctionnalités de performance et optimisations** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Accélération de site dynamique](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accélération de site dynamique - Compression d’image adaptative](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accélération de site dynamique - Récupération de l’objet](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optimisation du streaming vidéo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Optimisation des fichiers volumineux](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Équilibrage de charge du serveur global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Purge rapide](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Préchargement de ressources](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Paramètres du cache/des en-têtes (à l’aide des [règles de mise en cache](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Paramètres du cache/des en-têtes (à l’aide du [moteur de règles](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Mise en cache des chaînes de requête](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Mise en cache régionale  |**&#x2713;** |  |  |  |
| Double pile IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Assistance HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Sécurité** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Prise en charge HTTPS avec un point de terminaison CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS sur un domaine personnalisé](cdn-custom-ssl.md)  | **&#x2713;** | |**&#x2713;** |**&#x2713;** |
| [Prise en charge du nom de domaine personnalisé](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrage géographique](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jeton d’authentification](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Protection DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Apportez votre propre certificat](cdn-custom-ssl.md#option-2-enable-the-https-feature-with-your-own-certificate) |**&#x2713;** |  |  |  |
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
| Intégration simple des services Azure, notamment [Stockage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md) et [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestion via [REST API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) ou [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Moteur de distribution de contenu personnalisable et basé sur des règles](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| Redirection/réécriture d’URL (à l’aide du [moteur de règles](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Règles d’appareil mobile (à l’aide du [moteur de règles](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\* Microsoft et Verizon prennent en charge l’envoi direct de fichiers et de médias volumineux par le biais de l’optimisation de la distribution web générale.



