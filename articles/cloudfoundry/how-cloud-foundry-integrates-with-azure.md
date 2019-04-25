---
title: Comment Cloud Foundry s’intègre à Azure | Microsoft Docs
description: Décrit comment Cloud Foundry peut utiliser les services Azure pour améliorer l’expérience de l’entreprise
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 7cbffdd40e574c7e906a9388b70ca9d32fd84649
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198963"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Intégrer Cloud Foundry avec Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) est une plate-forme PaaS fonctionnant sur la plateforme IaaS des fournisseurs de cloud. Il offre une expérience de déploiement d’applications cohérente entre fournisseurs de cloud. Il peut également intégrer avec divers services Azure, avec le niveau de l’entreprise à haute disponibilité, évolutivité et réduction des coûts.
Il existe [6 sous-systèmes de Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), qui peuvent être mis à l'échelle en ligne de façon flexible, notamment : Routage, l’authentification, gestion de cycle de vie des applications, de gestion des services, la messagerie et de surveillance. Pour chaque sous-système, vous pouvez configurer Cloud Foundry pour utiliser correspondant service Azure. 

![Architecture d’intégration Cloud Foundry sur Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Haute disponibilité et extensibilité
### <a name="managed-disk"></a>Disque managé
Bosh utilise Azure CPI (Interface du fournisseur de Cloud) pour la création de disque et de suppression des routines. Par défaut, des disques non managés sont utilisés. Le client doit créer manuellement des comptes de stockage, puis configurer les comptes dans les fichiers manifestes de CF. Il s’agit en raison de la limitation du nombre de disques par compte de stockage.
[Disque managé](https://azure.microsoft.com/services/managed-disks/) offre un stockage disque sécurisé et fiable géré pour les machines virtuelles. Le client n’a plus besoin de traiter le compte de stockage en vue de la mise à l’échelle et de l’utilisation de la haute disponibilité. Azure organise automatiquement les disques. Qu’il s’agisse d’un nouveau paramètre ou un déploiement existant, l’IPC Azure gère la création ou la migration du disque géré lors d’un déploiement cloud Foundry. Il est pris en charge avec PCF 1.11. Pour en savoir plus, vous pouvez également explorer le [guide de référence de la fonctionnalité Disque managé](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) de Cloud Foundry open source. 
### <a name="availability-zone-"></a>Zone de disponibilité*
En tant que plateforme d’applications natives dans le cloud, Cloud Foundry est conçu avec [quatre niveaux de haute disponibilité](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Alors que les trois premiers niveaux de défaillances logicielles peuvent être gérés par le système CF lui-même, la tolérance aux pannes de la plateforme est assurée par les fournisseurs de cloud. Les composants CF clés doivent être protégés avec une solution de haute disponibilité de la plateforme d’un fournisseur de cloud. Cela inclut les gammes de bases de données et de services GoRouters, Diego Brains et CF. Par défaut, le [groupe à haute disponibilité Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) est utilisé pour la tolérance de panne entre clusters dans un centre de données.
La nouveauté, c’est que [Azure Availability Zone](https://docs.microsoft.com/azure/availability-zones/az-overview ) est désormais disponible, ce qui améliore significativement la tolérance de panne, avec une faible redondance de latence entre les centres de données.
Azure Availability Zone permet d’obtenir des fonctionnalités de haute disponibilité en plaçant un ensemble de machines virtuelles dans deux ou plusieurs centres de données, chaque ensemble de machines virtuelles étant redondant avec d’autres ensembles. Si une zone est défaillante, les autres ensembles restent actifs, car ils sont protégés de l’incident.
> [!NOTE] 
> Azure Availability Zone n’est pas disponible dans toutes les régions ; reportez-vous à la [liste la plus récente des régions prises en charge](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour Cloud Foundry open source, consultez les [conseils d’utilisation d’Azure Availability Zone pour Cloud Foundry open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Routage réseau
Par défaut, l’équilibreur de charge de base Azure est utilisé pour les demandes d’API/d’applications CF entrantes et il les transmet aux Gorouters. Les composants CF, tels que Diego Brain, MySQL et ERT, peuvent également utiliser l’équilibreur de charge afin d’équilibrer le trafic pour améliorer la haute disponibilité. Azure fournit également un ensemble de solutions d’équilibrage de charge entièrement gérés. Si vous avez besoin pour un arrêt TLS (« déchargement SSL ») ou par le traitement de couche d’application HTTP/HTTPS demande, considérez la passerelle d’Application. Pour un équilibrage de charge de haute disponibilité et d’évolutivité sur la couche 4, envisagez d’utiliser un équilibreur de charge standard.
### <a name="azure-application-gateway-"></a>Azure Application Gateway*
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) offre différentes fonctionnalités d’équilibrage de charge de Couche 7, notamment le déchargement SSL et SSL de bout en bout, le pare-feu d’application web, l’affinité de session basée sur les cookies, et bien plus encore. Vous pouvez [configurer Application Gateway dans Cloud Foundry open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Pour PCF, consultez les [notes de publication de PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) en vue d’un test de preuve de concept.

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer*
Azure Load Balancer est un équilibrage de charge de type Couche 4. Il est utilisé pour distribuer le trafic entre les instances de services dans un jeu d’équilibrage de charge. La version standard offre des [fonctionnalités avancées](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) au-dessus de la version de base. Par exemple 1. La limite maximale du pool principal est passée de 100 VM à 1 000 VM.  2. Les points de terminaison prennent désormais en charge plusieurs ensembles de disponibilité au lieu d’un seul.  3. Fonctionnalités supplémentaires, telles que les ports haute disponibilité, les données de surveillance plus riches et ainsi de suite. Si vous souhaitez déplacer vers la Zone de disponibilité Azure, l’équilibreur de charge standard est requis. Pour un nouveau déploiement, nous vous recommandons de démarrer avec le Standard Load Balancer d’Azure. 

## <a name="3-authentication"></a>3. Authentification 
[Le compte d’utilisateur et l’authentification Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) constituent le service de gestion d’identité central de CF et de ses différents composants. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) est le service mutualisé basé sur le cloud de Microsoft pour la gestion des annuaires et des identités. Par défaut, UAA est utilisé pour l’authentification Cloud Foundry. En tant qu’option avancée, UAA prend également en charge Azure AD en tant que magasin d’utilisateurs externe. Les utilisateurs Azure AD peuvent accéder à Cloud Foundry en utilisant leur identité LDAP sans posséder de compte Cloud Foundry. Pour [configurer AD Azure pour UAA dans PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html), suivez ces étapes.

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Stockage de données pour le système d’exécution de Cloud Foundry
En raison de ses capacités d’extensibilité importantes, Cloud Foundry permet d’utiliser le blobstore Azure ou Azure MySQL/PostgreSQL pour le stockage du système d’exécution des applications.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobstore pour le blobstore Cloud Controller de Cloud Foundry
Le blobstore Cloud Controller est un magasin de données essentiel pour les buildpacks, les droplets, les packages et les pools de ressources. Par défaut, le serveur NFS est utilisé pour le blobstore Cloud Controller. Pour éviter de créer un point de défaillance unique, utilisez le stockage d’objets Blob Azure en tant que magasin externe. Consultez la [documentation de Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) pour approfondir vos connaissances et vérifiez les [options dans Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL en tant que base de données d’exécution Elastic Cloud Foundry*
L’environnement d’exécution CF Elastic nécessite deux bases de données système principales :
#### <a name="ccdb"></a>CCDB 
Base de données Cloud Controller.  Cloud Controller fournit des points de terminaison d’API REST permettant aux clients d’accéder au système. CCDB stocke les tables pour les organisations, les espaces, les services, les rôles utilisateur et plus encore pour Cloud Controller.
#### <a name="uaadb"></a>UAADB 
Base de données pour le compte et l’authentification de l’utilisateur. Elle stocke les données relatives à l’authentification de l’utilisateur, par exemple les noms d’utilisateur et les mots de passe chiffrés.

Par défaut, une base de données système locale (MySQL) peut être utilisée. Pour la haute disponibilité et la mise à l’échelle, vous pouvez tirer parti des services MySQL ou PostgreSQL gérés par Azure. Voici l’instruction d’[activation de bases de données système Azure MySQL/PostgreSQL pour CCDB, UAADB et autres avec Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Projet Open Service Broker
Azure Service Broker offre une interface cohérente pour gérer l’accès des applications aux services Azure. Le nouveau [projet Open Service Broker pour Azure ](https://github.com/Azure/open-service-broker-azure) offre un moyen simple et unique de fournir des services aux applications par le biais de Cloud Foundry, OpenShift et Kubernetes. Reportez-vous à la [gamme de services Azure Open Service Broker pour PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) pour connaître les instructions de déploiement sur PCF.

## <a name="6-metrics-and-logging"></a>6. Mesures et journalisation
L’infrastructure Nozzle d’Azure Log Analytique est un composant de Cloud Foundry, qui envoie des mesures à partir de la [Cloud Foundry compileur de fichiers log firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) à [Azure Monitor enregistre](https://azure.microsoft.com/services/log-analytics/). Avec l’infrastructure Nozzle, vous pouvez collecter, consulter et analyser l’intégrité et les indicateurs de performance de Cloud Foundry, et ce, sur plusieurs déploiements.
Cliquez sur [ici](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) pour apprendre à déployer l’infrastructure Nozzle d’Azure Log Analytique Open Source et environnement Pivotal Cloud Foundry et ensuite accéder aux données à partir d’Azure Monitor enregistre la console. 
> [!NOTE]
> PCF 2.0, les mesures de contrôle d’intégrité BOSH pour les machines virtuelles sont réacheminés vers Loggregator Firehose par défaut et sont intégrés dans la console de journaux Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Réduction des coûts
### <a name="cost-saving-for-devtest-environments"></a>Réduction des coûts pour les environnements de Dev/Test
#### <a name="b-series-"></a>Série B : *
Alors que des VM de séries F et D étaient généralement recommandées pour l’environnement de production Pivotal Cloud Foundry, la nouvelle [série B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) « modulable » offre de nouvelles options. Machines virtuelles modulables série B sont idéales pour les charges de travail qui ne doit les performances complètes du processeur en continu, comme les serveurs web, développement et petites bases de données et les environnements de test. Ces charges de travail ont généralement des exigences modulables en termes de performances. Le tarif est de 0,012 $/heure (B1) comparé à 0,05 $/heure (F1) ; pour plus d’informations, consultez la liste complète des [tailles de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) et des [prix](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). 
#### <a name="managed-standard-disk"></a>Disque managé Standard : 
Les disques Premium sont recommandés pour la fiabilité de leurs performances en production.  Avec [Disque managé](https://azure.microsoft.com/services/managed-disks/), le stockage standard peut également offrir une fiabilité similaire mais avec des performances différentes. Pour les charges de travail qui ne sont pas sensibles aux performances, telles que l’environnement non critiques ou de développement/Test, des disques gérés standards offrent une alternative à moindre coût.  
### <a name="cost-saving-in-general"></a>Généralités sur la réduction des coûts 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Réduction significative des coûts des machines virtuelles grâce aux réservations Azure : 
Aujourd’hui, toutes les machines virtuelles CF sont facturées sur la base de la tarification « à la demande » même si les environnements restent généralement connectés indéfiniment. Vous pouvez désormais réserver la capacité de VM sur une durée de 1 ou 3 ans et bénéficier de réductions de 45 à 65 %. Les remises sont appliquées dans le système de facturation, sans modification de votre environnement. Pour plus d’informations, voir [Fonctionnement des réservations Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Disque Premium géré avec tailles inférieures : 
Les disques managés prennent en charge des tailles de disque plus petites, par exemple P4 (32 Go) et P6 (64 Go) pour les disques premium et standard. Si vous avez de petites charges de travail, vous pouvez faire des économies de coûts lors de la migration de disques premium standard vers des disques premium gérés.
#### <a name="use-azure-first-party-services"></a>Utilisez Azure premiers Services de tiers : 
Bénéficiez non seulement de la haute disponibilité et la fiabilité décrites ci-avant mais aussi du service de base Azure afin de réduire les coûts d’administration à long terme. 

Pivotal a lancé un [ERT compact](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) pour les clients PCF, les composants étant situés dans seulement 4 machines virtuelles pouvant exécuter jusqu’à 2 500 instances d’application. La version d’évaluation est désormais disponible sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Étapes suivantes
Fonctionnalités d’intégration d’Azure sont tout d’abord disponibles avec [Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), avant qu’il soit disponible sur Pivotal Cloud Foundry. Les fonctionnalités marquées d’un astérisque ne sont toujours pas disponibles via PCF. Cloud Foundry intégration avec Azure Stack n’est pas abordée dans ce document soit.
Pour connaître l’état actuel du support PCF sur les fonctionnalités marquées d’un astérisque (*) ou de l’intégration de Cloud Foundry dans Azure Stack, contactez votre gestionnaire de compte Pivotal et Microsoft. 

