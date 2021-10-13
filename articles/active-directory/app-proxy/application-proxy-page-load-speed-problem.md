---
title: Le chargement d'une application Proxy d'application Azure Active Directory prend trop de temps
description: Résoudre les problèmes de performances liés à un chargement de page avec le Proxy d'application Azure Active Directory
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 2de7e8df9148efdc04b49a1e7642eef739dce399
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612785"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Le chargement d’une application Proxy d’application prend trop de temps

Cet article vous aide à comprendre pourquoi le chargement d’une application Proxy d’application peut prendre beaucoup de temps. Il explique également ce que vous pouvez faire pour résoudre ce problème.

## <a name="overview"></a>Vue d’ensemble
Bien que vos applications fonctionnent, elles peuvent subir un temps de latence. Il peut y avoir des modifications de topologie de réseau que vous pouvez effectuer pour améliorer la vitesse. Pour une évaluation des différentes topologies, consultez le [document des considérations sur la topologie du réseau](application-proxy-network-topology.md).

Outre la topologie du réseau, il n’existe actuellement aucune autre recommandation pour l’optimisation des performances. Lorsque le service de proxy d’application se développe, il peut s’étendre à un centre de données qui est physiquement plus proche. La plus grande proximité peut améliorer la latence. Pour obtenir la liste de centres de données Azure, consultez la [page de test de latence](http://www.azurespeed.com/Azure/Latency). 

## <a name="next-steps"></a>Étapes suivantes
[Travailler avec des serveurs proxy locaux existants](application-proxy-configure-connectors-with-proxy-servers.md)
