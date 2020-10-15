---
title: Options de supervision - HSM Azure dédié| Microsoft Docs
description: Vue d’ensemble des options de supervision et responsabilités de supervision des HSM Azure dédiés
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "70881420"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Supervision du HSM dédié

Le service HSM dédié d’Azure fournit un appareil physique à usage exclusif du client, avec un contrôle administratif complet et une responsabilité de la gestion. L’appareil mis à disposition est un [modèle Gemalto SafeNet Luna 7 HSM A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Une fois l’approvisionnement effectué par un client, Microsoft n’aura aucun accès d’administration au-delà d’un attachement de port sériel physique dans un rôle de surveillance. Par conséquent, les clients sont responsables des activités opérationnelles typiques, notamment de la supervision complète et de l’analyse des journaux.
Ils sont entièrement responsables des applications qui utilisent les HSM et doivent utiliser Gemalto pour bénéficier d’un support ou d’une assistance de conseil . En raison de l’étendue de la propriété du client en matière d’hygiène opérationnelle, Microsoft ne peut offrir aucun type de garantie de haute disponibilité pour ce service. Il est la responsabilité du client de s’assurer que ses applications sont correctement configurées pour atteindre une haute disponibilité. Microsoft surveille l’intégrité de l’appareil et la connectivité réseau et assure leur maintenance.

## <a name="microsoft-monitoring"></a>Supervision Microsoft

L’appareil Gemalto SafeNet en cours d’utilisation a par défaut SNMP et un port série comme options pour la supervision de l’appareil. Microsoft a utilisé la connexion de port série comme physique signifie pour se connecter à l’appareil et récupérer les données de télémétrie base sur l’intégrité de l’appareil. Cela inclut des éléments tels que la température et l’état de composants tels que les blocs d’alimentation et les ventilateurs.
Pour ce faire, Microsoft utilise un rôle de « superviseur » non administratif pour l’appareil Gemalto. Ce rôle permet de récupérer la télémétrie, mais ne donne pas accès à l’appareil pour effectuer des tâches d’administration ou afficher d’autre manière des informations de chiffrement. Nos clients peuvent être certains qu’il leur incombe véritablement de gérer, d’administrer et d’utiliser leur appareil pour stocker des clés de chiffrement sensible. Si ces clients ne sont pas satisfaits de cet accès minimal pour la supervision de l’intégrité de base, ils peuvent désactiver le compte de supervision. La conséquence évidente est que Microsoft n’aura pas d’informations, donc ne pourra pas fournir de notifications proactives concernant des problèmes d’intégrité de l’appareil. Dans ce cas, le client est responsable de l’intégrité de l’appareil.
La fonction de supervision proprement dite est configurée pour interroger l’appareil toutes les 10 minutes afin d’obtenir des données d’intégrité. En raison de la tendance des communications série à engendrer des erreurs, une alerte ne sera déclenchée qu’après plusieurs indicateurs d’intégrité négatifs en une heure. Cette alerte aboutira finalement à une communication proactive informant le client du problème.
Selon la nature du problème, des mesures adéquates sont prises pour en réduire l’impact et garantir qu’il soit réglé sans prendre de risques. Par exemple, la défaillance d’un bloc d’alimentation nécessite une procédure de remplacement à chaud sans altération, qui peut donc être effectuée avec peu d’impact et un risque minimal pour le fonctionnement. D’autres procédures peuvent nécessiter la remise à zéro et le déprovisionnement d’un appareil pour minimiser les risques de sécurité du client. Dans ce cas, un client provisionne un autre appareil et rejoint un couplage de haute disponibilité, ce qui déclenche la synchronisation de l’appareil. Le fonctionnement normal reprend rapidement, avec une interruption minimale et un risque extrêmement faible en matière de sécurité.  

## <a name="customer-monitoring"></a>Supervision des clients

Une proposition de valeur du service HSM dédié est le contrôle exercé par le client sur l’appareil, surtout considérant qu’il s’agit d’un appareil livré dans le cloud. Une conséquence de ce contrôle est la responsabilité de superviser et de gérer l’intégrité de l’appareil. L’appareil Gemalto SafeNet est fourni avec des conseils pour l’implémentation de SNMP et de Syslog. Il est recommandé aux clients du service HSM dédié de les utiliser même lorsque le compte de supervision Microsoft reste actif, et il doit être considéré comme obligatoire s’ils désactivent le compte de supervision Microsoft.
Ces techniques disponibles permettent à un client d’identifier les problèmes et d’appeler le support Microsoft pour y remédier avec des mesures appropriées.

## <a name="next-steps"></a>Étapes suivantes

Il est recommandé de bien comprendre tous les concepts clés du service, comme la haute disponibilité et la sécurité, par exemple, avant le provisionnement des appareils et la conception ou le déploiement des applications. Quelques rubriques conceptuelles supplémentaires :

* [Haute disponibilité](high-availability.md)
* [Sécurité physique](physical-security.md)
* [Mise en réseau](networking.md)
* [Prise en charge](supportability.md)
