---
title: Points de terminaison de streaming (origine)
titleSuffix: Azure Media Services
description: Découvrez les points de terminaison de streaming (origine), un service dynamique d’empaquetage et de diffusion en continu fournissant du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: c1e9be605a6f01695f2472ae76a9e5a786388aa0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206104"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Points de terminaison de streaming (origine) dans Azure Media Services

Dans Microsoft Azure Media Services, un [point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) représente un empaquetage dynamique (juste-à-temps) et un service d’origine qui permet de transmettre votre contenu en direct et à la demande directement à une application de lecteur cliente, à l’aide de l’un des protocoles de streaming multimédia courants (HLS ou DASH). En outre, le **point de terminaison de streaming** offre un chiffrement dynamique (juste-à-temps) aux systèmes DRM de pointe.

Quand vous créez un compte Media Services, un point de terminaison de streaming **par défaut** est créé pour vous dans l’état Arrêté. Vous ne pouvez pas supprimer le point de terminaison de streaming **par défaut**. Vous pouvez créer d’autres points de terminaison de streaming sous votre compte (voir [Quotas et limitations](limits-quotas-constraints.md)).

> [!NOTE]
> Pour démarrer le streaming de vidéos, vous devez démarrer le **point de terminaison de streaming** à partir duquel vous souhaitez diffuser la vidéo.
>
> Vous êtes facturé uniquement lorsque votre point de terminaison de streaming est en cours d’exécution.

## <a name="naming-convention"></a>Conventions d’affectation de noms

Le format du nom d’hôte de l’URL de streaming est le suivant : `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, où `servicename` est le nom du point de terminaison de streaming ou le nom de l’événement en direct.

Quand vous utilisez le point de terminaison de streaming par défaut, `servicename` est omis ; l’URL est alors : `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Limites

* Le nom du point de terminaison de streaming peut contenir 24 caractères au maximum.
* Le nom doit suivre ce modèle [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Types

Il existe deux types de **points de terminaison de streaming** : **Standard** (préversion) et **Premium**. Le type est défini par le nombre d’unités d’échelle (`scaleUnits`) que vous allouez pour le point de terminaison de streaming.

Le tableau décrit les types :

|Type|Unités d’échelle|Description|
|--------|--------|--------|  
|**Standard**|0|Le point de terminaison de streaming par défaut est de type **Standard**, mais vous pouvez le passer au type Premium en ajustant `scaleUnits`.|
|**Premium**|>0|Les points de terminaison de streaming **Premium** sont conçus pour les charges de travail avancées et fournissent une capacité de bande passante dédiée et évolutive. Vous pouvez passer à un type **Premium** en ajustant `scaleUnits` (unités de streaming). Les `scaleUnits` vous offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbits/s. Quand vous utilisez le type **Premium**, chaque unité activée fournit une capacité de bande passante supplémentaire à l’application. |

> [!NOTE]
> Pour les clients qui souhaitent distribuer du contenu à un large public sur internet, il est conseillé d’activer le CDN sur le point de terminaison de streaming.

Pour plus d’informations sur le contrat SLA, consultez [Tarifs et contrat SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Comparaison des types de streaming

Fonctionnalité|standard|Premium
---|---|---
Débit |Jusqu’à 600 Mbits/s, et le débit fourni peut être beaucoup plus élevé avec un CDN.|200 Mbits/s par unité de streaming (SU). Le débit fourni peut être beaucoup plus élevé avec un CDN.
CDN|Azure CDN, CDN tiers ou sans CDN.|Azure CDN, CDN tiers ou sans CDN.
La facturation est calculée sur la base d'un taux| Quotidien|Quotidien
Chiffrement dynamique|Oui|Oui
l’empaquetage dynamique|Oui|Oui
Scale|Mise à l’échelle automatique vers le débit cible.|Unités de streaming supplémentaires
Hôte de filtrage d’IP/G20/personnalisé <sup>1</sup>|Oui|Oui
Téléchargement progressif|Oui|Oui
Utilisation recommandée |Recommandé pour la plupart des scénarios de streaming.|Utilisation professionnelle.

<sup>1</sup> Uniquement utilisé directement sur le point de terminaison de streaming quand le CDN n’est pas activé sur le point de terminaison.<br/>

## <a name="properties"></a>Propriétés

Cette section fournit des détails sur certaines propriétés du point de terminaison de streaming. Pour obtenir des exemples montrant comment créer un nouveau point de terminaison de streaming et les descriptions de toutes les propriétés, consultez [Point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: Permet de configurer les paramètres de sécurité suivants pour ce point de terminaison de diffusion en continu : Les clés d’authentification d’en-tête de signature Akamai et les adresses IP qui sont autorisées à se connecter à ce point de terminaison. Cette propriété peut uniquement être définie quand `cdnEnabled` est défini sur false.

- `cdnEnabled`: Indique si l’intégration d’Azure CDN pour ce point de terminaison de streaming est activée (elle est désactivée par défaut). Si vous définissez `cdnEnabled` sur true, les configurations suivantes sont désactivées : `customHostNames` et `accessControl`.

    Certains centres de données ne prennent pas en charge l’intégration d’Azure CDN. Pour vérifier si l’intégration d’Azure CDN est disponible pour votre centre de données, procédez comme suit :

  - Essayez de définir `cdnEnabled` sur true.
  - Vérifiez le résultat retourné pour `HTTP Error Code 412` (PreconditionFailed) avec le message « La propriété CdnEnabled du point de terminaison de streaming ne peut pas être définie sur true, car la fonctionnalité CDN n’est pas disponible dans la région actuelle ».

    Si vous recevez cette erreur, cela signifie que le centre de données ne la prend pas en charge. Essayez un autre centre de données.

- `cdnProfile`: Quand `cdnEnabled` est définie sur true, vous pouvez également passer des valeurs de `cdnProfile` valeurs. `cdnProfile` est le nom du profil CDN où le point du point de terminaison CDN doit être créé. Vous pouvez fournir un cdnProfile existant ou en utiliser un nouveau. Si la valeur est NULL et que `cdnEnabled` est true, la valeur par défaut « AzureMediaStreamingPlatformCdnProfile » est utilisée. Si le `cdnProfile` fourni existe déjà, un point de terminaison est créé sous celui-ci. Si le profil n’existe pas, un nouveau profil est créé automatiquement.
- `cdnProvider`: Quand le CDN est activé, vous pouvez également passer des valeurs de `cdnProvider`. `cdnProvider` contrôle les fournisseurs à utiliser. Actuellement, trois valeurs sont prises en charge : « StandardVerizon », « PremiumVerizon » et « StandardAkamai ». Si aucune valeur n’est fournie et que `cdnEnabled` a la valeur true, « StandardVerizon » est utilisé (c’est la valeur par défaut).
- `crossSiteAccessPolicies`: Utilisée pour spécifier des stratégies d’accès entre sites pour différents clients. Pour plus d’informations, consultez [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) et [Rendre un service disponible entre des limites de domaine](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). Les paramètres s’appliquent uniquement au format Smooth Streaming.
- `customHostNames`: Utilisée pour configurer un point de terminaison de streaming pour qu’il accepte le trafic dirigé vers un nom d’hôte personnalisé. Cette propriété est valide pour les points de terminaison de streaming Standard et Premium, et elle peut être définie quand `cdnEnabled` est définie sur false.

    La propriété du nom de domaine doit être vérifiée par Media Services. Media Services vérifie la propriété du nom de domaine en demandant un enregistrement `CName` qui contient l’ID de compte Media Services comme composant à ajouter au domaine utilisé. Par exemple, pour utiliser « sports.contoso.com » comme nom d’hôte personnalisé pour le point de terminaison de streaming, un enregistrement pour `<accountId>.contoso.com` doit être configuré de façon à pointer vers un des noms d’hôte de vérification de Media Services. Le nom d’hôte de vérification est composé de verifydns.\<mediaservices-dns-zone>.

    Voici les zones DNS attendues dans l’enregistrement de vérification pour les différentes régions Azure.
  
  - Amérique du Nord, Europe, Singapour, Hong Kong (R.A.S.), Japon :

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Chine :

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Par exemple, un enregistrement `CName` qui mappe « 945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com » à « verifydns.media.azure.net » prouve que l’ID Media Services 945a4c4e-28ea-45cd-8ccb-a519f6b700ad est propriétaire du domaine contoso.com, permettant ainsi l’utilisation de n’importe quel nom sous contoso.com comme nom d’hôte personnalisé pour un point de terminaison de streaming sous ce compte. Pour rechercher la valeur de l’ID du Service multimédia, accédez au [portail Azure](https://portal.azure.com/) et sélectionnez votre compte Service multimédia. L’**ID de compte** s’affiche dans le coin supérieur droit de la page.

    Si vous tentez de définir un nom d’hôte personnalisé sans vérification appropriée de l’enregistrement `CName`, la réponse DNS échoue, puis elle est mise en cache pendant un certain temps. Une fois qu’un enregistrement approprié est en place, un certain temps peut être nécessaire pour que la réponse mise en cache soit revalidée. Selon le fournisseur DNS pour le domaine personnalisé, quelques minutes à une heure sont nécessaires pour revalider l’enregistrement.

    En plus du `CName` qui mappe `<accountId>.<parent domain>` à `verifydns.<mediaservices-dns-zone>`, vous devez créer un autre `CName` qui mappe le nom d’hôte personnalisé (par exemple `sports.contoso.com`) au nom d’hôte de votre point de terminaison de streaming Media Services (par exemple `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Des points de terminaison de streaming qui se trouvent dans le même centre de données ne peuvent pas partager le même nom d’hôte personnalisé.

    Actuellement, Media Services ne prend pas en charge SSL avec les domaines personnalisés.

- `maxCacheAge` : remplace l’en-tête de contrôle du cache HTTP max-age par défaut défini par le point de terminaison de streaming sur des fragments de médias et des manifestes à la demande. La valeur est définie en secondes.
- `resourceState` -

    - Arrêté : état initial d’un point de terminaison de streaming après sa création.
    - Démarrage : le point de terminaison de streaming est en transition vers l’état « Exécution en cours ».
    - Exécution en cours : le point de terminaison de streaming peut diffuser du contenu aux clients.
    - Mise à l’échelle : les unités d’échelle sont augmentées ou diminuées.
    - Arrêt : le point de terminaison de streaming est en transition vers l’état « Arrêté ».
    - Suppression : votre travail est en cours de suppression.

- `scaleUnits`: Offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbits/s. Si vous devez passer à un type **Premium**, ajustez `scaleUnits`.

## <a name="working-with-cdn"></a>Utilisation avec un CDN

Dans la plupart des cas, un CDN doit être activé. Cependant, si vous prévoyez un nombre maximal d’accès simultanés inférieur à 500 visiteurs, il est recommandé de désactiver le CDN, car celui-ci se met à l’échelle de façon optimale avec les accès simultanés.

### <a name="considerations"></a>Considérations

* Le point de terminaison de streaming `hostname` et l’URL de streaming restent identiques, que vous activiez le CDN ou non.
* Si vous avez besoin de tester votre contenu avec ou sans CDN, créez un autre point de terminaison de streaming qui n’est pas activé pour le CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Explication détaillée du fonctionnement de la mise en cache

Il n’existe pas de valeur de bande passante spécifique lors de l’ajout du CDN, car la quantité de bande passante nécessaire pour un point de terminaison de streaming avec CDN varie. Cela dépend beaucoup du type de contenu, du nombre de visiteurs, de la vitesse de transmission et des protocoles. Le CDN met en cache seulement ce qui est demandé. Cela signifie que le contenu qui est souvent demandé est fourni directement par le CDN, tant que le fragment vidéo est mis en cache. Le contenu en direct est susceptible d’être mis en cache, car en général, beaucoup de gens regardent exactement la même chose. Le contenu à la demande peut être plus problématique, car vous pouvez avoir du contenu avec une demande forte et du contenu peu demandé. Si vous avez des millions de ressources vidéo avec peu de demandes de visualisation (seulement un ou deux visiteurs par semaine), mais que vous avez des milliers de personnes regardant chacune des vidéos différentes, le CDN devient beaucoup moins efficace. Avec ces absences dans le cache, vous augmentez la charge sur le point de terminaison de streaming.

Vous devez également prendre en compte le fonctionnement du streaming adaptif. Chaque fragment vidéo individuel est mis en cache comme entité propre. Par exemple, imaginez la première fois qu’une vidéo est visionnée. Si la personne ne regarde que quelques secondes ici et là, seuls les fragments vidéo associés à ce que la personne a regardé sont mis en cache dans le CDN. Avec le streaming adaptatif, vous avez en général 5 à 7 vitesses de transmission de vidéo différentes. Si une personne regarde à une certaine vitesse de transmission et qu’une autre personne regarde à une autre vitesse de transmission, le contenu qu’ils regardent chacun est mis en cache séparément dans le CDN. Même si deux personnes regardent du contenu à la même vitesse de transmission, leur streaming peut se faire via des protocoles différents. Chaque protocole (HLS, MPEG-DASH, Smooth Streaming) est mis en cache séparément. Ainsi, chaque vitesse de transmission et chaque protocole sont mis en cache séparément, et seuls les fragments vidéo qui ont été demandés sont mis en cache.

### <a name="enable-azure-cdn-integration"></a>Activer l’intégration au CDN Azure

> [!IMPORTANT]
> Vous ne pouvez pas activer le CDN pour les comptes Azure d’évaluation ou d’étudiant.
>
> L’intégration du CDN est activée dans tous les centres de données Azure, sauf dans les régions Chine et du gouvernement fédéral.

Une fois le point de terminaison de streaming provisionné et le CDN activé, il existe un délai d’attente défini sur Media Services avant la mise à jour des DNS pour le mappage du point de terminaison de streaming au point de terminaison CDN.

Si vous souhaitez activer/désactiver le CDN ultérieurement, votre point de terminaison de streaming doit avoir l’état **Arrêté**. Il peut s’écouler jusqu’à deux heures avant que l’intégration d’Azure CDN soit active et que les modifications soient actives sur tous les comptes POP CDN. Toutefois, vous pouvez démarrer votre point de terminaison de streaming et le flux sans interruptions à partir du point de terminaison de streaming. Une fois l’intégration terminée, le flux est émis à partir du CDN. Pendant la durée de l’approvisionnement, votre point de terminaison de streaming est en état de **démarrage** et vous pouvez observer une dégradation des performances.

Lorsque le point de terminaison de streaming Standard est créé, il est configuré par défaut avec Standard Verizon. Vous pouvez configurer les fournisseurs Premium Verizon ou Standard Akamai à l’aide des API REST.

L’intégration d’Azure Media Services au CDN Azure est implémentée sur le **CDN Azure fourni par Verizon** pour les points de terminaison de streaming Standard. Les points de terminaison de streaming Premium peuvent être configurés à l’aide de l’ensemble des **fournisseurs et niveaux de tarification Azure CDN**. 

> [!NOTE]
> Pour plus d’informations sur Azure CDN, consultez [Présentation du CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-was-made"></a>Déterminer si des modifications ont été apportées aux DNS

Vous pouvez déterminer si les DNS ont été modifiés sur un point de terminaison de streaming (le trafic est dirigé vers le CDN Azure) avec https://www.digwebinterface.com. Si les résultats contiennent des noms de domaine azureedge.net, le trafic est maintenant dirigé vers le CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="see-also"></a>Voir aussi

[Présentation du CDN](../../cdn/cdn-overview.md)

## <a name="next-steps"></a>Étapes suivantes

L’exemple [de ce dépôt](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) montre comment démarrer le point de terminaison de streaming par défaut avec .NET.
