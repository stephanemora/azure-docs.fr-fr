---
title: Résoudre les problèmes de votre déploiement Kubernetes sur Azure Stack | Microsoft Docs
description: Découvrez comment résoudre les problèmes de votre déploiement Kubernetes sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: f7f23a6d645a1d8e16e42e751050d8d91b49e2b3
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007823"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Résoudre les problèmes de votre déploiement Kubernetes sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!Note]  
> Kubernetes sur Azure Stack est en préversion.

L’article suivant traite de la résolution des problèmes rencontrés avec votre cluster Kubernetes. Vous pouvez consulter l’alerte de déploiement et examiner l’état de votre déploiement par le biais des éléments requis pour ce déploiement. Vous devrez peut-être collecter les journaux de déploiement à partir du système Azure Stack ou des machines virtuelles Linux qui hébergent Kubernetes. Vous pouvez également être amené à travailler avec votre administrateur Azure Stack pour récupérer des journaux à partir d’un point de terminaison d’administration.

## <a name="overview-of-deployment"></a>Vue d’ensemble du déploiement

Avant de commencer à résoudre des problèmes sur votre cluster, vous souhaitez peut-être revoir rapidement les principes du déploiement d’un cluster Kubernetes Azure Stack. Ce déploiement utilise un modèle de solution Azure Resource Manager pour créer les machines virtuelles, puis installe le moteur ACS de votre cluster.

### <a name="deployment-workflow"></a>Workflow du déploiement

Le schéma suivant illustre le processus général de déploiement du cluster.

![Processus de déploiement Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Étapes du déploiement

1. Collecter des paramètres d’entrée à partir de l’élément Place de marché.

    Entrer les valeurs dont vous avez besoin pour configurer le cluster Kubernetes, y compris :
    -  **Nom d’utilisateur** : le nom d’utilisateur des machines virtuelles Linux qui font partie du cluster Kubernetes et du déploiement de machines virtuelles (DVM).
    -  **Clé publique SSH** : la clé utilisée pour l’autorisation sur toutes les machines Linux créées dans le cadre du cluster Kubernetes et du DVM.
    -  **Principal du service**: l’ID utilisé par le fournisseur cloud d’Azure Kubernetes. Il s’agit de l’ID client identifié en tant qu’ID d’application lorsque vous avez créé votre principal de service. 
    -  **Secret client** : la clé que vous avez créée au moment de la création de votre principal de service.

2. Créer la machine virtuelle de déploiement et l’extension de script personnalisé.
    -  Créer les machines virtuelles Linux de déploiement au moyen de l’image Linux de la Place de marché, **Ubuntu Server 16. 04-LTS**.
    -  Télécharger et exécuter l’extension de script client à partir de la Place de marché. Le script est **Script personnalisé pour Linux 2.0**.
    -  Exécuter le script personnalisé DVM. Le script effectue les tâches suivantes :
        1. Obtient le point de terminaison de la galerie à partir du point de terminaison de métadonnées Azure Resource Manager.
        2. Obtient l’ID de ressource Active Directory à partir du point de terminaison des métadonnées Azure Resource Manager.
        3. Charge le modèle d’API pour le moteur ACS.
        4. Déploie le moteur ACS sur le cluster Kubernetes et enregistre le profil cloud Azure Stack sur `/etc/kubernetes/azurestackcloud.json`.
3. Créer les machines virtuelles principales.

4. Télécharger et exécuter des extensions de script client.

5. Exécuter le script principal.

    Le script effectue les tâches suivantes :
    - Installe les ressources etcd, Docker et Kubernetes, telles que kubelet. etcd est un magasin de valeurs de clés distribuées qui offre un moyen de stocker des données sur un cluster de machines. Docker prend en charge des virtualisations extrêmement simples au niveau du système d’exploitation, et connues sous l’appellation de conteneurs. Kubelet est l’agent de nœud qui s’exécute sur chaque nœud Kubernetes.
    - Configure le service etcd.
    - Configure le service kubelet.
    - Démarre Kubelet. Cette tâche implique les étapes suivantes :
        1. Démarrer le service de l’API.
        2. Démarrer le service du contrôleur.
        3. Démarrer le service du planificateur.
6. Créer des machines virtuelles d’agents.

7. Téléchargez et exécutez l’extension de script du client.

7. Exécuter le script de l’agent. Le script personnalisé de l’agent effectue les tâches suivantes :
    - Installe etcd
    - Configure le service kubelet
    - Rejoint le cluster Kubernetes

## <a name="steps-for-troubleshooting"></a>Étapes pour la résolution de problème

Vous pouvez collecter des journaux sur les machines virtuelles qui prennent en charge votre cluster Kubernetes. Vous pouvez également consulter le journal de déploiement. Vous aurez peut-être besoin de contacter votre administrateur Azure Stack pour vérifier la version d’Azure Stack à utiliser et obtenir à partir d’Azure Stack les journaux liés à votre déploiement.

1. Examinez l’[état du déploiement](#review-deployment-status) et [récupérez les journaux](#get-logs-from-a-vm) à partir du nœud principal de votre cluster Kubernetes.
2. Assurez-vous que vous utilisez la version la plus récente d’Azure Stack. Si vous ne savez pas quelle version vous utilisez, contactez votre administrateur Azure Stack. L’élément de cluster Kubernetes version 0.3.0 de la Place de marché requiert Azure Stack 1808 ou une version ultérieure.
3.  Passez en revue vos fichiers de création des machines virtuelles. Vous avez peut-être rencontré les problèmes suivants :  
    - La clé publique peut être non valide. Examinez la clé que vous avez créée.  
    - La création de machines virtuelles peut avoir déclenché une erreur interne ou une erreur de création. Différents facteurs peuvent entraîner des erreurs, y compris les limites de capacité de votre abonnement Azure Stack.
    - Assurez-vous que le nom de domaine complet (FQDN) de la machine virtuelle commence par un préfixe dupliqué.
4.  Si la machine virtuelle est **OK**, passez à l’évaluation du DVM. Si le DVM affiche un message d’erreur :

    - La clé publique peut être non valide. Examinez la clé que vous avez créée.  
    - Vous devez contacter votre administrateur Azure Stack pour récupérer les journaux Azure Stack en utilisant les points de terminaison privilégiés. Pour plus d’informations, voir [Outils de diagnostics Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Si vous avez une question concernant votre déploiement, vous pouvez la poster ou regarder si quelqu’un y a déjà répondu sur le [Forum Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Examiner l’état du déploiement

Lorsque vous déployez votre cluster Kubernetes, vous pouvez consulter l’état du déploiement pour détecter d’éventuels problèmes.

1. Ouvrez le [portail Azure Stack](https://portal.local.azurestack.external).
2. Sélectionnez **Groupe de ressources**, puis sélectionnez le nom du groupe de ressources utilisé lors du déploiement du cluster Kubernetes.
3. Sélectionnez **Déploiements**, puis le **Nom du déploiement**.

    ![Résolution de problèmes](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consultez la fenêtre de résolution des problèmes. Chaque ressource déployée fournit les informations suivantes :
    
    | Propriété | Description |
    | ----     | ----        |
    | Ressource | Nom de la ressource. |
    | type | Fournisseur de la ressource et type de la ressource. |
    | Statut | État de l’élément. |
    | TimeStamp | Horodatage UTC du temps. |
    | Détails de l’opération | Informations détaillées sur l’opération, par exemple le fournisseur de ressources concerné par l’opération, le point de terminaison de la ressource et le nom de la ressource. |

    Chaque élément affiche une icône d’état verte ou rouge.

## <a name="get-logs-from-a-vm"></a>Obtenir des journaux à partir d’une machine virtuelle

Pour générer des journaux, vous devez vous connecter à la machine virtuelle principale de votre cluster, ouvrir une invite bash, puis exécuter un script. La machine virtuelle maître se trouve dans votre groupe de ressources de cluster et est nommée `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une invite bash sur la machine que vous utilisez pour gérer Azure Stack. Utilisez bash pour exécuter les scripts qui accèdent aux journaux. Sur une machine Windows, vous pouvez utiliser l’invite bash installée avec Git. Pour obtenir la version la plus récente de git, consultez les [téléchargements Git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Obtenir des journaux

Pour obtenir des journaux, suivez les étapes ci-dessous :

1. Ouvrez une invite bash. Si vous utilisez Git sur une machine Windows, vous pouvez ouvrir une invite bash à partir de l’emplacement suivant : `c:\programfiles\git\bin\bash.exe`.
2. Exécutez les commandes bash suivantes :

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > Sur Windows, vous n’avez pas besoin d’exécuter `sudo`. Vous pouvez simplement utiliser `chmod 744 getkuberneteslogs.sh` à la place.

3. Dans la même session, exécutez la commande suivante avec les paramètres mis à jour pour correspondre à votre environnement :

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

4. Passez en revue les paramètres et définissez les valeurs en fonction de votre environnement.
    | Paramètre           | Description                                                                                                      | Exemples                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i, --identity-file | Fichier de clé privée RSA pour se connecter à la machine virtuelle principale Kubernetes. La clé doit commencer par `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.pem                                                        |
    | -h, --host          | Adresse IP publique ou nom de domaine complet (FQDN) de la machine virtuelle principale du cluster Kubernetes. Le nom de la machine virtuelle commence par `k8s-master-`.                       | IP : 192.168.102.37<br><br>Nom de domaine complet : k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | Nom d’utilisateur de la machine virtuelle principale du cluster Kubernetes. Vous définissez ce nom lorsque vous configurez l’élément de la Place de marché.                                                                    | azureuser                                                                     |
    | -d, --vmdhost       | Adresse IP publique ou nom de domaine complet du DVM. Le nom de la machine virtuelle commence par `vmd-`.                                                       | IP : 192.168.102.38<br><br>DNS : vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Lorsque vous ajoutez vos valeurs de paramètres, cela peut ressembler à ce qui suit :

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Une exécution réussie crée les journaux.

    ![Journaux générés](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Récupérez les journaux dans les dossiers créés par la commande. La commande crée de nouveaux dossiers et les horodate.
    - KubernetesLogs*AAAA-MM-JJ-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Étapes suivantes

[Déployer Kubernetes sur Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Ajouter un cluster Kubernetes à la Place de marché (pour l’opérateur Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes sur Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
