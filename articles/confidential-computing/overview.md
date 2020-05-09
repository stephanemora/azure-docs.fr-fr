---
title: Vue d’ensemble de l’informatique confidentielle Azure
description: Vue d’ensemble de l’informatique confidentielle Azure (ACC, Azure Confidential Computing)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: ae98325d98df1ac8a06e0c0bc950d89cc6b77eda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82192261"
---
# <a name="confidential-computing-on-azure"></a>Informatique confidentielle dans Azure

L’informatique confidentielle Azure vous permet d’isoler vos données sensibles pendant qu’elles sont traitées dans le cloud. De nombreux secteurs utilisent l’informatique confidentielle pour protéger leurs données. Voici quelques charges de travail :

- Sécurisation des données financières
- Protection des informations relatives aux patients
- Exécution de processus Machine Learning sur des informations sensibles
- Exécution d’algorithmes sur des jeux de données chiffrés provenant de plusieurs sources


## <a name="overview"></a>Vue d’ensemble
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Nous savons que la sécurisation de vos données cloud est importante. Nous sommes conscients de vos préoccupations. Voici quelques questions que nos clients peuvent se poser lors de la migration de charges de travail sensibles vers le cloud : 

- Comment m’assurer que Microsoft ne peut pas accéder aux données qui ne sont pas chiffrées ?
- Comment éviter les menaces de sécurité provenant d’administrateurs privilégiés dans mon entreprise ?
- Par quels autres moyens est-ce que je peux empêcher des tiers d’accéder aux données sensibles des clients ?

Microsoft Azure vous aide à réduire la surface d’attaque pour renforcer la protection de vos données. Azure propose déjà de nombreux outils pour protéger les [**données au repos**](../security/fundamentals/encryption-atrest.md), par le biais de modèles tels que le chiffrement côté client et le chiffrement côté serveur. Azure permet également de chiffrer les [**données en transit**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) au moyen de mécanismes reposant sur des protocoles sécurisés tels que TLS et HTTPS. Cette page introduit une troisième forme de chiffrement, à savoir le chiffrement des **données en cours d’utilisation**.


## <a name="introduction-to-confidential-computing"></a>Présentation de l’informatique confidentielle <a id="intro to acc"></a>

L’informatique confidentielle est un terme du [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC), une fondation dédiée à la définition de l’informatique confidentielle et à l’accélération de son adoption. L’informatique confidentielle est la protection des données en cours d’utilisation lors de calculs. Les calculs sont effectués dans un environnement d’exécution approuvé (TEE, Trusted Execution Environment) basé sur le matériel.

Un TEE est un environnement dans lequel seul le code autorisé est exécuté. Aucune donnée dans le TEE ne peut être lue ou falsifiée par du code en dehors de cet environnement.

### <a name="enclaves-and-trusted-execution-environments"></a>Enclaves et environnements d’exécution approuvés

Dans le cadre de l’informatique confidentielle, les TEE sont communément appelés *enclaves* ou *enclaves sécurisées*. Les enclaves sont les parties sécurisées du processeur et de la mémoire d’un matériel. Il n’existe aucun moyen de consulter les données ou le code à l’intérieur de l’enclave, même avec un débogueur. Si du code non fiable tente de modifier le contenu dans la mémoire de l’enclave, l’environnement est désactivé et les opérations sont refusées.

Quand vous développez des applications, vous pouvez utiliser des [outils logiciels](#oe-sdk) pour protéger des parties de votre code et de vos données dans l’enclave. Ces outils garantissent qu’aucune personne extérieure à l’environnement approuvé ne peut consulter ou modifier votre code et vos données. 

Une enclave est similaire à une boîte noire. Vous placez le code et les données chiffrés dans la boîte noire. De l’extérieur, vous ne voyez rien. Vous donnez à l’enclave une clé pour déchiffrer les données, puis les données sont traitées et rechiffrées avant d’être envoyées hors de l’enclave.

### <a name="attestation"></a>Attestation

Vous devez vérifier et valider que votre environnement approuvé est sécurisé. Cette vérification est le processus d’attestation. 

L’attestation permet à une partie de confiance d’avoir davantage confiance dans le fait (1) que son logiciel fonctionne dans une enclave et (2) que l’enclave est à jour et sécurisée. Par exemple, une enclave demande au matériel sous-jacent de générer des informations d’identification qui incluent la preuve que l’enclave existe sur la plateforme. Le rapport peut ensuite être remis à une deuxième enclave qui vérifie que le rapport a été généré sur la même plateforme.

L’attestation doit être implémentée à l’aide d’un service d’attestation sécurisé qui est compatible avec le logiciel système et le silicium. Les [services d’attestation et de provisionnement d’Intel](https://software.intel.com/sgx/attestation-services) sont compatibles avec les machines virtuelles d’informatique confidentielle Azure.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Utilisation d’Azure pour l’informatique confidentielle basée sur le cloud <a id="cc-on-azure"></a>

L’informatique confidentielle Azure vous permet de tirer parti des avantages de l’informatique confidentielle dans un environnement virtualisé. Vous pouvez désormais utiliser des outils, des logiciels et une infrastructure cloud pour créer des applications sur du matériel sécurisé. 

### <a name="virtual-machines"></a>Virtual Machines

Azure est le premier fournisseur de cloud à proposer l’informatique confidentielle dans un environnement virtualisé. Nous avons développé des machines virtuelles qui agissent comme une couche d’abstraction entre le matériel et votre application. Vous pouvez exécuter des charges de travail à grande échelle et avec des options de redondance et de disponibilité.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Machines virtuelles compatibles avec Intel SGX

Dans les machines virtuelles d’informatique confidentielle Azure, une partie du matériel du processeur est réservée à une partie du code et des données de votre application. Cette partie restreinte est l’enclave. 

![Modèle de machine virtuelle](media/overview/hardware-backed-enclave.png)

L’infrastructure d’informatique confidentielle Azure se compose actuellement d’une référence SKU spécialisée de machines virtuelles. Ces machines virtuelles s’exécutent sur des processeurs Intel avec Software Guard Extension (Intel SGX). [Intel SGX](https://intel.com/sgx) est le composant qui nous permet de renforcer la protection pour mettre en œuvre l’informatique confidentielle. 

Aujourd’hui, Azure propose la [série DCsv2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) qui exploite la technologie Intel SGX pour permettre la création d’enclaves basées sur le matériel. Vous pouvez donc créer des applications basées sur des enclaves sécurisées et les exécuter dans la série DCsv2 de machines virtuelles pour protéger les données et le code de votre application en cours d’utilisation. 

Pour plus d’informations sur le déploiement de machines virtuelles d’informatique confidentielle Azure avec des enclaves approuvées basées sur le matériel, [consultez cette page](virtual-machine-solutions.md).

## <a name="application-development"></a>Développement d’applications <a id="application-development"></a>

Pour tirer parti de la puissance des enclaves et des environnements isolés, vous devez utiliser des outils qui prennent en charge l’informatique confidentielle. Différents outils prennent en charge le développement d’applications enclaves. Par exemple, vous pouvez utiliser ces frameworks open source : 

- [SDK Open Enclave](https://github.com/openenclave/openenclave)
- [Confidential Consortium Framework (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Vue d’ensemble

Une application créée avec des enclaves est partitionnée de deux façons :
1. Un composant « non approuvé » (l’hôte)
1. Un composant « approuvé » (l’enclave)

L’**hôte** est votre application enclave s’exécutant sur un environnement non approuvé. Le code dans l’hôte n’a pas accès au code chargé dans l’enclave. 

L’**enclave** est l’endroit où le code et les données s’exécutent dans l’implémentation du TEE. Les calculs sécurisés doivent avoir lieu dans l’enclave pour garantir la protection des secrets et des données sensibles. 

Quand vous commencez à développer une application enclave, vous devez déterminer le code et les données à protéger. Le code que vous choisissez de placer dans le composant approuvé est isolé du reste de votre application. Une fois l’enclave initialisée et le code chargé en mémoire, ce code ne peut être ni lu ni modifié en dehors de l’environnement protégé.

### <a name="open-enclave-software-development-kit-oe-sdk"></a>SDK Open Enclave (SDK OE) <a id="oe-sdk"></a>

Utilisez une bibliothèque ou un framework pris en charge par votre fournisseur si vous souhaitez écrire du code qui s’exécute dans une enclave. Le [SDK Open Enclave](https://github.com/openenclave/openenclave) (SDK OE) est un SDK open source qui permet de créer une couche d’abstraction sur différents matériels prenant en charge l’informatique confidentielle. 

Le SDK OE est conçu pour servir de couche d’abstraction unique sur n’importe quel matériel sur n’importe quel fournisseur de solutions cloud. Le SDK OE peut être utilisé sur des machines virtuelles d’informatique confidentielle Azure pour créer et exécuter des applications sur des enclaves.

## <a name="next-steps"></a>Étapes suivantes

Déployez une machine virtuelle de la série DCsv2 et installez le SDK OE sur celle-ci.

> [!div class="nextstepaction"]
> [Déployez une machine virtuelle d’informatique confidentielle dans la Place de marché Azure](quick-create-marketplace.md)