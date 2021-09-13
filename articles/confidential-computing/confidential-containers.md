---
title: Conteneurs confidentiels sur Azure Kubernetes Service (AKS)
description: En savoir plus sur la prise en charge des conteneurs non modifiés sur des conteneurs confidentiels.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.subservice: confidential-computing
ms.openlocfilehash: 7b225fa68b9a280ef4a1c7145ab691b82168202a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967409"
---
# <a name="confidential-containers"></a>Conteneurs confidentiels

## <a name="overview"></a>Vue d’ensemble

Permettez aux développeurs d’importer une **application Docker (nouvelle ou existante)** et de l’exécuter en toute sécurité sur Azure Kubernetes Service (AKS) via la prise en charge de nœuds d’informatique confidentielle.

Les conteneurs confidentiels apportent une protection dans les domaines suivants :

- intégrité des données 
- confidentialité des données
- intégrité du code
- protection du code du conteneur par chiffrement
- assurances basées sur le matériel
- autorisation de l’exécution d’applications existantes
- création de racine matérielle de confiance
- suppression de l’administrateur de l’hôte, de l’administrateur Kubernetes, de l’hyperviseur de la limite d’approbation

Un environnement d’exécution de confiance (Trusted Execution Environment, TEE) basé sur du matériel est un composant important utilisé pour fournir des assurances fortes via des mesures matérielles et logicielles de composants TCB (Trusted Computing Base). Les vérifications de ces mesures facilitent la validation du calcul attendu, ainsi que le contrôle de l’absence de toute altération des applications de conteneur.

Les conteneurs confidentiels prennent en charge les applications personnalisées développées en **Python, Java, Node JS, etc. ou des applications conteneur empaquetées telles que NGINX, Cache Redis, MemCache**, etc. qui doivent être exécutées sans modification sur AKS avec des nœuds de calcul confidentiels.

Les conteneurs confidentiels sont la voie la plus rapide vers la confidentialité des conteneurs. Ils nécessitent uniquement le réempaquetage des applications conteneur Docker existantes. Ils ne nécessitent pas de modifications du code de l’application. Les conteneurs confidentiels sont des applications conteneur Docker qui sont empaquetées pour s’exécuter dans un environnement TEE. Certains activateurs de conteneurs confidentiels offrent également le chiffrement du conteneur qui peut aider à protéger le code du conteneur pendant le stockage et le transport, ainsi que quand il est monté sur l’hôte. Le chiffrement du conteneur vous permet d’aller plus loin et de protéger le code/modèle empaqueté dans le conteneur avec sa clé de déchiffrement jointe au TEE.

Voici le processus pour les conteneurs confidentiels depuis le développement jusqu’au déploiement ![Processus pour les conteneurs confidentiels.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Activateurs de conteneur confidentiel
Pour exécuter un conteneur Docker existant non modifié, vous devez disposer d’un logiciel SGX afin que les appels d’application puissent utiliser le jeu d’instructions de processeur spécial rendu disponible pour réduire la surface d’exposition de l’attachement et ne pas dépendre du système d’exploitation invité. Une fois encapsulés avec le logiciel de runtime SGX, les conteneurs se lancent automatiquement dans les enclaves protégées, supprimant ainsi le système d’exploitation invité, le système d’exploitation hôte ou l’hyperviseur de la limite d’approbation. Cette exécution isolée dans un nœud (machine virtuelle) avec le chiffrement des données en mémoire soutenu par le matériel réduit les zones d’attaque globale et les vulnérabilités des couches de système d’exploitation ou d’hyperviseur.

Les conteneurs confidentiels sont entièrement pris en charge sur AKS, et activés via des partenaires Azure et des projets de logiciels open source (OSS). Les développeurs peuvent choisir des fournisseurs de logiciels en fonction des fonctionnalités, de l’intégration aux services Azure et de la prise en charge des outils.

## <a name="partner-enablers"></a>Activateurs de partenaires
> [!NOTE]
> Les solutions ci-dessous sont proposées via des partenaires Azure et peuvent occasionner des frais de licence. Veuillez vérifier les conditions d’utilisation des logiciels de partenaires de façon indépendante. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) fournit un logiciel de plateforme SGX qui vous permet d’exécuter des conteneurs non modifiés sur AKS. Pour plus d’informations sur les fonctionnalités, consultez les exemples d’applications [ici](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Commencez avec un exemple de Cache Redis et d’application personnalisée Python [ici](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

![Processus Anjuna](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) offre aux développeurs la possibilité de choisir un portail et une expérience basée sur une interface de ligne de commande pour importer leurs applications en conteneur et les convertir en conteneurs confidentiels compatibles SGX sans avoir à modifier ou à recompiler l’application. Fortanix offre la flexibilité nécessaire pour exécuter et gérer l’ensemble le plus vaste d’applications, à savoir des applications existantes, de nouvelles applications natives de l’enclave et des applications prépackagées. Les utilisateurs peuvent commencer avec l’interface utilisateur du [Confidential Computing Manager](https://em.fortanix.com/) ou des [API REST](https://www.fortanix.com/api/em/) pour créer des conteneurs confidentiels en suivant le guide de [Démarrage rapide](https://fortanix.com/blog/2020/10/fortanix-confidential-containers-on-microsoft-azure-kubernetes-service-aks/) pour Azure Kubernetes Service.

![Processus de déploiement de Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[SCONE](https://scontain.com/index.html?lang=en) prend en charge les stratégies de sécurité pouvant générer des certificats, des clés et des secrets, et veille à ce que ceux-ci ne soient visibles que par des services attestés d’une application. Ainsi, les services d’une application s’attestent mutuellement de façon automatique via le protocole TLS, sans qu’il soit nécessaire de modifier les applications ou le protocole TLS. Ceci est expliqué avec l’aide d’une simple application Flask : https://sconedocs.github.io/flask_demo/  

SCONE peut convertir la plupart des fichiers binaires existants en applications qui s’exécutent à l’intérieur d’enclaves, sans nécessité de modifier ou de recompiler ces applications. SCONE protège également les langages interprétés tels que Python, en **chiffrant** à la fois les fichiers de données et les fichiers de code Python. Avec l’aide d’une stratégie de sécurité SCONE, vous pouvez protéger les fichiers chiffrés contre les accès, les modifications et les restaurations non autorisés. La procédure de « sconification » d’une application Python existante est décrite [ici](https://sconedocs.github.io/sconify_image/).

![Flux scontain](./media/confidential-containers/scone-workflow.png)

Les déploiements de Scone sur des nœuds de calcul confidentiels avec AKS sont entièrement pris en charge et intégrés avec d’autres services Azure. Commencez avec un exemple d’application ici https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>Activateurs OSS 
> [!NOTE]
> Les solutions ci-dessous sont proposées via des projets open source et ne sont pas directement affiliées à Azure Confidential Computing (ACC) ou à Microsoft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) est un système d’exploitation invité léger, conçu pour exécuter une application Linux unique avec des exigences minimales en matière d’hébergement. Graphene peut exécuter des applications dans un environnement isolé, avec des avantages comparables à l’exécution d’un système d’exploitation complet, et offre une bonne prise en charge des outils pour la conversion d’une application de conteneur Docker existante en conteneurs Graphene dotés d’une protection maximale (SGC).

Commencez avec un exemple d’application et un déploiement sur AKS [ici](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) est un système d’exploitation de bibliothèque (LibOS) multiprocessus, à mémoire sécurisée, pour Intel SGX. Il permet aux applications héritées de s’exécuter sur SGX sans modification, ou presque, du code source. Occlum protège en toute transparence la confidentialité des charges de travail utilisateur, tout en permettant une migration lift-and-shift aisée vers des applications Docker existantes.

Occlum prend en charge les déploiements AKS. Suivez les instructions de déploiement avec divers exemples d’applications [ici](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md).

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) est un framework d’orchestration pour les conteneurs confidentiels. Il simplifie l’exécution et la mise à l’échelle des services confidentiels sur Kubernetes compatible SGX. Marblerun assure des tâches réutilisables telles que la vérification des services dans votre cluster, la gestion des secrets pour ces services et l’établissement de connexions mTLS d’enclave à enclave entre eux. Marblerun garantit également que votre cluster de conteneurs confidentiels respecte un manifeste défini en code JSON simple. Le manifeste peut être vérifié par des clients externes par le biais d’une attestation distante. 

![Flux Marblerun](./media/confidential-containers/marblerun-workflow.png)

En résumé, Marblerun étend les propriétés de confidentialité, d’intégrité et de vérifiabilité d’une seule enclave à un cluster Kubernetes. 

Marblerun prend en charge les conteneurs confidentiels créés avec Graphene, Occlum et EGo. Vous trouverez [ici](https://docs.edgeless.systems/marblerun/#/examples?id=examples) des exemples pour chaque SDK. Marblerun est conçu pour s’exécuter sur Kubernetes, parallèlement à vos outils natifs cloud existants. Il offre une interface de ligne de commande simple à utiliser et des graphiques Helm. Il assure une excellente prise en charge des nœuds de calcul confidentiels sur AKS. Vous trouverez [ici](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment) des informations sur le déploiement de Marblerun sur AKS.

## <a name="confidential-containers-demo"></a>Démonstration des conteneurs confidentiels
Regardez la démonstration relative à la confidentialité des soins de santé avec des conteneurs confidentiels. L’exemple est disponible [ici](/azure/architecture/example-scenario/confidential/healthcare-inference). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Contactez-vous

Vous avez des questions concernant votre implémentation ou souhaitez devenir un activateur ? Envoyer un e-mail à l’équipe produit **acconaks@microsoft.com**

## <a name="reference-links"></a>Liens de référence

[Microsoft Azure Attestation](../attestation/overview.md)

[Machines virtuelles DCsv2](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
