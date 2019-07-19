---
title: Déployer un Prometheus autonome dans un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Voici comment créer une instance Prometheus sur un cluster Azure Red Hat OpenShift pour superviser les métriques de votre application.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, métriques, red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343264"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Déployer un Prometheus autonome dans un cluster Azure Red Hat OpenShift

Ce guide décrit comment configurer un Prometheus autonome avec la découverte des services dans un cluster Azure Red Hat OpenShift.  L’accès au cluster « Administrateur du client » n’est PAS nécessaire.

La configuration cible est la suivante :

- Un projet (prometheus-project), qui contient Prometheus et Alertmanager
- Deux projets (app-project1 et app-project2), qui contiennent les applications à superviser

Vous allez préparer certains fichiers de configuration Prometheus localement. Créez un dossier pour les stocker.
Ces fichiers de configuration seront stockés dans le cluster en tant que secrets pour le cas où des jetons secrets leur seraient ajoutés plus tard.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>Étape 1 : Se connecter au cluster à l’aide de l’outil `oc`
À l’aide d’un navigateur web, accédez à la console web de votre cluster (https://openshift.*ID_aléatoire*.*région*.azmosa.io).
Connectez-vous avec vos informations d’identification Azure.
Cliquez sur votre nom d’utilisateur dans le coin supérieur droit et sélectionnez « Copy Login Command » (Copier la commande de connexion). Collez-la dans le terminal que vous utiliserez.

Vous pouvez vérifier si vous êtes connecté au bon cluster avec la commande `oc whoami -c`.

## <a name="step-2-prepare-the-projects"></a>Étape 2 : Préparer les projets

Créez des projets.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Vous pouvez utiliser le paramètre `-n` ou `--namespace` ou sélectionner un projet actif avec la commande `oc project`.

## <a name="step-3-prepare-prometheus-config"></a>Étape 3 : Préparer la configuration Prometheus
Créez un fichier nommé prometheus.yml avec le contenu suivant.
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Créez un secret nommé « prom » avec la configuration.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Le fichier ci-dessus est un fichier de configuration Prometheus de base.
Il définit les intervalles et configure la découverte automatique dans trois projets (prometheus-project, app-project1, app-project2).
Dans cet exemple, les points de terminaison découverts automatiquement seront interrogés sur HTTP sans authentification.
Pour plus d’informations sur l’interrogation (« scraping ») des points de terminaison, consultez https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>Étape 4 : Préparer la configuration Alertmanager
Créez un fichier nommé alertmanager.yml avec le contenu suivant.
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Créez un secret nommé « prom-alerts » avec la configuration.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Le fichier ci-dessus est le fichier de configuration Alertmanager.

> [!NOTE]
> Vous pouvez vérifier les deux étapes précédentes avec `oc get secret -n prometheus-project`.

## <a name="step-5-start-prometheus-and-alertmanager"></a>Étape 5 : Démarrer Prometheus et Alertmanager
Téléchargez le modèle [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) à partir du [dépôt openshift/origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) et appliquez-le dans prometheus-project.
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Le fichier prometheus-standalone.yaml est un modèle OpenShift qui crée une instance Prometheus avec oauth-proxy en guise de préfixe et une instance Alertmanager, également sécurisée avec oauth-proxy.  Dans ce modèle, oauth-proxy est configuré pour autoriser tout utilisateur capable d’obtenir (« get ») l’espace de noms « prometheus-project » (voir l’indicateur `-openshift-sar`).

> [!NOTE]
> Vous pouvez vérifier si le StatefulSet « prom » a un nombre égal de réplicas *DESIRED* et *CURRENT* avec la commande `oc get statefulset -n prometheus-project`.
> Vous pouvez également vérifier toutes les ressources dans le projet avec `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>Étape 6 : Ajouter des autorisations pour permettre la découverte des services
Créez un fichier prometheus-sdrole.yml avec le contenu suivant.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Appliquez le modèle à tous les projets où vous souhaitez autoriser la découverte des services.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Si vous souhaitez également que Prometheus puisse recueillir des métriques de lui-même, n’oubliez pas d’appliquer aussi les autorisations dans prometheus-project.

> [!NOTE]
> Vous pouvez vérifier si le rôle et la liaison de rôle ont été créés correctement respectivement avec les commandes `oc get role` et `oc get rolebinding`.

## <a name="optional-deploy-example-application"></a>Facultatif : Déployer l’exemple d’application
Tout fonctionne, mais il n’y a aucune source de métriques. Accédez à l’URL Prometheus (https://prom-prometheus-project.apps.*ID_aléatoire*.*région*.azmosa.io/), que vous pouvez trouver avec la commande suivante.
```
oc get route prom -n prometheus-project
```
N’oubliez pas de faire précéder le nom d’hôte du préfixe https://.

La page**Status > Service Discovery** (États > Découverte des services) affiche 0/0 cible active.

Pour déployer un exemple d’application qui expose des métriques de base python sous le point de terminaison /metrics, exécutez les commandes suivantes.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Les nouvelles applications apparaîtront en tant que cibles valides dans la page de découverte des services dans les 30 secondes après le déploiement. Vous trouverez des détails supplémentaires dans la page **Status > Targets** (États > Cibles).

> [!NOTE]
> Pour chaque cible correctement interrogée, Prometheus ajoute un point de données dans la métrique « up ». Cliquez sur **Prometheus** dans le coin supérieur gauche, puis entrez « up » comme expression et cliquez sur **Execute**.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter une instrumentation Prometheus personnalisée à vos applications.

La bibliothèque de client Prometheus, qui simplifie la préparation des métriques Prometheus, est prête pour différents langages de programmation.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Go https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
