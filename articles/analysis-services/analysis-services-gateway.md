---
title: Passerelle de données locale pour Azure Analysis Services | Microsoft Docs
description: Une passerelle locale est nécessaire si votre serveur Analysis Services dans Azure se connecte à des sources de données locales.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310150"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Connexion aux sources de données locales avec la passerelle de données locale

La passerelle de données locale assure un transfert de données sécurisé entre des sources de données locales et vos serveurs Azure Analysis Services dans le cloud. Pouvant être utilisée avec plusieurs serveurs Azure Analysis Services dans la même région, la dernière version de la passerelle fonctionne également avec Azure Logic Apps, Power BI, Power Apps et Power Automate. Bien que la passerelle que vous installez est la même pour tous ces services, Azure Analysis Services et Logic Apps présentent des étapes supplémentaires.

Les informations fournies ici sont spécifiques à la façon dont Azure Analysis Services fonctionne avec la passerelle de données locale. Pour plus d’informations sur la passerelle en général et sur son fonctionnement avec d’autres services, consultez [Qu’est-ce qu’une passerelle de données locale ?](/data-integration/gateway/service-gateway-onprem).

Pour Azure Analysis Services, la première configuration de la passerelle se déroule en quatre étapes :

- **Télécharger et exécuter le programme d’installation** - Cette étape installe un service de passerelle sur un ordinateur de votre organisation. Vous vous connectez également à Azure à l’aide d’un compte du Azure AD de votre [locataire](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant). Les comptes Azure B2B (invité) ne sont pas pris en charge.

- **Inscrire votre passerelle** - Lors de cette étape, vous spécifiez un nom et une clé de récupération pour votre passerelle et vous sélectionnez une région, pour l’inscription de votre passerelle auprès du service cloud de passerelle. Votre ressource de passerelle peut être inscrite dans n’importe quelle région, mais il est recommandé de l’inscrire dans la même région que vos serveurs Analysis Services. 

- **Créer une ressource de passerelle dans Azure** - Dans cette étape, vous créez une ressource de passerelle dans Azure.

- **Connecter vos serveurs à votre ressource de passerelle** - Une fois que vous avez une ressource de passerelle, vous pouvez commencer à y connecter vos serveurs. Vous pouvez y connecter plusieurs serveurs et d’autres ressources, pourvu qu’ils soient dans la même région.



## <a name="how-it-works"> </a>Fonctionnement
La passerelle que vous installez sur un ordinateur de votre organisation s’exécute comme un service Windows, **Passerelle de données locale**. Ce service local est inscrit auprès du service cloud de passerelle via Azure Service Bus. Vous créez ensuite une ressource de passerelle de données locale pour votre abonnement Azure. Vos serveurs Azure Analysis Services sont alors connectés à vos ressources de passerelle Azure. Lorsque des modèles sur votre serveur doivent se connecter à vos sources de données locales pour des requêtes ou un traitement, un flux de données et de requête parcourt la ressource de passerelle, Azure Service Bus, le service de passerelle de données locale et vos sources de données. 

![Fonctionnement](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Requêtes et flux de données :

1. Une requête est créée par le service cloud avec les informations d’identification chiffrées de la source de données locale. Elle est ensuite envoyée dans une file d’attente pour être traitée par la passerelle.
2. Le service cloud de la passerelle analyse la requête et envoie la requête vers [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.
4. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte aux sources de données avec ces informations d’identification.
5. La passerelle envoie la requête à la source de données pour exécution.
6. Les résultats sont renvoyés de la source de données vers la passerelle, puis vers le service cloud et votre serveur.

## <a name="installing"></a>Installation

Lors de l’installation d’un environnement Azure Analysis Services, il est important de suivre les étapes décrites dans [Installer et configurer la passerelle de données locale pour Azure Analysis Services](analysis-services-gateway-install.md). Cet article est spécifique à Azure Analysis Services. Il comprend des étapes supplémentaires requises pour configurer une ressource de passerelle de données locale dans Azure et connecter votre serveur Azure Analysis Services à la ressource.

## <a name="ports-and-communication-settings"></a>Paramètres de ports et de communication

La passerelle crée une connexion sortante vers Azure Service Bus. Elle communique sur les ports sortants : TCP 443 (par défaut), 5671, 5672 et 9350 à 9354.  La passerelle ne nécessite pas de ports entrants.

Il vous faudra peut-être inclure les adresses IP pour votre région de données dans votre pare-feu. Vous pouvez télécharger la [liste d’adresses IP de centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=56519). Cette liste est actualisée chaque semaine. Les adresses IP répertoriées dans la liste d’adresses IP de centre de données Azure sont en notation CIDR. Pour plus d’informations, voir [Routage interdomaine sans classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Voici les noms de domaine complets utilisés par la passerelle.

| Noms de domaine | Ports sortants | Description |
| --- | --- | --- |
| *. powerbi.com |80 |HTTP utilisé pour télécharger le programme d’installation. |
| *. powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Permet de tester la connectivité internet si la passerelle est inaccessible par le service Power BI. |
| *.microsoftonline-p.com |443 |Utilisé pour l’authentification en fonction de la configuration. |
| dc.services.visualstudio.com  |443 |Utilisé par AppInsights pour collecter les données de télémétrie. |

### <a name="force-https"></a>Forcer les communications HTTPS avec Azure Service Bus

Vous pouvez forcer la passerelle à communiquer avec Azure Service Bus à l’aide de HTTPS au lieu de TCP direct ; toutefois, cela peut affecter considérablement les performances. Vous pouvez modifier le fichier *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* en remplaçant la valeur `AutoDetect` par `Https`. Ce fichier se trouve généralement dans *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Étapes suivantes 

Les articles suivants sont inclus dans le contenu général de la passerelle de données locale qui s’applique à tous les services pris en charge par la passerelle :

* [Questions fréquentes (FAQ) sur la passerelle de données locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Utiliser l’application de passerelle de données locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administration au niveau du locataire](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Configurer les paramètres de proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Ajuster les paramètres de communication](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Configurer des fichiers journaux](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Résolution des problèmes](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Surveiller et optimiser les performances de la passerelle](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
