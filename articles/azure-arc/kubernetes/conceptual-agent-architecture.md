---
title: Architecture des agents Kubernetes compatibles avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article offre une vue d’ensemble architecturale des agents Kubernetes compatibles avec Azure Arc.
keywords: Kubernetes, Arc, Azure, conteneurs
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561141"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architecture des agents Kubernetes compatibles avec Azure Arc

[Kubernetes](https://kubernetes.io/) peut être utilisé pour déployer des charges de travail conteneurisées dans des environnements hybrides et à plusieurs clouds et ce, de manière cohérente. Kubernetes compatible avec Azure Arc peut être utilisé en tant que plan de contrôle centralisé pour gérer de manière cohérente la stratégie, la gouvernance et la sécurité dans ces environnements hétérogènes. Cet article fournit :

* Présentation architecturale de la connexion d’un cluster à Azure Arc.
* Modèle de connectivité suivi par les agents.
* Description des données échangées entre l’environnement de cluster et Azure.

## <a name="deploy-agents-to-your-cluster"></a>Déployer des agents sur votre cluster

La plupart des centres de données locaux appliquent des règles de réseau strictes qui empêchent les communications entrantes sur le pare-feu utilisé à la limite du réseau. Kubernetes compatible avec Azure Arc utilise ces restrictions en activant uniquement des points de terminaison de sortie sélectifs à des fins de communication sortante et ne nécessitant pas de ports entrants sur le pare-feu. Les agents Kubernetes compatibles avec Azure Arc activent les connexions sortantes.

![Vue d’ensemble de l’architecture](./media/architectural-overview.png)

Pour connecter un cluster à Azure Arc, procédez comme suit :

1. Créez un cluster Kubernetes sur l’infrastructure de votre choix (VMware vSphere, Amazon Web Services, Google Cloud Platform, etc.). 

    > [!NOTE]
    > Les clients doivent créer et gérer le cycle de vie du cluster Kubernetes, car actuellement Kubernetes compatible avec Azure Arc prend uniquement en charge l’attachement de clusters Kubernetes existants sur Azure Arc.  

1. Lancez l’inscription Azure Arc pour votre cluster à l’aide d’Azure CLI.
    * Azure CLI utilise Helm pour déployer le graphique Helm de l’agent sur le cluster.
    * Les nœuds de cluster initient une communication sortante vers [Microsoft Container Registry](https://github.com/microsoft/containerregistry) et extraient les images nécessaires à la création des agents suivants dans l’espace de noms `azure-arc` :

        | Agent | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Actuellement, Kubernetes compatible avec Azure Arc prend uniquement en charge les [identités affectées par le système](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). clusteridentityoperator établit la première communication sortante nécessaire pour récupérer le certificat MSI (Managed Service Identity) utilisé par d’autres agents pour communiquer avec Azure. |
        | `deployment.apps/config-agent` | Surveille le cluster connecté pour les ressources de configuration du contrôle de code source appliquées au cluster et met à jour l’état de conformité |
        | `deployment.apps/controller-manager` | Opérateur d’opérateurs orchestrant les interactions entre les composants Azure Arc |    
        | `deployment.apps/metrics-agent` | Collecte les métriques d’autres agents Arc pour s’assurer que ces agents offrent des performances optimales |
        | `deployment.apps/cluster-metadata-operator` | Collecte les métadonnées de cluster, la version du cluster, le nombre de nœuds et la version de l’agent Azure Arc |
        | `deployment.apps/resource-sync-agent` | Synchronise les métadonnées de cluster mentionnées ci-dessus dans Azure |
        | `deployment.apps/flux-logs-agent` | Collecte des journaux des opérateurs de flux déployés dans le cadre de la configuration du contrôle de code source |
    
1. Une fois tous les pods d’agent Kubernetes compatibles avec Azure Arc dans l’état `Running`, vérifiez que votre cluster est connecté à Azure Arc. Ce qui suit doit s’afficher :
    * Une ressource Kubernetes compatible avec Azure Arc dans [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Cette ressource est suivie dans Azure en tant que projection du cluster Kubernetes managé par le client, et non en tant que cluster Kubernetes réel.
    * Des métadonnées de cluster, telles que la version Kubernetes, la version de l’agent et le nombre de nœuds, apparaissent sur la ressource Kubernetes compatible avec Azure Arc sous forme de métadonnées.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Échange de données entre l’environnement de cluster et Azure

| Type de données | Scénario | Mode de communication |
| --------- | -------- | ------------------ |
| Version du cluster Kubernetes | Métadonnées du cluster | Envoi (push) de l’agent vers Azure |
| Nombre de nœuds du cluster | Métadonnées du cluster | Envoi (push) de l’agent vers Azure |
| Version de l’agent | Métadonnées du cluster | Envoi (push) de l’agent vers Azure |
| Type de distribution Kubernetes | Métadonnées du cluster | Envoi (push) d’Azure CLI vers Azure |
| Type d’infrastructure (AWS/GCP/vSphere/...) | Métadonnées du cluster | Envoi (push) d’Azure CLI vers Azure |
| Nombre de processeurs virtuels de nœuds virtuels du cluster | Facturation | Envoi (push) d’Azure CLI vers Azure |
| Pulsation de l'agent | Intégrité des ressources | Envoi (push) de l’agent vers Azure |
| Consommation des ressources (mémoire/UC) par les agents | Diagnostics et prise en charge | Envoi (push) de l’agent vers Azure |
| Journaux de tous les conteneurs d’agent | Diagnostics et prise en charge | Envoi (push) de l’agent vers Azure |
| Disponibilité de la mise à niveau de l’agent | Mise à niveau de l'agent | Tirage (pull) de l’agent depuis Azure |
| État souhaité de la configuration : URL du référentiel Git, paramètres d’opérateur de flux, clé privée, contenu des hôtes connus, nom d’utilisateur HTTPS, jeton/mot de passe | Configuration | Tirage (pull) de l’agent depuis Azure |
| État d’installation de l’opérateur de flux | Configuration | Envoi (push) de l’agent vers Azure |
| Affectations Azure Policy nécessitant l’application de Gatekeeper au sein du cluster | Azure Policy | Tirage (pull) de l’agent depuis Azure |
| Audit et état de conformité des applications de stratégie dans le cluster | Azure Policy | Envoi (push) de l’agent vers Azure |
| Métriques et journaux des charges de travail client | Azure Monitor | Envoi (push) de l’agent vers ressource de l’espace de travail Log Analytics dans le locataire et l’abonnement du client |

## <a name="connectivity-status"></a>État de la connectivité

| Statut | Description |
| ------ | ----------- |
| Connecting | Ressource Kubernetes compatible avec Azure Arc créée dans Azure Resource Manager, mais le service n’a pas encore reçu de pulsation de l’agent. |
| Connecté | Le service Kubernetes compatible Azure Arc a reçu une pulsation de l’agent au cours des 15 dernières minutes. |
| Hors connexion | La ressource Kubernetes compatible Azure Arc était précédemment connectée, mais le service n’a pas reçu de pulsation d’agent pendant 15 minutes. |
| Expiré | Le certificat MSI (Managed Service Identity) présente une fenêtre d’expiration de 90 jours après son émission. Une fois ce certificat expiré, la ressource est considérée `Expired` et toutes les fonctionnalités telles que la configuration, la surveillance et la stratégie cessent de fonctionner sur ce cluster. De plus amples informations sur la façon de traiter les ressources Kubernetes compatibles avec Azure Arc expirées sont disponibles [ici](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Comprendre les modes de connectivité

| Mode de connectivité | Description |
| ----------------- | ----------- |
| Entièrement connecté | Les agents sont toujours en mesure d’accéder à Azure. L’expérience est idéale dans ce cas, car il y a peu de retard en termes de propagation des configurations (pour GitOps), d’application des stratégies (dans Azure Policy et Gatekeeper) et de collecte des métriques et des journaux des charges de travail (dans Azure Monitor) |
| Semi-connecté | Le certificat MSI extrait par le `clusteridentityoperator` est valide pendant 90 jours maximum avant son expiration. Une fois le certificat expiré, la ressource Kubernetes compatible avec Azure Arc cesse de fonctionner. Supprimez et recréez la ressource Kubernetes compatible avec Azure Arc et les agents pour permettre le fonctionnement de toutes les fonctionnalités Arc sur le cluster. Pendant les 90 jours, il est recommandé aux utilisateurs de connecter le cluster au moins une fois tous les 30 jours. |
| Déconnecté | Actuellement, les clusters Kubernetes des environnements déconnectés sans accès à Azure ne sont pas pris en charge par Kubernetes compatible avec Azure Arc. Si cette fonctionnalité vous intéresse, envoyez ou votez pour une idée sur le [forum UserVoice d’Azure Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Étapes suivantes

* [Connecter un cluster à Azure Arc](./connect-cluster.md)
* [Vue d’ensemble conceptuelle des configurations](./conceptual-configurations.md)