---
title: Déployer une passerelle auto-hébergée sur Kubernetes | Microsoft Docs
description: Découvrez comment déployer le composant de passerelle auto-hébergée de la Gestion des API Azure sur Kubernetes.
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204830"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Déployer une passerelle auto-hébergée sur Kubernetes

Cet article explique comment déployer la passerelle auto-hébergée de la Gestion des API Azure sur un cluster Kubernetes.

## <a name="prerequisites"></a>Prérequis

- Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
- Créez un cluster Kubernetes.
> [!TIP]
> Les [clusters à nœud unique](https://kubernetes.io/docs/setup/#learning-environment) conviennent bien au développement et à l’évaluation. Utilisez des clusters multinœuds [certifiés par Kubernetes](https://kubernetes.io/partners/#conformance) pour des charges de travail de production locales ou dans le cloud.
- [Provisionnez une ressource de passerelle auto-hébergée dans votre instance de la Gestion des API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Déployer sur Kubernetes

1. Sélectionnez **Gateways** (Passerelles) sous **Deployment and infrastructure** (Déploiement et infrastructure).
2. Sélectionnez la ressource de passerelle auto-hébergée que vous souhaitez déployer.
3. Sélectionnez **Déploiement**.
4. Remarquez qu’un jeton d’accès a été généré automatiquement dans la zone de texte **Token** (Jeton), avec les valeurs par défaut pour l’expiration (**Expiry**) et la clé secrète (**Secret key**). Si nécessaire, sélectionnez les valeurs souhaitées dans l’un des contrôles ou dans les deux pour générer un jeton.
5. Sélectionnez l’onglet **Kubernetes** sous **Deployment scripts** (Scripts de déploiement).
6. Cliquez sur le lien du fichier **<nom de la passerelle>.yml**, puis téléchargez le fichier YAML.
7. Sélectionnez l’icône **Copier** située en bas à droite de la zone de texte **Deploy** (Déployer) pour enregistrer les commandes `kubectl` dans le Presse-papiers.
8. Collez les commandes dans la fenêtre du terminal. La première commande crée une clé secrète Kubernetes contenant un jeton d’accès généré à l’étape 4. La deuxième commande applique le fichier de configuration téléchargé à l’étape 6 au cluster Kubernetes, et s’attend à ce que le fichier soit présent dans le répertoire actif.
9. Exécutez les commandes pour créer les objets Kubernetes nécessaires dans l’[espace de noms par défaut](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) et démarrez le ou les pods de la passerelle auto-hébergée à partir d’une [image conteneur](https://aka.ms/apim/sputnik/dhub) téléchargée à partir de Microsoft Container Registry.
10. Exécutez la commande indiquée ci-dessous pour vérifier si le déploiement a réussi. Notez que le temps nécessaire à la création de tous les objets et à l’initialisation du pod peut être long.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Exécutez la commande ci-dessous pour vérifier si le service a bien été créé. Notez que les adresses IP et les ports de votre service seront différents.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Revenez au portail Azure, puis sélectionnez **Vue d’ensemble**.
13. Lorsqu’une coche verte, suivie du nombre de nœuds correspondant au nombre de réplicas spécifiés dans le fichier YAML, s’affiche sous **État**, cela signifie que les pods de la passerelle auto-hébergée déployés communiquent avec le service Gestion des API et que leur « pulsation » est normale.

![état de la passerelle](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Exécutez la commande <code>kubectl logs deployment/<gateway-name></code> pour afficher les journaux d’un pod sélectionné de manière aléatoire, s’il en existe plusieurs.
> Exécutez <code>kubectl logs -h</code> pour obtenir un ensemble complet d’options de commande, par exemple, comment afficher les journaux d’un pod ou d’un conteneur.

## <a name="production-deployment-considerations"></a>Points à prendre en considération pour le déploiement de production

### <a name="access-token"></a>Jeton d’accès
Sans un jeton d’accès valide, la passerelle auto-hébergée ne pourra pas accéder à la configuration ni la télécharger à partir du point de terminaison des données de configuration du service Gestion des API associé. Le jeton d’accès reste valide pendant un maximum de 30 jours. Le jeton doit être regénéré et le cluster doit être configuré avec un nouveau jeton, manuellement ou via l’automatisation, avant son expiration. Si vous automatisez l’actualisation des jetons, utilisez cette [opération](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) d’API de gestion pour générer un nouveau jeton. Pour plus d’informations sur la gestion des secrets Kubernetes, cliquez sur [ce lien](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Espace de noms
Les [espaces de noms](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes permettent de diviser un cluster entre plusieurs équipes, projets ou applications. Les espaces de noms fournissent une étendue pour les ressources et les noms, et peuvent être associés à un quota de ressources et à des stratégies de contrôle d’accès.
Les commandes fournies dans le portail Azure créent des ressources de passerelle auto-hébergée dans l’espace de noms **par défaut**. Celui-ci est créé automatiquement, se trouve sur chacun des clusters et ne peut pas être supprimé.
Dans un environnement de production, il est conseillé de [créer et déployer](https://kubernetesbyexample.com/ns/) la passerelle auto-hébergée dans un espace de noms distinct.

### <a name="number-of-replicas"></a>Nombre de réplicas
Dans un environnement de production, vous devez disposer d’au moins deux réplicas.

Par défaut, la passerelle auto-hébergée est déployée avec une [stratégie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy) de déploiement de type **RollingUpdate**. Passez en revue les valeurs par défaut. Il est conseillé de définir explicitement les champs [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) et [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge), en particulier si vous utilisez un nombre élevé de réplicas.

### <a name="container-resources"></a>Ressources sur les conteneurs
Par défaut, le fichier YAML fourni dans le portail Azure ne spécifie pas les demandes de ressources de conteneur.

Il est impossible de prédire et donc de recommander de manière fiable la quantité de ressources processeur et mémoire pour chaque conteneur, ainsi que le nombre de réplicas nécessaires pour prendre en charge une charge de travail spécifique. Ceci est dû à de nombreux facteurs, comme :

- Le matériel sur lequel le cluster s’exécute
- La présence et le type de virtualisation
- Le nombre et le taux de connexions clientes simultanées
- Taux de demandes
- Le type et le nombre de stratégies configurées
- La taille de la charge utile, et si les charges utiles sont mises en mémoire tampon ou diffusées en continu
- Latence du service back-end

Pour commencer, nous vous recommandons de définir la demande de ressource sur 2 cœurs et 2 Gio, d’effectuer un test de charge et d’effectuer un scale-up ou un scale-down en fonction des résultats.

### <a name="container-image-tag"></a>Étiquette de l’image conteneur
Le fichier YAML fourni dans le portail Azure utilise l’étiquette **la plus récente**, qui référence toujours la version la plus récente de l’image conteneur de la passerelle auto-hébergée.

Il est conseillé d’utiliser une étiquette de version distincte en production pour éviter toute mise à niveau involontaire vers une version plus récente.

Pour afficher la liste complète des étiquettes disponibles, cliquez sur [ce lien](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>Stratégie DNS
La résolution de noms DNS joue un rôle essentiel dans la capacité de la passerelle auto-hébergée à se connecter aux dépendances dans Azure, et à distribuer des appels d’API aux services back-end.

Le fichier YAML fourni dans le portail Azure applique la stratégie par défaut [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy), qui provoque le transfert des demandes de résolution de noms non résolues par le DNS du cluster vers le serveur DNS en amont hérité du nœud.

Cliquez sur [ce lien](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) pour en savoir plus sur la résolution de noms dans Kubernetes et pensez à personnaliser votre [stratégie DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) ou votre [configuration DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config), selon votre configuration.

### <a name="configuration-backup"></a>Sauvegarde de la configuration
Cliquez sur [ce lien](self-hosted-gateway-overview.md#connectivity-to-azure) pour en savoir plus sur le comportement de la passerelle auto-hébergée en cas de panne de connectivité temporaire à Azure.
Configurez le volume de stockage local pour le conteneur de la passerelle auto-hébergée, afin qu’il puisse conserver une copie de sauvegarde de la dernière configuration téléchargée, et pour qu’il puisse l’utiliser au redémarrage si la connectivité est interrompue. Le chemin du montage de volume doit être le suivant : <code>/apim/config</code>. Un exemple vous est proposé [ici](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Pour plus d’informations sur le stockage dans Kubernetes, cliquez sur [ce lien](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Métriques et journaux locaux
La passerelle auto-hébergée envoie des données de télémétrie à [Azure Monitor](api-management-howto-use-azure-monitor.md) et à [Azure Application Insights](api-management-howto-app-insights.md) pour chaque paramètre de configuration du service Gestion des API associé.
Lorsque la [connectivité à Azure](self-hosted-gateway-overview.md#connectivity-to-azure) est temporairement perdue, le flux des données de télémétrie envoyées vers Azure est interrompu et les données sont perdues pendant toute la durée de la panne.
Il est conseillé de [configurer une supervision locale](how-to-configure-local-metrics-logs.md) pour garantir la capacité à observer le trafic d’API et pour empêcher la perte des données de télémétrie pendant les interruptions de connectivité Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md)