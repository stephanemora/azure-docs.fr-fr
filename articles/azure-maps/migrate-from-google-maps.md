---
title: 'Tutoriel : Migrer de Google Maps vers Azure Maps | Microsoft Azure Maps'
description: Tutoriel sur la migration de Google Maps vers Microsoft Azure Maps. Vous aide à passer aux API et kits SDK Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0e841b1f386d45ddb4af8598855d8e739750307e
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910740"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrer de Google Maps vers Azure Maps

Ce didacticiel fournit des insights sur la migration d’applications web, mobiles et basées sur serveur de Google Maps vers la plateforme Microsoft Azure Maps. Ce didacticiel comprend des exemples de code comparatifs, des suggestions de migration et des meilleures pratiques pour la migration vers Azure Maps.

## <a name="azure-maps-platform-overview"></a>Vue d’ensemble de la plateforme Azure Maps

Azure Maps offre aux développeurs de tous les secteurs d’activité de puissantes fonctionnalités géospatiales comprenant les données cartographiques régulièrement mises à jour pour fournir un contexte géographique aux applications mobiles et web. Azure Maps est un jeu d’API REST conforme à l’API Azure One pour les cartes, la recherche, le routage, le trafic, les fuseaux horaires, la géolocalisation, le geofencing, les données cartographiques, la météo, la mobilité et les opérations spatiales. Il est accompagné de kits SDK web et Android pour rendre le développement facile, flexible et portable sur de nombreuses plateformes.

## <a name="high-level-platform-comparison"></a>Comparaison générale de plateformes

Le tableau suivant fournit une liste générale des fonctionnalités de Google Maps et la prise en charge relative de ces fonctionnalités dans Azure Maps. Cette liste n’inclut pas d’autres fonctionnalités Azure Maps telles que l’accessibilité, les API de geofencing, les isochrones, les opérations spatiales, l’accès direct aux mosaïques, les services Batch et les comparaisons de couverture de données (par exemple, la couverture d’image).

| Fonctionnalité Google Maps         | Support Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Kit de développement logiciel (SDK) web                     | ✓                                      |
| Kit de développement logiciel Android                 | ✓                                      |
| Kit de développement logiciel (SDK) iOS                     | Prévu                                |
| API de service REST           | ✓                                      |
| Directions (routage)        | ✓                                      |
| Matrice des distances             | ✓                                      |
| Elevation                   | Prévu                                |
| Géocodage (transfert/inversé) | ✓                                      |
| Géolocalisation                 | N/A                                    |
| Recherche de lieux               | ✓                                      |
| Détails des lieux              | N/A : site web et numéro de téléphone disponibles |
| Photos des lieux               | N/A                                    |
| Autocomplétion des lieux          | ✓                                      |
| Cartes statiques                 | ✓                                      |
| Affichage des rues statiques          | N/A                                    |
| Time Zone (Fuseau horaire)                   | ✓                                      |
| API Incorporation de cartes           | N/A                                    |
| URL de cartes                    | N/A                                    |

Google Maps fournit une authentification basée sur les clés de base. Azure Maps fournit une authentification basée sur les clés de base, ainsi qu’une authentification Azure Active Directory hautement sécurisée.

## <a name="licensing-considerations"></a>Considérations relatives aux licences

Lors de la migration vers Azure Maps à partir de Google Maps, les points suivants doivent être pris en compte en ce qui concerne les licences.

- Azure Maps facture l’utilisation de cartes interactives en fonction du nombre de mosaïques chargées, tandis que Google Maps facture le chargement du contrôle de carte. Dans les kits de développement logiciel (SDK) Azure Maps interactifs, les mosaïques sont automatiquement mises en cache pour réduire le coût du développeur. Une transaction Azure Maps est générée toutes les 15 mosaïques qui sont chargées. Les kits de développement logiciel (SDK) Azure Maps interactifs utilisent des mosaïques de 512 pixels et génèrent en moyenne une transaction ou moins par vue de page.
- Il est souvent beaucoup plus économique de remplacer les images de carte statiques des services web Google Maps par le kit de développement logiciel (SDK) web Azure Maps, car il utilise des mosaïques et, à moins que l’utilisateur n’effectue un panoramique et un zoom sur la carte, elles génèrent souvent uniquement une fraction de transaction par chargement de carte. Le kit de développement logiciel (SDK) web Azure Maps dispose d’options pour désactiver le panoramique et le zoom. En outre, le kit de développement logiciel (SDK) web Azure Maps offre beaucoup plus d’options de visualisation qu’un service web de cartes statiques.
- Azure Maps permet de stocker les données de sa plateforme dans Azure. Elles peuvent également être mises en cache ailleurs pendant six mois au maximum, en fonction des [conditions d’utilisation](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Voici quelques ressources associées pour Azure Maps :

- [Page des tarifs Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Conditions d’utilisation Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluses dans les conditions d’utilisation de Microsoft Online Services)
- [Choix du bon niveau tarifaire dans Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Plan de migration suggéré

Voici un plan de migration général.

1. Effectuez l’inventaire des services et des kits de développement logiciel (SDK) Google Maps que votre application utilise et vérifiez qu’Azure Maps fournit d’autres kits de développement logiciel et services vers lesquels migrer.
2. Créez un abonnement Azure (si vous n’en avez pas encore) à l’adresse [https://azure.com](https://azure.com).
3. Créez un compte Azure Maps ([documentation](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) et une clé d’authentification ou Azure Active Directory ([documentation](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migrez votre code d’application.
5. Testez votre application migrée.
6. Déployez votre application migrée en production.

## <a name="azure-maps-technical-resources"></a>Ressources techniques Azure Maps

Voici une liste de ressources techniques utiles pour Azure Maps.

- Vue d’ensemble : [https://azure.com/maps](https://azure.com/maps)
- Documentation : [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Exemples de code de kit de développement logiciel web : [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Forums des développeurs : [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Vidéos : [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog : [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Commentaires Azure Maps (UserVoice) : [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Prise en charge de la migration

Les développeurs peuvent rechercher la prise en charge de la migration via les [forums](https://aka.ms/AzureMapsForums) ou via l’une des nombreuses options de support Azure : [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment migrer votre application Google Maps avec les articles suivants :

> [!div class="nextstepaction"]
> [Migrer une application web](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrer une application Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrer un service web](migrate-from-google-maps-web-services.md)
