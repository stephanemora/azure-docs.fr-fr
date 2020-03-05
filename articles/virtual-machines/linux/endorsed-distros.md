---
title: Distributions Linux approuvées sur Azure
description: Découvrez les distributions Linux approuvées sur Azure, notamment des instructions pour Ubuntu, CentOS, Oracle et SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: mimckitt
ms.openlocfilehash: 7318095dce77856087ad7dd949e91e0eb7936294
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251641"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Distributions Linux approuvées sur Azure
Les partenaires fournissent des images Linux dans la Place de marché Microsoft Azure. Nous collaborons avec différentes communautés Linux pour étoffer la liste des distributions approuvée. En attendant, pour les distributions qui ne sont pas disponibles sur la Place de marché, vous pouvez toujours apporter votre propre distribution Linux en suivant les instructions fournies dans [Créer et charger un disque dur virtuel contenant le système d’exploitation Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distributions et versions prises en charge
Le tableau suivant répertorie les distributions et les version Linux prises en charge sur Microsoft Azure. Reportez-vous à la section [Prise en charge des images Linux dans Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) pour plus d’informations sur la prise en charge de Linux et de la technologie open source dans Azure.

Les pilotes LIS (Linux Integration Services) pour Hyper-V et Azure sont des modules noyau que Microsoft distribue directement au noyau Linux en amont.  Certains pilotes LIS sont intégrés dans le noyau de la distribution par défaut. D’anciennes distributions basées sur Red Hat Enterprise (RHEL)/CentOS sont disponibles sous la forme de téléchargements séparés à partir de la page [Linux Integration Services Version 4.2 for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106) (Services d’intégration Linux version 4.2 pour Hyper-V et Azure). Pour plus d’informations sur les pilotes LIS, voir [Conditions requises pour le noyau Linux](create-upload-generic.md#linux-kernel-requirements).

L’agent Linux Azure est pré-installé sur les images disponibles sur la Place de marché Microsoft Azure, et est généralement disponible dans le référentiel du package de la distribution. Le code source est disponible sur [GitHub](https://github.com/azure/walinuxagent).


| Distribution | Version | Pilotes | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 et versions ultérieures, 7.0 et versions ultérieures, 8.0 et versions ultérieures |CentOS 6.3 : [Téléchargement de LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+ : Dans le noyau |Package : dans le [dépôt](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) sous « WALinuxAgent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Dans le noyau |Code source : [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 et versions ultérieures, 8.2 et versions ultérieures, 9 et 10 |Dans le noyau |Package : dans le dépôt sous « waagent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Dans le noyau |Package : dans le dépôt sous « WALinuxAgent » <br/>Code source : [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+, 8.0+ |Dans le noyau |Package : dans le dépôt sous « WALinuxAgent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES pour SAP<br>11 SP4<br>12 SP1+<br>15|Dans le noyau |Package :<p> pour 11 dans le référentiel [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools)<br>pour 12 inclus dans le module « Cloud public » sous « python-azure-agent »<br/>Code source : [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| OpenSUSE |openSUSE Leap 42.2+ |Dans le noyau |Package : dans le dépôt [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) sous « python-azure-agent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |Dans le noyau |Package : dans le dépôt sous « walinuxagent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Pour plus d’informations sur la prise en charge étendue pour Ubuntu 12.04 et 14.04, consultez : [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Cadence de mise à jour d’image
Azure nécessite des éditeurs des distributions Linux approuvées qu’ils mettent régulièrement à jour leurs images dans la Place de marché Azure avec les derniers correctifs et résolution de sécurité, au moins une fois par trimestre. Les images mises à jour dans la Place de marché Azure sont disponibles automatiquement pour les clients en tant que nouvelles versions d’une référence d’image. Plus d’informations sur la recherche d’images Linux : [Rechercher des images de machine virtuelle Linux sur la Place de marché Azure](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Liens supplémentaires
 - [Cycle de vie d’une image de cloud public SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Kernels optimisés pour Azure

Azure travaille en collaboration étroite avec diverses distributions Linux approuvées afin d’optimiser les images qu’elles publient sur la Place de marché Azure. L’un des aspects de cette collaboration se traduit par le développement de kernels Linux « optimisés » pour la plateforme Azure et proposés comme composants entièrement pris en charge de la distribution Linux. Les kernels optimisés pour Azure intègrent de nouvelles améliorations en matière de fonctionnalités et de performances, et de façon plus régulière (une fois par trimestre) comparé à des kernels génériques ou par défaut disponibles.

Dans la plupart des cas, vous trouverez ces kernels préinstallés sur les images par défaut sur la Place de marché Azure, de sorte que les clients Azure profitent immédiatement des avantages qu’ils procurent. Pour en savoir plus sur ces kernels optimisés pour Azure, suivez ces liens :

 - Kernel optimisé pour Azure CentOS - Disponible via le SIG de virtualisation CentOS - [En savoir plus](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Kernel de cloud Debian - Disponible avec les images « backports » Debian 10 et Debian 9 sur Azure - [En savoir plus](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - Kernel optimisé pour Azure SLES - [En savoir plus](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Kernel optimisé pour Azure Ubuntu - [En savoir plus](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partenaires

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

À partir du site web CoreOS :

*CoreOS est conçu pour la sécurité, la cohérence et la fiabilité. Au lieu d’installer des packages via yum ou apt, CoreOS utilise des conteneurs Linux pour gérer vos services à un niveau supérieur d’abstraction. Un code de service unique et l’ensemble des dépendances sont déployés au sein d’un conteneur pouvant être exécuté sur une ou plusieurs machines CoreOS.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ est une société de conseil et de services indépendante, spécialisée dans le développement et l’implémentation de solutions professionnelles à l’aide de logiciels libres. En tant de leader spécialiste de l’Open Source, Credativ a acquis une reconnaissance internationale et de nombreux services informatiques font appel à ses services. En collaboration avec Microsoft, Credativ prépare actuellement des images Debian correspondantes pour Debian 8 (Jessie) et Debian avant 7 (Wheezy). Les deux images sont spécialement conçues pour s’exécuter sur Azure et peuvent être facilement gérées via la plateforme. Credativ prendra également en charge la maintenance et la mise à jour sur le long terme des images Debian pour Azure grâce à ses centres de support Open Source.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La stratégie d’Oracle est d’offrir un vaste portefeuille de solutions pour les clouds privés et publics. La stratégie offre aux clients de choix et la flexibilité leur permettant de décider comment déployer les logiciels Oracle dans des clouds Oracle et autres. À la faveur du partenariat conclu entre Oracle et Microsoft, les clients peuvent déployer les logiciels Oracle dans les clouds publics et privés Microsoft tout en étant assurés de bénéficier de la certification et du support d'Oracle.  L'engagement et l'investissement d'Oracle vis-à-vis des solutions de cloud public et privé Oracle sont intacts.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Le leader mondial de solutions open source, Red Hat permet à plus de 90 % des sociétés du classement Fortune 500 de résoudre les défis commerciaux, d’aligner leur stratégies informatique et commerciale, et d’anticiper l’évolution des technologies. Red Hat y parvient en proposant des solutions sécurisées grâce à un modèle commercial ouvert et à un modèle d’abonnement abordable et prévisible.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une fiabilité et un niveau de sécurité supérieurs pour le cloud computing. Polyvalente, la plateforme Linux de SUSE s'intègre en toute transparence aux services cloud Azure pour fournir un environnement cloud facile à gérer. Avec plus de 9 200 applications certifiées issues de plus de 1 800 éditeurs de logiciels indépendants pour SUSE Linux Enterprise Server, SUSE est l’assurance que les charges de travail prises en charge dans les centres de données peuvent être déployées en toute confiance sur Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

L’ingénierie de Canonical et le mode de gouvernance de la communauté Open Source sont les éléments moteurs de la réussite d’Ubuntu dans les environnements client, serveur et cloud, y compris les services cloud personnels à destination du grand public. La vision de Canonical d’une plateforme unifiée gratuite disponible dans Ubuntu, offre une gamme d’interfaces cohérentes pour téléphones, tablettes, TV et ordinateurs de bureau. Cette vision fait d’Ubuntu le premier choix pour divers établissements, des fournisseurs de cloud public aux fabricant de produits électroniques de grande consommation, et l’un des choix de prédilection des technologues individuels.

Avec des développeurs et des centres d’ingénierie présents dans le monde entier, Canonical est particulièrement bien placé pour collaborer avec les fabricants de matériel, les fournisseurs de contenu et les développeurs de logiciels pour mettre sur le marché des solutions Ubuntu destinées tant aux PC, qu’aux serveurs ou aux appareils portables.
