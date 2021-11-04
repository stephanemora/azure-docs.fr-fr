---
title: Plug-in Azure Container Service pour machines virtuelles confidentielles
description: Comment utiliser le plug-in d’appareil Intel SGX et des jeux de démons Intel SGX Quote Helper pour machines virtuelles confidentielles avec Azure Kubernetes Service.
author: agowdamsft
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: article
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f4a00e1c70427c6b467a52694340e10109b5f4f5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098163"
---
# <a name="confidential-computing-plugin-for-confidential-vms"></a>Plug-in de calcul confidentiel pour machines virtuelles confidentielles

Azure Kubernetes service (AKS) fournit un plug-in pour machines virtuelles de calcul confidentiel Azure. Le plug-in, `confcom`, est un ensemble de démons. Il s’exécute uniquement pour des machines virtuelles confidentielles Intel Software Guard Extensions (SGX) dans un cluster AKS. Ce plug-in est inscrit au niveau du cluster AKS. Vous pouvez l’utiliser pour gérer facilement des nœuds confidentiels. [Activez le plug-in sur votre cluster AKS](./confidential-enclave-nodes-aks-get-started.md) avant de commencer.

## <a name="intel-sgx-device-plugin-for-aks"></a>Plug-in d’appareil Intel SGX pour AKS

Le plug-in d’appareil SGX implémente l’interface de plug-in d’appareil Kubernetes pour la mémoire EPC (Enclave Page Cache). En effet, ce plug-in convertit la mémoire EPC en un autre type de ressource dans Kubernetes. Les utilisateurs peuvent spécifier des limites sur EPC comme sur d’autres ressources. Outre la fonction de planification, le plug-in d’appareil facilite l’attribution d’autorisations de pilote d’appareil SGX aux conteneurs de charges de travail confidentiels. [Un exemple d’implémentation de déploiement basé sur la mémoire EPC](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) est disponible.

## <a name="psm-with-sgx-quote-helper"></a>PSM avec SGX Quote Helper

Les applications d’enclave qui effectuent une attestation à distance doivent générer une Quote. La Quote fournit une preuve chiffrée de l’identité et de l’état de l’application, ainsi que de l’environnement hôte de l’enclave. La génération de Quote repose sur certains composants logiciels approuvés d’Intel, qui font partie des composants logiciels de la plateforme SGX (PSW/DCAP). Ce PSW est inclus sous la forme d’un ensemble de démons (un DaemonSet) qui s’exécute sur chaque nœud. Vous pouvez utiliser le PSW lors de la demande de Quote d’attestation à partir d’applications d’enclave. L’utilisation du service fourni par AKS permet de mieux assurer la compatibilité entre le PSW et d’autres composants SW de l’hôte. Lisez les détails de la fonctionnalité ci-dessous.

Les [applications d’enclave](confidential-computing-enclaves.md) qui effectuent une attestation à distance ont besoin d’une Quote générée. Cette Quote fournit une preuve chiffrée de l’identité, de l’état et de l’environnement d’exécution de l’application. La génération nécessite des composants logiciels approuvés qui font partie du PSW d’Intel.

### <a name="overview"></a>Vue d’ensemble

> [!NOTE]
> Cette fonctionnalité n’est requise que pour des machines virtuelles DCsv2/DCsv3 qui utilisent du matériel Intel SGX spécialisé. 
 
Intel prend en charge deux modes d’attestation pour la génération de Quote : Pour savoir quel type choisir, consultez les [différences de type d’attestation](#attestation-type-differences).

- **intraprocessus (in-proc)**  : les composants logiciels de confiance sont hébergés au sein du processus d’application de l’enclave.

- **hors processus (out-of-proc)**  : les composants logiciels de confiance sont hébergés en dehors de l’application de l’enclave.
 
Les applications SGX créées avec le SDK Open Enclave utilisent par défaut le mode d’attestation intraprocessus. Les applications basées sur SGX autorisent une attestation hors processus et nécessitent un hébergement supplémentaire. Ces applications exposent les composants requis, tels que AESM (Architectural Enclave Service Manager), externes à l’application.

Il est fortement recommandé d’utiliser cette fonctionnalité. Cette fonctionnalité augmente de temps d’activité de vos applications d’enclave lors des mises à jour de plateforme Intel ou de pilote DCAP.

### <a name="attestation-type-differences"></a>Différences de type d’attestation

Aucune mise à jour n’est nécessaire pour les composants PSW de génération de déclaration pour chaque application conteneurisée.

Avec le mode hors processus, les propriétaires de conteneur n’ont pas besoin de gérer les mises à jour dans leur conteneur. Les propriétaires de conteneurs s’appuient plutôt sur l’interface fournie qui appelle le service centralisé en dehors du conteneur. Le fournisseur met à jour et gère ce service.

Pour les attestations hors processus, il n’y a pas de problème de défaillances résultant de composants PSW obsolètes. La génération de QUOTE fait appel aux composants logiciels de confiance QE (Quoting Enclave) et PCE (Provisioning Certificate Enclave), qui font partie de la base TCB (Trusted Computing Base). Ces composants logiciels doivent être à jour pour respecter les exigences d’attestation. Le fournisseur gère les mises à jour de ces composants. Les clients ne sont jamais confronté à des défaillances d’attestation résultant de composants SW approuvés obsolètes au sein de leur conteneur.

Une attestation hors processus utilise mieux la mémoire EPC. En mode d’attestation dans le processus, chaque application d’enclave instancie la copie de QE et de PCE pour l’attestation à distance. Avec une attestation hors processus, le conteneur n’héberge pas ces enclaves et ne consomme pas de mémoire d’enclave du quota du conteneur.

Il existe également des protections contre l’application du mode noyau. Quand le pilote SGX est diffusé en continu dans le noyau Linux, une enclave dispose d’un privilège plus élevé. Ce privilège permet à l’enclave d’appeler PCE, ce qui interrompt l’exécution de l’application d’enclave en mode dans le processus. Par défaut, les enclaves n’obtiennent pas cette autorisation. L’attribution de ce privilège à une application enclavée nécessite de modifier le processus d’installation de l’application. Le fournisseur du service qui gère les demandes hors processus vérifie que le service est installé avec ce privilège.

Vous n’avez pas besoin de vérifier la compatibilité descendante avec PSW et DCAP. Le fournisseur valide les mises à jour des composants de génération de Quote de PSW pour la compatibilité descendante. Cette étape gère les problèmes de compatibilité initiaux, et les résout avant de déployer des mises à jour pour des charges de travail confidentielles.

### <a name="out-of-proc-attestation-for-confidential-workloads"></a>Attestation hors processus pour des charges de travail confidentielles

Le modèle d’attestation hors processus fonctionne pour les charges de travail confidentielles. Le demandeur de Quote et la génération de Quote s’exécutent séparément, mais sur la même machine physique. La génération de Quote se produit de manière centralisée et sert les demandes de Quotes en provenance de toutes les entités. Définissez correctement l’interface et rendez-la détectable par une entité pour demander des Quotes.

![Diagramme de demandeur de Quote et d’interface de génération de Quote.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Le modèle abstrait s’applique aux scénarios de charge de travail confidentielle. Ce modèle utilise le service AESM déjà disponible. AESM est conteneurisé et déployé en tant que démon défini sur le cluster Kubernetes. Kubernetes garantit qu’une seule instance d’un conteneur de service AESM, incluse dans un pod, est déployée sur chaque nœud d’agent. Le nouvel ensemble de démons SGX Quote a une dépendance sur l’ensemble de démons `sgx-device-plugin`, car le conteneur de services AESM aura besoin de mémoire de `sgx-device-plugin` pour lancer des enclaves QE et PCE.

Chaque conteneur doit accepter d’utiliser la génération de Quote hors processus en définissant la variable d’environnement `SGX_AESM_ADDR=1` au moment de la création. Le conteneur doit également inclure le package `libsgx-quote-ex` qui dirige la demande vers le socket de domaine Unix par défaut

Une application peut continuer à utiliser l’attestation dans le processus comme avant. Toutefois, vous ne pouvez pas utiliser simultanément des attestations dans le processus et hors processus au sein d’une application. L’infrastructure hors processus est disponible par défaut et consomme des ressources.

### <a name="sample-implementation"></a>Implémentation d'exemple

Le fichier docker ci-dessous est un exemple d’application basée sur Open Enclave. Définissez la variable d’environnement `SGX_AESM_ADDR=1` dans le fichier Docker. Ou définissez la variable dans le fichier de déploiement. Pour plus d’informations sur le fichier Docker et le fichier YAML de déploiement, suivez cet exemple. 

> [!Note] 
> Pour que l’attestation hors processus fonctionne correctement, le package **libsgx-quote-ex** d’Intel doit être empaqueté dans le conteneur d’applications.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
Au lieu de cela, définissez le mode d’attestation hors processus dans le fichier YAML de déploiement comme suit :

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer des nœuds confidentiels (de série DCsv2/DCsv3) sur AKS](./confidential-enclave-nodes-aks-get-started.md)
- [Exemples de démarrages rapides pour conteneurs confidentiels](https://github.com/Azure-Samples/confidential-container-samples)
- [Liste des références SKU DCsv2](../virtual-machines/dcv2-series.md)
- [Liste des références SKU DCSv3](../virtual-machines/dcv3-series.md)
- [Azure Attestation](../attestation/index.yml)
- [Machines virtuelles confidentielles Intel SGX sur Azure](/confidential-computing/virtual-machine-solutions-sgx.md)
