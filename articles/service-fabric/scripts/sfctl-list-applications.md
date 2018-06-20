---
title: Exemple de script CLI Service Fabric - Liste des applications dans un cluster
description: Exemple de script CLI Service Fabric - Répertorie les applications configurées sur un cluster Service Fabric.
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: 2a58a9d534116dbdd486f1319f3d87e1d2ba24ef
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642765"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Répertorier les applications exécutées dans un cluster Service Fabric

Cet exemple de script se connecte à un cluster Service Fabric puis répertorie toutes les applications configurées.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [documentation relative à Service Fabric CLI](../service-fabric-cli.md).

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).
