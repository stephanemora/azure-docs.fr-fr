---
title: Ouverture des ports de pare-feu requis pour une application de proxy d’application | Microsoft Docs
description: Découvrez quels ports ouvrir pour le bon fonctionnement du proxy d’application Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: efc8c0f0581878d41eb62cbfbd88aec730ecf789
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175234"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Ouverture des ports de pare-feu requis pour une application de proxy d’application

Pour voir la liste complète des ports requis et la fonction de chaque port, consultez la section dédiée aux conditions préalables de la [documentation du proxy d’application](application-proxy-add-on-premises-application.md). Le proxy d’application utilise uniquement des ports de sortie.

Vous pouvez également vérifier si tous les ports requis sont ouverts par le biais de l’[outil de test des ports du connecteur](https://aadap-portcheck.connectorporttest.msappproxy.net/) à partir de votre réseau local. Un nombre plus élevé de coches vertes signifie une résilience accrue. 

## <a name="app-proxy-regions"></a>Régions du proxy d’application

Nous travaillons sur un moyen de vous informer des régions qui doivent vous apparaître en vert. Pour l’instant, assurez-vous que toutes le sont. La région USA Centre est également requise, quelle que soit la région où vous vous trouvez.

Pour vous assurer que l’outil fournit des résultats corrects, veillez à :

-   Ouvrir l’outil sur un navigateur à partir du serveur sur lequel vous avez installé le connecteur.

-   Vous assurer que les proxys ou pare-feu applicables à votre connecteur sont également appliqués à cette page. Cela peut être fait dans Internet Explorer. Pour cela, allez dans **Paramètres** -&gt; **Options Internet** -&gt; **Connexions** -&gt; **Paramètres de réseau local**. Cette page comporte le champ Utiliser un serveur proxy pour votre réseau local. Activez cette case et entrez l’adresse proxy dans le champ Adresse.

## <a name="next-steps"></a>Étapes suivantes
[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-connectors.md)
