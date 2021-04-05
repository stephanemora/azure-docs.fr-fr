---
title: Résolution des problèmes de compression de fichiers dans Azure Front Door Standard/Premium
description: Découvrez comment résoudre les problèmes de compression de fichiers dans Azure Front Door. Cet article aborde plusieurs causes possibles.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097830"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Résolution des problèmes de compression de fichiers d’Azure Front Door Standard/Premium

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Cet article vous aide à résoudre les problèmes de compression de fichiers d’Azure Front Door Standard/Premium.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Symptôme

La compression pour votre route est activée, mais les fichiers sont retournés non compressés.

> [!TIP]
> Pour vérifier si vos fichiers sont retournés sous une forme compressée, vous devez utiliser un outil tel que [Fiddler](https://www.telerik.com/fiddler) ou les [outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) de votre navigateur.  Vérifiez les en-têtes de réponse HTTP retournés avec votre contenu CDN mis en cache.  S’il existe un en-tête nommé `Content-Encoding` avec une valeur **gzip**, **bzip2**, ou **deflate**, votre contenu est compressé.
> 
> ![En-tête d’encodage de contenu](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Cause

Il existe plusieurs causes possibles, y compris :

* Le contenu demandé n’est pas éligible à la compression.
* La compression n’est pas activée pour le type de fichier demandé.
* La requête HTTP ne comprenait pas un en-tête demandant un type de compression valide.
* L’origine envoie du contenu segmenté.

## <a name="troubleshooting-steps"></a>Étapes de dépannage

> [!TIP]
> Comme avec le déploiement de nouveaux points de terminaison, les modifications de configuration d’AFD prennent un certain temps pour se propager sur le réseau.  En règle générale, les modifications sont appliquées dans les 90 minutes.  S’il s’agit de la première fois que vous avez configuré la compression pour votre point de terminaison CDN, vous devez envisager d’attendre 1 à 2 heures pour vous assurer que les paramètres de compression ont été transmis aux POP. 
> 

### <a name="verify-the-request"></a>Vérifier la requête

D’abord, nous devons bien vérifier la requête. Vous pouvez utiliser les **[outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** de votre navigateur pour voir les requêtes en cours.

* Vérifiez que la requête est envoyée à l’URL de votre point de terminaison, `<endpointname>.z01.azurefd.net`, et non pas à votre origine.
* Vérifiez que la requête contient un en-tête **Accept-Encoding** et que la valeur de cet en-tête contient **gzip**, **deflate** ou **bzip2**.

![En-têtes de requête CDN](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Vérifier les paramètres de compression

Accédez à votre point de terminaison dans le [portail Azure](https://portal.azure.com), puis cliquez sur le bouton **Configurer** dans le panneau Routes. Vérifiez que la compression est **activée**.

![Paramètres de compression CDN](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Vérifiez la requête sur le serveur d’origine pour un en-tête **Via**

L’en-tête HTTP **Via** indique au serveur web que la requête est transmise par un serveur proxy.  Par défaut, les serveurs web Microsoft IIS ne compressent pas les réponses quand la requête contient un en-tête **Via**.  Pour changer ce comportement, effectuez les opérations suivantes :

* **IIS 6** : Définissez HcNoCompressionForProxies="FALSE" dans les propriétés de la métabase IIS. Pour plus d’informations, consultez la page [Compression d’IIS 6](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 et ultérieur** : Définissez **noCompressionForHttp10** et **noCompressionForProxies** sur *False* dans la configuration du serveur. Pour plus d’informations, consultez la page [Compression HTTP](https://www.iis.net/configreference/system.webserver/httpcompression).
