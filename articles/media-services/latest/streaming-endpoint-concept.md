---
title: Points de terminaison de streaming dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les points de terminaison de streaming et comment ils sont utilisés par Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 8b6deadca610916a10f719d715fe6a17e29148bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125421"
---
# <a name="streaming-endpoints"></a>Points de terminaison de diffusion en continu

Dans Microsoft Azure Media Services (AMS), l’entité [Points de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) représente un service de streaming qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN) pour être redistribué. Le flux sortant d’un service de **point de terminaison de streaming** peut être un flux temps réel ou un actif multimédia de vidéo à la demande dans votre compte Media Services. Quand vous créez un compte Media Services, un point de terminaison de streaming **par défaut** est créé pour vous dans l’état Arrêté. Vous ne pouvez pas supprimer le point de terminaison de streaming **par défaut**. Vous pouvez créer d’autres points de terminaison de streaming sous votre compte. 

> [!NOTE]
> Pour démarrer le streaming de vidéos, vous devez démarrer le **point de terminaison de streaming** à partir duquel vous souhaitez diffuser la vidéo. 
>  
> Vous êtes facturé uniquement lorsque votre point de terminaison de streaming est en cours d’exécution.

## <a name="naming-convention"></a>Conventions d’affectation de noms

Pour le point de terminaison par défaut : `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

Pour les points de terminaison supplémentaires : `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Types  

Il existe deux types de **points de terminaison de streaming** : **Standard** et **Premium**. Le type est défini par le nombre d’unités d’échelle (`scaleUnits`) que vous allouez pour le point de terminaison de streaming. 

Le tableau décrit les types :  

|Type|Unités d’échelle|Description|
|--------|--------|--------|  
|**Point de terminaison de streaming Standard** (recommandé)|0|La valeur par défaut est le point de terminaison de diffusion en continu un **Standard** type, mais peut être remplacé par le type Premium.<br/> Le type Standard est l’option recommandée pour pratiquement tous les scénarios de diffusion en continu et la taille du public. Le type **Standard** met automatiquement à l’échelle la bande passante sortante. Le débit à partir de ce type de point de terminaison de diffusion en continu est jusqu'à 600 Mbits/s. Les fragments vidéo mis en cache dans le CDN, n’utilisez pas la bande passante de point de terminaison de diffusion en continu.<br/>Pour les clients avec des exigences extrêmement hautes, Media Services offre des points de terminaison de streaming **Premium**, qui peuvent être utilisés à des fins de scale out de la capacité pour les audiences Internet les plus étendues. Si vous pensez que le grand public et des utilisateurs simultanés, contactez-nous à amsstreaming\@microsoft.com pour obtenir des conseils sur la nécessité de déplacer vers le **Premium** type. |
|**Point de terminaison de streaming Premium**|>0|Les points de terminaison de streaming **Premium** sont conçus pour les charges de travail avancées et fournissent une capacité de bande passante dédiée et scalable. Vous passez à un type **Premium** en ajustant les `scaleUnits`. Les `scaleUnits` vous offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbits/s. Quand vous utilisez le type **Premium**, chaque unité activée fournit une capacité de bande passante supplémentaire à l’application. |
 
## <a name="comparing-streaming-types"></a>Comparaison des types de streaming

### <a name="features"></a>Caractéristiques

Fonctionnalité|standard|Premium
---|---|---
Gratuit les 15 premiers jours| Oui |Non 
Débit |Jusqu'à 600 Mbits/s lorsqu’Azure CDN n’est pas utilisé. Mis à l’échelle avec CDN.|200 Mbits/s par unité de streaming (SU). Mis à l’échelle avec CDN.
CDN|Azure CDN, CDN tiers ou sans CDN.|Azure CDN, CDN tiers ou sans CDN.
La facturation est calculée sur la base d'un taux| Quotidien|Quotidien
Chiffrement dynamique|Oui|Oui
l’empaquetage dynamique|Oui|Oui
Scale|Mise à l’échelle automatique vers le débit cible.|Unités de diffusion en continu supplémentaires
Hôte de filtrage/G20/personnalisé IP <sup>1</sup>|Oui|Oui
Téléchargement progressif|Oui|Oui
Utilisation recommandée |Recommandé pour la plupart des scénarios de streaming.|Utilisation professionnelle.<br/>Si vous pensez que vos besoins dépassent ce qu’offre l’abonnement Standard. Contactez-nous (amsstreaming@microsoft.com) si vous prévoyez une taille d’audience simultanée supérieure à 50 000 utilisateurs.

<sup>1</sup> uniquement utilisés directement sur le point de terminaison de diffusion en continu lorsque le CDN n’est pas activé sur le point de terminaison.

## <a name="properties"></a>properties 

Cette section fournit des détails sur certaines des propriétés de la diffusion en continu du point de terminaison. Pour obtenir des exemples montrant comment créer un nouveau point de terminaison de streaming et les descriptions de toutes les propriétés, consultez [Point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` -Permet de configurer les paramètres de sécurité suivants pour ce point de terminaison de diffusion en continu : Les clés d’authentification d’en-tête de signature Akamai et les adresses IP qui sont autorisées à se connecter à ce point de terminaison.<br />Cette propriété peut uniquement être définie lorsque `cdnEnabled` est défini sur false.
- `cdnEnabled` : Indique si l’intégration d’Azure CDN pour ce point de terminaison de diffusion en continu est activée (désactivée par défaut). Si vous définissez `cdnEnabled` sur true, les configurations suivantes sont désactivées : `customHostNames` et `accessControl`.
  
    Certains centres de données ne prennent pas en charge l’intégration d’Azure CDN. Pour vérifier si votre centre de données a l’intégration d’Azure CDN disponible, procédez comme suit :
 
  - Essayez de définir le `cdnEnabled` sur true.
  - Vérifiez le résultat renvoyé pour une `HTTP Error Code 412` (ne) avec un message de « Diffusion en continu de la propriété CdnEnabled de point de terminaison ne peut pas être définie sur true comme fonctionnalité CDN n’est pas disponible dans la région actuelle. » 

    Si vous recevez cette erreur, cela signifie que le centre de données ne la prend pas en charge. Vous devez essayer un autre centre de données.
- `cdnProfile` -Lorsque `cdnEnabled` est définie sur true, vous pouvez également passer `cdnProfile` valeurs. `cdnProfile` est le nom du profil CDN où le point du point de terminaison CDN doit être créé. Vous pouvez fournir un cdnProfile existant ou en utiliser un nouveau. Si la valeur est NULL et que `cdnEnabled` est true, la valeur par défaut « AzureMediaStreamingPlatformCdnProfile » est utilisée. Si le `cdnProfile` fourni existe déjà, un point de terminaison est créé sous celui-ci. Si le profil n’existe pas, un nouveau profil est automatiquement créé.
- `cdnProvider` -Lorsque le CDN est activé, vous pouvez également transmettre `cdnProvider` valeurs. `cdnProvider` contrôle les fournisseurs à utiliser. Actuellement, trois valeurs sont prises en charge : « StandardVerizon », « PremiumVerizon » et « StandardAkamai ». Si aucune valeur n’est fournie et `cdnEnabled` a la valeur true, « StandardVerizon » est utilisé (ce qui est la valeur par défaut).
- `crossSiteAccessPolicies` -Permet de spécifier des stratégies d’accès entre sites pour différents clients. Pour plus d’informations, consultez [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) et [Rendre un service disponible entre des limites de domaine](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>Les paramètres s’appliquent uniquement au format Smooth Streaming.
- `customHostNames` -Permet de configurer un point de terminaison de diffusion en continu pour accepter le trafic dirigé vers un nom d’hôte personnalisé.  Cette propriété est valide pour Standard et les points de terminaison de Streaming Premium et peut être définie lorsque `cdnEnabled`: false.
    
    La propriété du nom de domaine doit être vérifiée par Media Services. Media Services vérifie la propriété de nom de domaine en demandant un `CName` enregistrement qui contient l’ID de compte Media Services en tant que composant à ajouter au domaine en cours d’utilisation. Par exemple, pour utiliser « sports.contoso.com » comme nom d’hôte personnalisé pour le point de terminaison de streaming, un enregistrement pour `<accountId>.contoso.com` doit être configuré de façon à pointer vers un des noms d’hôte de vérification de Media Services. Le nom d’hôte de vérification est composé de verifydns.\<mediaservices-dns-zone>. 

    Les zones DNS attendues à utiliser dans l’enregistrement de vérification pour les différentes régions Azure sont les suivantes :
  
  - Amérique du Nord, Europe, Singapour, RAS de Hong Kong, Japon :
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Chine :
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Par exemple, un `CName` enregistrement qui mappe « 945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com » à « verifydns.media.azure.net » prouve que l’ID de Media Services 945a4c4e-28ea-45cd-8ccb-a519f6b700ad est propriétaire du domaine contoso.com, par conséquent, l’activation de n’importe quel nom sous contoso.com à utiliser comme nom d’hôte personnalisé pour un point de terminaison de diffusion en continu sous ce compte. Pour rechercher la valeur de l’ID du Service multimédia, accédez au [portail Azure](https://portal.azure.com/) et sélectionnez votre compte Service multimédia. Le **ID de compte** s’affiche dans le coin supérieur droit de la page.
        
    si vous tentez de définir un nom d’hôte personnalisé sans vérification appropriée de l’enregistrement `CName`, la réponse DNS échoue, puis elle est mise en cache pendant un certain temps. Une fois qu’un enregistrement approprié est en place, un certain temps peut être nécessaire pour que la réponse mise en cache soit revalidée. Selon le fournisseur DNS pour le domaine personnalisé, quelques minutes à une heure peuvent être nécessaires pour revalider l’enregistrement.
        
    Outre le `CName` qui mappe `<accountId>.<parent domain>` à `verifydns.<mediaservices-dns-zone>`, vous devez créer un autre `CName` qui mappe le nom d’hôte personnalisé (par exemple, `sports.contoso.com`) au nom d’hôte de votre Media Services de diffusion en continu du point de terminaison (par exemple, `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Points de terminaison situés dans le même centre de données de diffusion en continu ne peuvent pas partager le même nom d’hôte personnalisé.

    Actuellement, Media Services ne prend en charge SSL avec des domaines personnalisés. 
    
- `maxCacheAge` -Les remplacements de cache HTTP MaxAge par défaut contrôler en-tête défini par le point de terminaison de diffusion en continu sur des fragments multimédias et les manifestes à la demande. La valeur est définie en secondes.
- `resourceState` -

    - Arrêté - l’état initial d’un point de terminaison de diffusion en continu après sa création
    - Démarrage - passe à l’état en cours d’exécution
    - En cours d’exécution - est en mesure de diffuser du contenu aux clients
    - Échelle - les unités sont en cours augmentée ou réduite
    - Arrêt en cours - est en transition vers l’état arrêté
    - Suppression – est en cours de suppression
    
- `scaleUnits` -Vous offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbits/s. Si vous devez passer à un type **Premium**, ajustez `scaleUnits`.

## <a name="working-with-cdn"></a>Utilisation avec un CDN

Dans la plupart des cas, un CDN doit être activé. Cependant, si vous prévoyez un nombre maximal d’accès simultanés inférieur à 500 visiteurs, il est recommandé de désactiver le CDN, car celui-ci se met à l’échelle de façon optimale avec les accès simultanés.

### <a name="considerations"></a>Considérations

* Le point de terminaison de diffusion en continu `hostname` et l’URL de diffusion en continu restent identiques que vous activiez le CDN ou non.
* Si vous avez besoin de tester votre contenu avec ou sans CDN, vous pouvez créer un autre terminaison de Streaming qui n’est pas activé pour le CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Explication détaillée du fonctionnement de la mise en cache

Il n’existe pas de valeur de bande passante spécifique lors de l’ajout du CDN, car la quantité de bande passante nécessaire pour un point de terminaison de streaming avec CDN varie. Cela dépend beaucoup du type de contenu, du nombre de visiteurs, de la vitesse de transmission et des protocoles. Le CDN met en cache seulement ce qui est demandé. Cela signifie que le contenu qui est souvent demandé est fourni directement par le CDN, tant que le fragment vidéo est mis en cache. Le contenu en direct est susceptible d’être mis en cache, car en général, beaucoup de gens regardent exactement la même chose. Le contenu à la demande peut être plus problématique, car vous pouvez avoir du contenu avec une demande forte et du contenu peu demandé. Si vous avez des millions de ressources vidéo avec peu de demandes de visualisation (seulement 1 ou 2 visiteurs par semaine), mais que vous avez des milliers de personnes regardant chacune des vidéos différentes, le CDN devient beaucoup moins efficace. Avec ces absences dans le cache, vous augmentez la charge sur le point de terminaison de streaming.
 
Vous devez également prendre en compte le fonctionnement du streaming adaptif. Chaque fragment vidéo individuel est mis en cache comme entité propre. Par exemple si, la première fois qu’une certaine vidéo est regardée, la personne ne regarde que quelques secondes ici et là, seuls les fragments vidéo associés à ce que la personne a regardé sont mis en cache dans le CDN. Avec le streaming adaptatif, vous avez en général 5 à 7 vitesses de transmission de vidéo différentes. Si une personne regarde à une certaine vitesse de transmission et qu’une autre personne regarde à une autre vitesse de transmission, le contenu qu’ils regardent chacun est mis en cache séparément dans le CDN. Même si deux personnes regardent du contenu à la même vitesse de transmission, leur streaming peut se faire via des protocoles différents. Chaque protocole (HLS, MPEG-DASH, Smooth Streaming) est mis en cache séparément. Ainsi, chaque vitesse de transmission et chaque protocole sont mis en cache séparément, et seuls les fragments vidéo qui ont été demandés sont mis en cache.

### <a name="enable-azure-cdn-integration"></a>Activer l’intégration d’Azure CDN

Une fois configuré avec un point de terminaison de diffusion en continu CDN activé il est un délai d’attente défini sur Media Services avant la mise à jour DNS est effectuée pour mapper le point de terminaison de diffusion en continu au point de terminaison CDN.

Si vous souhaitez activer/désactiver le CDN ultérieurement, votre point de terminaison de streaming doit avoir l’état **Arrêté**. Il peut s’écouler jusqu’à deux heures avant que l’intégration d’Azure CDN soit active et que les modifications soient actives sur tous les comptes POP CDN. Toutefois, vous pouvez démarrer votre point de terminaison de streaming et le flux sans interruptions à partir du point de terminaison de streaming. Une fois l’intégration terminée, le flux est émis à partir du CDN. Pendant la durée de l’approvisionnement, votre point de terminaison de streaming est en état de **démarrage** et vous pouvez observer une dégradation des performances.

Lorsque le point de terminaison de streaming Standard est créé, il est configuré par défaut avec Verizon Standard. Vous pouvez configurer les fournisseurs de Premium Verizon ou Akamai Standard à l’aide des API REST. 

L’intégration du CDN est activée dans tous les centres de données Azure, sauf dans les régions Gouvernement fédéral et Chine.

> [!IMPORTANT]
> L’intégration d’Azure Media Services au CDN Azure est implémentée sur le **CDN Azure fourni par Verizon** pour les points de terminaison de streaming Standard. Les points de terminaison de streaming Premium peuvent être configurés à l’aide de l’ensemble des **fournisseurs et niveaux de tarification Azure CDN**. Pour plus d’informations sur les fonctionnalités du CDN Azure, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN)](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Déterminer si DNS a été modifié

Vous pouvez déterminer si DNS a été modifié sur un point de terminaison de diffusion en continu (le trafic dirigé vers le CDN Azure) à l’aide de https://www.digwebinterface.com. Si les résultats contient des noms de domaine azureedge.net dans les résultats, le trafic est maintenant pointé le CDN.

## <a name="provide-feedback"></a>Fournir des commentaires

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

L’exemple [de ce dépôt](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) montre comment démarrer le point de terminaison de streaming par défaut avec .NET.

