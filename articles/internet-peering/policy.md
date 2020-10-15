---
title: Stratégie de Peering Microsoft
titleSuffix: Azure
description: Stratégie de Peering Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75773948"
---
# <a name="peering-policy"></a>Stratégie de peering
Les exigences générales de Microsoft concernant votre réseau sont décrites dans les sections ci-dessous. Elles s’appliquent à la fois aux requêtes de Peering direct et de Peering Exchange.

## <a name="technical-requirements"></a>Exigences techniques

* Un réseau entièrement redondant avec une capacité suffisante pour échanger du trafic sans congestion.
* L’homologue aura un numéro de système autonome (NSA) routable publiquement.
* Les protocoles IPv4 et IPv6 sont pris en charge, et Microsoft s’attend à établir des sessions des deux types dans chaque emplacement de Peering.
* MD5 n’est pas pris en charge.
* **Détails concernant les NSA :**
    * Microsoft gère AS8075 avec les NSA suivants : AS8068, AS8069, AS12076. Pour obtenir la liste complète des NSA avec Peering AS8075, consultez AS-SET MICROSOFT.
    * Toutes les parties qui établissent un Peering avec Microsoft s’engagent à n’accepter en aucune circonstance les routes à partir de AS12076 (Express Route), et doivent filtrer AS12076 sur tous les homologues.
* **Stratégie de routage :**
    * L’homologue aura au moins un bloc /24 routable public.
    * Microsoft remplacera les MED (Multi-Exit Discriminators) reçus.
    * Microsoft préfère recevoir des balises de communauté BGP en provenance des homologues pour indiquer l’origine de la route.
    * L’homologue est censé inscrire ses routes dans une base de données de routage Internet (IRR, Internet Routing Registry) publique, à des fins de filtrage, et fera tout son possible pour tenir ces informations à jour.
    * Nous suggérons aux homologues de définir un max-prefix de 1000 (IPv4) et 100 (IPv6) routes sur les sessions de Peering avec Microsoft.
    * Sauf accord spécifique préalable, les homologues sont censés annoncer des routes cohérentes dans tous les emplacements où ils établissent un Peering avec Microsoft.
    * En général, les sessions de Peering avec AS8075 publieront toutes les routes AS-MICROSOFT. Les interconnexions AS8075 en Afrique et en Asie peuvent être limitées aux routes pertinentes pour la région concernée.
    * Aucune des parties n’établira une route statique, une route de dernier recours, ni n’enverra le trafic à l’autre partie pour une route non annoncée par le biais du protocole BGP.
    * Les homologues sont censés adhérer aux normes du secteur [MANRS](https://www.manrs.org/) pour la sécurité des routes.

## <a name="operational-requirements"></a>Exigences opérationnelles
* Un centre d’opérations réseau 24x7, capable d’aider à la résolution de tous les problèmes techniques et de performances, les violations de sécurité, les attaques par déni de service ou tout autre abus provenant de l’homologue ou de ses clients.
* Les homologues sont censés disposer d’un profil complet et à jour sur [PeeringDB](https://www.peeringdb.com), y compris un e-mail NOC 24x7 du domaine d’entreprise et un numéro de téléphone. Nous utilisons ces informations pour valider les détails de l’homologue, tels que les informations NOC, les informations de contact technique, leur présence dans les installations de Peering, et ainsi de suite. Les comptes personnels Yahoo, Gmail et Hotmail ne sont pas acceptés.

## <a name="physical-connection-requirements"></a>Exigences relatives aux connexions physiques
* Les emplacements où vous pouvez vous connecter à Microsoft pour le Peering direct ou le Peering Exchange sont listés dans [PeeringDB](https://www.peeringdb.com/net/694).
* **Peering Exchange :**
    * L’interconnexion doit être établie sur une fibre monomode utilisant l’optique 10Gbps appropriée.
    * Les homologues sont censés mettre à niveau leurs ports quand le pic d’utilisation dépasse 50 %.
* **Peering direct :**
    * L’interconnexion doit être établie sur une fibre monomode utilisant l’optique 10Gbps ou 100Gbps appropriée.
    * Microsoft n’établira un Peering direct qu’avec des fournisseurs de services Internet (ISP) ou des fournisseurs de services réseau.
    * Les homologues sont censés mettre à niveau leurs ports quand le pic d’utilisation dépasse 50 %, et maintenir une capacité diversifiée dans chaque métro, soit dans un même emplacement, soit parmi plusieurs emplacements dans un métro.
    * Chaque Peering direct se compose de deux connexions à deux routeurs Microsoft Edge à partir des routeurs de l’homologue situés dans la périphérie de l’homologue. Microsoft exige des sessions BGP doubles sur ces connexions. L’homologue peut choisir de ne pas déployer d’appareils redondants à son extrémité.

## <a name="traffic-requirements"></a>Exigences relatives au trafic
* Les homologues sur le Peering Exchange doivent avoir au moins 200 Mo de trafic, et au plus 2 Go.  Pour le trafic qui dépasse 2 Go, le Peering direct doit être passé en revue.
* Pour le Peering direct, le trafic allant de votre réseau vers Microsoft doit respecter les exigences minimales requises suivantes.

    | Zone géographique                      | Trafic minimal vers Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afrique                   | 500 Mbits/s                       |
    | APAC (sauf l’Inde)      |   2 Gbit/s                       |
    | APAC (Inde uniquement)        | 500 Mbits/s                       |
    | Europe                   |   2 Gbit/s                       |
    | AMLAT                    |   2 Gbit/s                       |
    | Moyen-Orient              | 500 Mbits/s                       |
    | N/D                       |   2 Gbit/s                       |

* **Diversité :**
    * Dans les zones N/A, Europe, APAC et AMLAT, interconnectez dans au moins trois emplacements géographiquement différents si cela est faisable, et maintenir une capacité diversifiée afin de permettre au trafic de basculer au sein de chaque métro.
    * En Afrique, au Moyen-Orient et en Inde, interconnectez dans autant de sites différents que possible. Vous devez maintenir une capacité diversifiée suffisante pour garantir que le trafic reste dans la région.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les étapes de configuration du Peering direct avec Microsoft, suivez la [procédure pas à pas de configuration du Peering direct](walkthrough-direct-all.md).
* Pour plus d’informations sur les étapes de configuration d’un Peering Exchange avec Microsoft, suivez la [procédure pas à pas de configuration d’un Peering Exchange](walkthrough-exchange-all.md).