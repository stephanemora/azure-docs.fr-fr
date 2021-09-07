---
title: Certification de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez-en plus sur la certification de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/14/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ade0e82131f65183ac9471bb77d0ab0cc92ae285
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219637"
---
# <a name="certification"></a>Certification

Outre la certification NetWeaver, SAP requiert une certification spéciale pour SAP HANA, afin de prendre en charge SAP HANA sur certaines infrastructures, telles qu'Azure IaaS et BareMetal Infrastructure.

La Remarque SAP principale sur NetWeaver, et dans une certaine mesure sur la certification SAP HANA, est [Remarque SAP n° 1928533 - Applications SAP sur Azure : types de machines virtuelles Azure et produits pris en charge](https://launchpad.support.sap.com/#/notes/1928533).

Vous trouvez les enregistrements de certification pour les grandes instances SAP HANA sur Azure sur le site [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

Les types SAP HANA sur Azure (grandes instances), auxquels il est fait référence sur le site Plateformes IaaS certifiées SAP HANA, fournit aux clients SAP et Microsoft la possibilité de déployer :

- Grande SAP Business Suite
- SAP BW
- S/4 HANA
- BW/4HANA
- Autres charges de travail SAP HANA dans Azure. 

La solution est basée sur le tampon matériel dédié et certifié SAP-HANA ([SAP HANA Tailored Datacenter Integration – TDI](https://scn.sap.com/docs/DOC-63140)). Si vous exécutez une solution SAP HANA TDI configurée, toutes les applications basées sur SAP HANA ci-dessus fonctionnent sur l’infrastructure matérielle.

Par rapport à l'exécution de SAP HANA dans des machines virtuelles, cette solution présente l'avantage de fournir des volumes de mémoire beaucoup plus importants. 

## <a name="key-concepts"></a>Concepts clés

Pour activer cette solution, vous devez comprendre les aspects clés suivants :

- La couche Application SAP et les applications autres que SAP s’exécutent dans des machines virtuelles hébergées dans les tampons matériels Azure habituels.
- Les déploiements d’applications, de centres de données et d’infrastructures locaux du client sont connectés à la plateforme cloud via ExpressRoute (recommandé) ou un réseau privé virtuel (VPN). Le répertoire Active Directory et le DNS sont également étendus dans Azure.
- L’instance de base de données SAP HANA pour les charges de travail HANA s’exécute sur SAP HANA sur Azure (grandes instances). Le tampon de grande instance est connecté au réseau Azure, afin que les logiciels s’exécutant dans des machines virtuelles puissent interagir avec l’instance HANA s’exécutant dans la grande instance HANA.
- Le matériel de SAP HANA sur Azure (grandes instances) est un matériel dédié fourni dans une Infrastructure as a Service (IaaS) avec SUSE Linux Enterprise Server ou Red Hat Enterprise Linux préinstallé. Comme pour les machines virtuelles, les mises à jour et la maintenance supplémentaires du système d’exploitation relèvent de votre responsabilité.
- L'installation de HANA ou de tout autre composant nécessaire à l'exécution de SAP HANA sur des unités de grande instance HANA relève de votre responsabilité. Toutes les opérations en cours respectives et l’administration de SAP HANA sur Azure sont également de votre responsabilité.
- Vous pouvez également installer d’autres composants de votre abonnement Azure qui se connectent à SAP HANA sur Azure (grandes instances). Par exemple, les composants qui permettent la communication avec la base de données SAP HANA, tels que les suivants :        
    - Serveurs de rebond
    - Serveurs RDP
    - SAP HANA Studio
    - Services SAP Data pour scénarios SAP BI
    - Solutions d’analyse du réseau
- Comme dans Azure, la grande instance HANA prend en charge des fonctionnalités de haute disponibilité et de récupération d’urgence.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les références SKU disponibles pour les grandes instances HANA

> [!div class="nextstepaction"]
> [Références (SKU) disponibles pour HLI](hana-available-skus.md)
