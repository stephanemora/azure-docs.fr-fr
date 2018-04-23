---
title: Vue d’ensemble de l’analyse de l’intégrité pour Azure Application Gateway
description: En savoir plus sur les capacités d’analyse dans Azure Application Gateway
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2018
ms.author: victorh
ms.openlocfilehash: 2f62f01c1178f9529eb46051f088affccc5279a7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2018
---
# <a name="application-gateway-health-monitoring-overview"></a>Vue d’ensemble de l’analyse d’intégrité Application Gateway

Azure Application Gateway analyse par défaut l’intégrité de toutes les ressources de son pool principal et supprime automatiquement du pool les ressources considérées comme défectueuses. Application Gateway continue de surveiller les instances défaillantes et les réintroduit dans le pool principal intègre une fois qu’elles redeviennent disponibles et répondent aux sondes d’intégrité. La passerelle d’application envoie les sondes d’intégrité avec le même port que celui défini dans les paramètres HTTP du serveur principal. Cette configuration garantit que la sonde teste le même port que celui qu’utiliseraient les clients pour se connecter au serveur principal.

![exemple de sonde application gateway][1]

En plus d’utiliser la surveillance par sonde d’intégrité par défaut, vous pouvez aussi personnaliser la sonde d’intégrité pour répondre aux exigences de votre application. Dans cet article, nous nous intéressons aux sondes d’intégrité par défaut et personnalisées.

> [!NOTE]
> Si le sous-réseau Application Gateway comporte un groupe de sécurité réseau, les plages de ports 65503-65534 doivent être ouvertes sur ce sous-réseau pour permettre l’arrivée du trafic entrant. Ces ports sont requis pour permettre à l’API relative à l’intégrité du serveur principal de fonctionner correctement.

## <a name="default-health-probe"></a>Sonde d’intégrité par défaut

Une passerelle d’application configure automatiquement une sonde d’intégrité par défaut lorsque vous ne définissez pas de configuration de sonde personnalisée. Le comportement d’analyse par défaut consiste à lancer une requête HTTP aux adresses IP configurées pour le pool principal. En ce qui concerne les sondes par défaut, si les paramètres HTTP du serveur principal sont configurés pour HTTPS, la sonde utilise également HTTPS pour tester l’intégrité des serveurs principaux.

Par exemple : vous configurez votre passerelle d’application pour utiliser les serveurs principaux A, B et C, qui recevront le trafic réseau HTTP sur le port 80. Les contrôles de défaillance par défaut testent les trois serveurs toutes les 30 secondes pour obtenir une réponse HTTP correcte. Le [code d’état](https://msdn.microsoft.com/library/aa287675.aspx) d’une réponse HTTP correcte est compris entre 200 et 399.

Si l’analyse de la sonde par défaut échoue pour le serveur A, la passerelle d’application le retire de son pool principal et le trafic réseau cesse de passer par ce serveur. La sonde par défaut continue de contrôler le serveur A toutes les 30 secondes. Dès que le serveur A répond avec succès à une requête de la sonde d’intégrité par défaut, il est réintroduit dans le pool principal en tant que serveur intègre et le trafic vers celui-ci reprend.

### <a name="probe-matching"></a>Correspondance des sondes

Par défaut, une réponse HTTP(S) avec le code d’état 200 est considérée comme intègre. Les sondes d’intégrité personnalisées prennent également en charge deux critères de correspondance. Les critères de correspondance peuvent être utilisés pour éventuellement modifier l’interprétation par défaut de ce qui constitue une réponse intègre.

Les éléments suivants sont des critères de correspondance : 

- **Correspondance de code d’état de la réponse HTTP** : le critère de correspondance de la sonde pour l’acceptation du code de réponse http spécifié par l’utilisateur ou des plages de codes de réponse. Les codes d’état de réponse séparées par des virgules individuelles ou une plage de codes d’état sont pris en charge.
- **Correspondance du corps de la réponse HTTP** : le critère de correspondance de la sonde qui examine le corps de la réponse HTTP et correspond à une chaîne spécifiée par l’utilisateur. Notez que la correspondance ne tient compte que de la présence d’une chaîne spécifiée par l’utilisateur dans le corps de la réponse et n’est pas une correspondance d’expression régulière complète.

Les critères de correspondance peuvent être spécifiés à l’aide de la cmdlet `New-AzureRmApplicationGatewayProbeHealthResponseMatch`.

Par exemple : 

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Une fois les critères de correspondance spécifiés, ils peuvent être joints à la configuration de la sonde à l’aide d’un `-Match` paramètre dans PowerShell.

### <a name="default-health-probe-settings"></a>Paramètres de sonde d’intégrité par défaut

| Propriétés de la sonde | Valeur | Description |
| --- | --- | --- |
| URL de sonde |http://127.0.0.1:\<port\>/ |Chemin d'accès de l'URL |
| Intervalle |30 |Intervalle d’analyse en secondes |
| Délai d’attente |30 |Délai d’expiration de l’analyse en secondes |
| Seuil de défaillance sur le plan de l’intégrité |3 |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

> [!NOTE]
> Le port est le même que celui utilisé par les paramètres HTTP du serveur principal.

La sonde par défaut examine uniquement le http://127.0.0.1:\<port\> pour déterminer l’état d’intégrité. Si vous devez configurer la sonde d’intégrité de sorte qu’elle accède à une URL personnalisée ou modifier d’autres paramètres, vous devez utiliser des sondes personnalisées comme décrit dans les étapes suivantes :

## <a name="custom-health-probe"></a>Sonde d’intégrité personnalisée

Les sondes personnalisées vous permettent d’avoir un contrôle plus précis de l’analyse de l’intégrité. En utilisant des sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL et le chemin à tester et le nombre de réponses en échec autorisé avant que l’instance de pool principal soit marquée comme étant défectueuse.

### <a name="custom-health-probe-settings"></a>Paramètres de sonde d’intégrité personnalisée

Le tableau suivant fournit des définitions pour les propriétés d’une sonde d’intégrité personnalisée.

| Propriétés de la sonde | Description |
| --- | --- |
| NOM |Nom de la sonde. Ce nom est utilisé pour désigner la sonde dans les paramètres HTTP du serveur principal. |
| Protocole |Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP du serveur principal |
| Host |Nom d’hôte pour l’envoi de la sonde. S’applique uniquement lorsque plusieurs sites sont configurés sur Application Gateway, sinon utilisez '127.0.0.1'. Cette valeur est différente du nom d’hôte de la machine virtuelle. |
| path |Chemin relatif de la sonde. Le chemin valide commence par « / ». |
| Intervalle |Intervalle d’analyse en secondes. Cette valeur est l’intervalle de temps qui s’écoule entre deux analyses consécutives. |
| Délai d’attente |Délai d’expiration de l’analyse en secondes. Si aucune réponse valide n’est reçue dans le délai imparti, la sonde est marquée comme étant en échec.  |
| Seuil de défaillance sur le plan de l’intégrité |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

> [!IMPORTANT]
> Si Application Gateway est configuré pour un seul site, le nom d’hôte par défaut doit être spécifié sous la forme « 127.0.0.1 », sauf s’il est configuré d’une autre manière dans la sonde personnalisée.
> Pour référence, une sonde personnalisée est envoyée à \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\>. Le port utilisé est identique à celui défini dans les paramètres HTTP du serveur principal.

## <a name="next-steps"></a>Étapes suivantes
Après vous être familiarisé avec l’analyse d’intégrité Application Gateway, vous pouvez configurer une [sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md) dans le portail Azure ou une [sonde d’intégrité personnalisée](application-gateway-create-probe-ps.md) à l’aide de PowerShell et du modèle de déploiement Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
