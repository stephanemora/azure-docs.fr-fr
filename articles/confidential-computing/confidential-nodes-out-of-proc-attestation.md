---
title: Prise en charge de l’attestation avec le DaemonSet Intel SGX Quote Helper sur Azure (préversion)
description: Un DaemonSet pour générer la déclaration (quote) en dehors du processus de l’application Intel SGX. Cet article explique comment la fonctionnalité d’attestation hors processus est mise en œuvre pour les charges de travail confidentielles qui s’exécutent dans un conteneur.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484402"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Gestion des logiciels de plateforme avec le DaemonSet Intel SGX Quote Helper (préversion)

Les [applications d’enclave](confidential-computing-enclaves.md) qui effectuent une attestation à distance ont besoin d’une déclaration générée. Cette déclaration fournit une preuve de chiffrement de l’identité et de l’état de l’application ainsi que de l’environnement où l’enclave s’exécute. La génération de la déclaration nécessite des composants logiciels approuvés qui font partie des composants logiciels de la plateforme Intel (PSW).

## <a name="overview"></a>Vue d’ensemble
 
Intel prend en charge deux modes d’attestation dans le cadre de la génération de la QUOTE :

- *In-process* : les composants logiciels approuvés sont hébergés au sein du processus de l’application d’enclave.

- *Hors processus* : les composants logiciels approuvés sont hébergés en dehors de l’application d’enclave.
 
Les applications Intel Software Guard Extension (Intel SGX) créées avec le SDK Open Enclave utilisent par défaut le mode d’attestation in-process. Les applications basées sur Intel SGX autorisent le mode d’attestation hors processus. Si vous voulez utiliser ce mode, vous avez besoin d’un hébergement supplémentaire et vous devez exposer les composants nécessaires, comme AESM (Architectural Enclave Service Manager), à l’extérieur de l’application.

Cette fonctionnalité augmente la durée de bon fonctionnement de vos applications d’enclave lors des mises à jour de la plateforme Intel ou du pilote DCAP. C’est pourquoi nous vous recommandons de l’utiliser.

Pour activer cette fonctionnalité sur un cluster Azure Kubernetes Services (AKS), ajoutez la commande `--enable-sgxquotehelper` à Azure CLI quand vous activez le module complémentaire d’informatique confidentielle. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Pour plus d’informations, consultez [Démarrage rapide : Déployer un cluster AKS avec des nœuds d’informatique confidentielle en utilisant Azure CLI](confidential-nodes-aks-get-started.md).

## <a name="benefits-of-the-out-of-process-mode"></a>Avantages du mode hors processus

La liste suivante décrit certains des principaux avantages de ce mode d’attestation :

-   Aucune mise à jour n’est nécessaire pour les composants PSW de génération de déclaration pour chaque application conteneurisée. Les propriétaires de conteneur n’ont pas besoin de gérer les mises à jour dans leur conteneur. À la place, ils s’appuient sur l’interface du fournisseur qui appelle le service centralisé en dehors du conteneur. Le fournisseur met à jour et gère le conteneur.

-   Vous n’avez pas à vous soucier des échecs d’attestation provoqués par des composants PSW obsolètes. Le fournisseur gère les mises à jour de ces composants.

-   Le mode hors processus offre une meilleure utilisation de la mémoire EPC que le mode in-process. Dans le mode in-process, chaque application d’enclave doit instancier la copie des composants QE et PCE pour l’attestation à distance. Avec le mode hors processus, le conteneur n’a pas besoin d’héberger ces enclaves et il ne consomme donc pas la mémoire d’enclave du quota du conteneur.

-   Quand vous chargez le pilote Intel SGX dans un noyau Linux, il est nécessaire pour une enclave de disposer d’une privilège plus élevé. Ce privilège permet à l’enclave d’appeler le composant PCE, ce qui va entraîner l’arrêt de l’application d’enclave exécutée en mode in-process. Par défaut, les enclaves n’obtiennent pas cette autorisation. L’attribution de ce privilège à une application enclavée nécessite de modifier le processus d’installation de l’application. En revanche, dans le mode hors processus, le fournisseur du service qui gère les demandes hors processus vérifie que le service est installé avec ce privilège.

-   Vous n’avez pas besoin de vérifier la compatibilité descendante avec PSW et DCAP. Les mises à jour des composants PSW de génération de QUOTE sont validées pour la compatibilité descendante par le fournisseur avant leur application. Ceci vous permet de gérer les problèmes de compatibilité avant de déployer des mises à jour pour des charges de travail confidentielles.

## <a name="confidential-workloads"></a>Charges de travail confidentielles

Le demandeur de déclaration et la génération de déclaration s’exécutent séparément, mais sur la même machine physique. La génération de déclaration est centralisée et répond aux demandes de déclaration de toutes les entités. Pour qu’une entité puisse demander des déclarations, l’interface doit être correctement définie et découvrable.

![Diagramme montrant les relations entre le demandeur de déclaration, la génération de déclaration et l’interface.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Ce modèle abstrait s’applique au scénario des charges de travail confidentielles, en tirant parti du service AESM déjà disponible. AESM est conteneurisé et déployé en tant que DaemonSet dans le cluster Kubernetes. Kubernetes garantit qu’une seule instance d’un conteneur de service AESM, incluse dans un pod, est déployée sur chaque nœud d’agent. Le nouveau DaemonSet SGX Quote va avoir une dépendance vis-à-vis du DaemonSet sgx-device-plugin, car le conteneur du service AESM demande de la mémoire EPC auprès du sgx-device-plugin pour lancer les enclaves QE et PCE.

Chaque conteneur doit choisir explicitement d’utiliser la génération de déclaration hors processus en définissant la variable d’environnement `SGX_AESM_ADDR=1` au moment de la création. Le conteneur doit également inclure le package libsgx-quote-ex, qui est chargé de rediriger la demande vers le socket du domaine Unix par défaut.

Une application peut néanmoins toujours utiliser l’attestation in-process comme auparavant, mais elle ne peut pas utiliser simultanément l’attestation in-process et l’attestation hors processus. L’infrastructure hors processus est disponible par défaut et consomme des ressources.

## <a name="sample-implementation"></a>Implémentation d'exemple

Le fichier Docker suivant est un exemple d’application basée sur Open Enclave. Définissez la variable d’environnement `SGX_AESM_ADDR=1` dans le fichier Docker ou définissez-la dans le fichier de déploiement. L’exemple suivant fournit des détails sur le fichier Docker et le déploiement. 

  > [!Note] 
  > Pour que l’attestation hors processus fonctionne correctement, libsgx-quote-ex d’Intel doit être empaqueté dans le conteneur d’application.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
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
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
Vous pouvez aussi définir le mode d’attestation hors processus dans le fichier de déploiement .yaml. Voici comment faire :

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

[Démarrage rapide : Déployer un cluster AKS avec des nœuds d’informatique confidentielle à l’aide d’Azure CLI](./confidential-nodes-aks-get-started.md)

[Démarrer rapidement avec des exemples de conteneurs confidentiels](https://github.com/Azure-Samples/confidential-container-samples)

[Références SKU DCsv2](../virtual-machines/dcv2-series.md)
