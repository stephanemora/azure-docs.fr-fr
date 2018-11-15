---
title: Gestionnaire de sécurité Azure IoT Edge | Microsoft Docs
description: Gère le principe de sécurité d’appareil IoT Edge et l’intégrité des services de sécurité.
services: iot-edge
keywords: sécurité, élément sécurisé, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6231f50134f7dcfd29e8754841847beb69570204
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567687"
---
# <a name="azure-iot-edge-security-manager"></a>Gestionnaire de sécurité Azure IoT Edge

Le Gestionnaire de sécurité Azure IoT Edge est un cœur de sécurité bien délimité de protection de l’appareil IoT Edge et tous ses composants en faisant abstraction du matériel de silicium de sécurité. Il est le point focal du renforcement de la sécurité et fournit un point d’intégration de la technologie aux fabricants d’appareil d’origine (OEM).

![Gestionnaire de sécurité Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Le Gestionnaire de sécurité IoT Edge vise à protéger l’intégrité de l’appareil IoT Edge et toutes les opérations de logiciel inhérentes.  Il le fait en convertissant une approbation à partir de la racine matérielle sous-jacent du matériel de confiance (si disponible) pour démarrer le runtime IoT Edge en toute sécurité et de continuer à surveiller l’intégrité de ses opérations.  En bref, le Gestionnaire de sécurité IoT Edge comprend des logiciels qui fonctionnent conjointement avec le matériel de silicium sécurisé lorsqu’il est disponible et activé pour aider à offrir les garanties de sécurité les plus élevées possible.  

Les responsabilités du Gestionnaire de sécurité IoT Edge incluent (liste non exhaustive) :

* Démarrage sécurisé et mesuré de l’appareil Azure IoT Edge.
* Approvisionnement de l’identité de l’appareil et la transition de confiance, le cas échéant.
* Héberger et protéger les composants de l’appareil de services cloud comme Service Device Provisioning.
* Configurer en toute sécurité des modules IoT Edge avec une identité unique du module.
* Opérateur de contrôle à la racine matérielle d’appareil de confiance via les services de notaire.
* Surveiller l’intégrité des opérations IoT Edge lors de l’exécution.

Gestionnaire de sécurité IoT Edge comprend trois composants principaux :

* Démon de sécurité IoT Edge.
* Abstraction calque de la plateforme du module de sécurité matérielle (PAL HSM).
* Racine matérielle de silicium de confiance facultative mais fortement recommandée ou HSM.

## <a name="the-iot-edge-security-daemon"></a>Démon de sécurité IoT Edge

Démon de sécurité IoT Edge est le logiciel chargé pour les opérations logiques de gestionnaire de sécurité IoT Edge.  Il comprend une partie significative de la base informatique approuvée de l’appareil IoT Edge. 

### <a name="design-principles"></a>Principes de conception

Pour effectuer de manière adéquate et pratique ses responsabilités, le démon de sécurité IoT Edge vise à respecter les principes de bases suivants :

#### <a name="maximize-operational-integrity"></a>Optimiser l’intégrité opérationnelle

Le démon de sécurité IoT Edge est conçu pour fonctionner avec l’intégrité la plus élevée possible au sein de la fonctionnalité de défense de n’importe quelle racine donnée du matériel de confiance. Avec l’intégration appropriée, la racine du matériel de confiance mesure et surveille le démon de sécurité statiquement et lors de l’exécution pour résister aux falsifications.

L’accès physique constitue toujours une menace pour les appareils IoT en général.  Par conséquent, la racine matérielle de confiance joue un rôle important dans la protection de l’intégrité du démon de sécurité IoT Edge.  La racine matérielle de confiance se présente sous deux versions :

* éléments sécurisés pour la protection des informations sensibles telles que les secrets et clés de chiffrement.
* enclaves sécurisées pour la protection des secrets tels que les clés et des charges de travail sensibles telles que les métriques et les facturations.

L’existence de ces deux modèles d’utilisation de racine matérielle de confiance donne lieu à deux types d’environnements d’exécution :

* L’environnement d’exécution standard ou riche (EER) qui s’appuie sur l’utilisation d’éléments sécurisés pour protéger les informations sensibles.
* L’environnement d’exécution approuvé (EEA) qui s’appuie sur l’utilisation de la technologie d’enclave sécurisée pour protéger les informations sensibles et offre une protection à l’exécution de logiciels.

Pour les appareils qui utilisent des enclaves sécurisées en tant que racine matérielle de confiance, logique sensible au sein de démon de sécurité IoT Edge est censée résider au sein de l’enclave.  Les parties non sensibles du démon de sécurité peuvent résider en dehors de la TEE.  Dans tous les cas, les fabricants de design d’origine (FDO) et les fabricants de matériel d’origine (FMO) sont censés élargir la confiance à partir de leur HSM à mesurer et à défendre l’intégrité du démon de sécurité Edge au démarrage et à l’exécution.

#### <a name="minimize-bloat-and-churn"></a>Réduire le ballonnement et l’évolution

Un autre principe de base pour le démon de sécurité Edge consiste à réduire l’évolution.  Pour le niveau le plus élevé de confiance, le démon de sécurité IoT Edge peut étroitement s’associer à la racine matérielle de confiance de l’appareil lorsqu’il est disponible et fonctionne comme un code natif.  Il est courant pour ces types de réalisations de mettre à jour le démon via le chemin d’accès de mise à jour sécurisée de la racine matérielle de confiance (par opposition à des mécanismes de mise à jour du système d’exploitation fourni), ce qui peut s’avérer difficile selon le matériel spécifique et le scénario de déploiement.  Tandis que le renouvellement de la sécurité est une forte recommandation pour les appareils IoT, il est évident que les exigences de mise à jour excessives ou charges utiles de mise à jour importantes peuvent développer la surface d’attaque de nombreuses façons.  Ignorer des mises à jour pour optimiser la disponibilité opérationnelle ou la racine du matériel de confiance trop limitée pour traiter des misse à jour de charges utiles importantes.  Par conséquent, la conception du démon de sécurité IoT Edge est concise pour conserver l’encombrement et donc, la petite base informatique approuvée, et pour réduire les exigences de mise à jour.

### <a name="architecture-of-iot-edge-security-daemon"></a>L’architecture du démon de sécurité IoT Edge

![Démon de sécurité Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Le démon de sécurité IoT Edge est conçu pour tirer parti de n’importe quelle racine matérielle disponible de confiance de la technologie pour le renforcement de la sécurité.  Plus par conséquent, il est conçu pour permettre l’opération de fractionnement entre un environnement d’exécution Standard/riche (EER) et un environnement d’exécution approuvé (EEA) pour tirer parti des technologies de matériel qui offrent des environnements d’exécution approuvée (EEA).  Le cœur de l’architecture du démon de sécurité IoT Edge est composé d’interfaces spécifiques à un rôle pour permettre l’interaction entre les composants principaux de Edge afin d’assurer l’intégrité de l’appareil IoT Edge et de ses opérations.

#### <a name="cloud-interface"></a>Interface Cloud

L’interface cloud permet au démon de sécurité IoT Edge d’accéder aux services cloud tels que les compliments cloud et la sécurité des appareils, comme le renouvellement de la sécurité.  Par exemple, le démon de sécurité IoT Edge utilise actuellement cette interface pour accéder à Azure IoT Hub [Service d’approvisionnement des appareils (SAA)](https://docs.microsoft.com/azure/iot-dps/) pour la gestion du cycle de vie des identités d’appareil.  

#### <a name="management-api"></a>API de gestion

Le démon de sécurité IoT Edge offre une API de gestion, qui est appelée par l’Agent Edge lors de la création/démarrage/arrêt/suppression d’un module edge. Le démon de sécurité IoT Edge stocke « inscriptions » pour tous les modules actifs. Ces inscriptions mappent les identités d’un module à certaines propriétés du module. L’identificateur de processus (pid) du processus en cours d’exécution dans le conteneur ou le hachage du contenu du conteneur de docker constituent deux exemples pour ces propriétés.

Elles sont utilisées par l’API de charge de travail (décrite ci-dessous) pour attester que l’appelant est autorisé à effectuer une action.

L’API de gestion est une API privilégiée, pouvant être appelée uniquement à partir de l’agent IoT Edge.  Étant donné que le démon de sécurité IoT Edge amorce et démarre l’agent IoT Edge, il peut créer une inscription implicite pour l’agent IoT Edge, une fois qu’il a été attesté que l’agent IoT Edge n’a pas été falsifié. Le même processus d’attestation qui utilise des API de la charge de travail est utilisé pour restreindre l’accès à l’API de gestion pour uniquement l’agent IoT Edge.

#### <a name="container-api"></a>API de conteneur

Le démon de sécurité IoT Edge offre l’interface du conteneur pour interagir avec le système du conteneur en cours d’utilisation, tel que Moby et Docker pour l’instanciation du module.

#### <a name="workload-api"></a>API de charge de travail

L’API de charge de travail est un démon API de sécurité IoT Edge accessible à tous les modules, y compris l’agent IoT Edge. Il fournit la preuve d’identité, sous la forme d’un module HSM jeton rooté signé ou un certificat X509 et un offre groupée correspondante à un module. L’offre groupée de confiance contient les certificats d’autorité de certification pour tous les autres serveurs que les modules doivent approuver.

Le démon de sécurité IoT Edge utilise un processus d’attestation pour protéger cette API. Lorsqu’un module appelle cette API, le démon de sécurité IoT Edge tente de trouver une inscription pour l’identité. En cas de réussite, il utilise les propriétés de l’inscription pour mesurer le module. Si le résultat du processus de mesure correspond à l’inscription, un nouveau jeton HSM connecté et signé ou un certificat X509 est généré et les certificats d’autorité de certification correspondants (offre groupée approuvée) sont retournés au module.  Le module utilise ce certificat pour vous connecter à IoT Hub, les autres modules, ou démarrer un serveur. Lorsque la date d’expiration du jeton signé ou du certificat approche, il est à la charge du module à demander un nouveau certificat.  Des considérations supplémentaires sont intégrées à la conception pour rendre le processus de renouvellement d’identité aussi transparente que possible.

### <a name="integration-and-maintenance"></a>Intégration et maintenance

Microsoft tient à jour le code principal pour le [démon de sécurité IoT Edge sur GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installation et mises à jour

Étant un processus natif, l’installation et les mises à jour du démon de sécurité IoT Edge sont gérées via le système de gestion des packages du système d’exploitation.  Toutefois, il est probable que les appareils IoT Edge avec racine matérielle de confiance fournissent des renforcement supplémentaires à l’intégrité du démon grâce à la gestion de son cycle de vie via les appareils sécurisée et les mises à jour de systèmes de gestion.  Il revient aux fabricants d’appareils d’explorer ces voies conformément à leurs fonctionnalités respectives d’appareil.

#### <a name="versioning"></a>Contrôle de version

Le runtime IoT Edge effectue le suivi et signale la version du démon de sécurité IoT Edge. La version est signalée comme l’attribut *runtime.platform.version* de la propriété rapportée du module d’Agent Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Abstraction calque de la plateforme du module de sécurité matériel (PAL HSM)

La publication de HSM PAL extrait toutes les racines du matériel de confiance pour isoler le développeur ou l’utilisateur de IoT Edge de leur complexité.  Il comprend une combinaison de l’interface de programmation d’applications (API) et des transactions de procédures de communications de domaine, par exemple la communication entre un environnement d’exécution standard et une enclave sécurisée.  L’implémentation réelle de la PAL HSM varie selon le matériel sécurisé en cours d’utilisation.  Son existence permet d’utiliser n’importe quel matériel de silicium sécurisé au sein de l’écosystème IoT.

## <a name="secure-silicon-root-of-trust-hardware"></a>Racine de silicium sécurisé du matériel de confiance

Le silicium sécurisé est nécessaire à l’approbation d’ancrage dans le matériel de l’appareil IoT Edge.  Un silicium sécurisé se présente en plusieurs variétés pour inclure le Module de plateforme sécurisée (TPM), l’élément sécurisé incorporé (eSE), ARM TrustZone, Intel SGX et des technologies de silicium sécurisé personnalisé.  L’utilisation de racine de silicium de confiance sur les appareils est hautement recommandée étant donné les menaces associées à l’accessibilité physique des appareils IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Maintenance et intégration de gestionnaire de sécurité IoT Edge

Un objectif majeur du Gestionnaire de sécurité IoT Edge consiste à identifier et isoler les composants occupant la tâche de défendre la sécurité et l’intégrité de la plateforme Azure IoT Edge pour un renforcement personnalisé.  Par conséquent il est attendu des parties tierces, telles que les fabricants d’appareils de recourir à des fonctionnalités de sécurité personnalisées disponibles avec leur matériel de l’appareil.  Consultez la section étapes suivantes ci-dessous sur les liens vers un exemple sur la manière de renforcer le Gestionnaire de sécurité Azure IoT avec le Module de plateforme sécurisée (TPM) sur les plateformes Linux et Windows.  Ces exemples utilisent des logiciels ou des modules de plateforme sécurisée virtuels mais s’appliquent directement à l’utilisation des appareils TPM discrets.  

## <a name="next-steps"></a>Étapes suivantes

Lire le blog sur [Sécuriser la périphérie intelligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Créer et provisionner un appareil Edge à l’aide d’un [TPM virtuel sur une machine virtuelle Linux](how-to-auto-provision-simulated-device-linux.md).

Créer et provisionner un [appareil Edge avec TPM simulé sur Windows](how-to-auto-provision-simulated-device-windows.md).

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/
