---
title: Résoudre les problèmes du module complémentaire Open Service Mesh
description: Résolution des problèmes du module complémentaire Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 0394815b4e75fb8d1f1f277010602839dc6876eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440996"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Guides de résolution des problèmes du module complémentaire Open Service Mesh (OSM) AKS

Lorsque vous déployez le module complémentaire OSM AKS, vous risquez de rencontrer des problèmes liés à la configuration de la maille du service. Le guide suivant vous aidera à résoudre les erreurs et les problèmes courants.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verifying-and-troubleshooting-osm-components"></a>Vérification et résolution des problèmes des composants OSM

### <a name="check-osm-controller-deployment"></a>Vérifier le déploiement du contrôleur OSM

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Un contrôleur OSM sain ressemble à ceci :

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="check-the-osm-controller-pod"></a>Vérifier le pod du contrôleur OSM

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Un pod OSM sain ressemble à ceci :

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Même si un contrôleur a été expulsé à un moment donné, un autre indique READY 1/1 et Running avec 0 redémarrage. Si la colonne READY indique un résultat différent de 1/1, le maillage de services est en panne.
La colonne READY avec 0/1 indique que le conteneur du plan de contrôle rencontre un incident. Nous devons donc récupérer les journaux. Consultez la section Obtenir les journaux du contrôleur OSM du Centre de support Azure ci-dessous. La colonne READY avec un nombre supérieur à 1 après le signe « / » indique que des side-cars sont installés. Le contrôleur OSM ne fonctionnera probablement pas si des side-cars lui sont attachés.

### <a name="check-osm-controller-service"></a>Vérifier le service du contrôleur OSM

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Un service de contrôleur OSM sain ressemble à ceci :

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> La valeur pour CLUSTER-IP sera différente. Les propriétés NAME et PORT(S) du service doivent être les mêmes que dans l’exemple ci-dessus.

### <a name="check-osm-controller-endpoints"></a>Vérifier les points de terminaison du contrôleur OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Un point de terminaison de contrôleur OSM sain ressemble à ceci :

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

### <a name="check-osm-injector-deployment"></a>Vérifier le déploiement de l’injecteur OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Un déploiement d’injecteur OSM sain ressemble à ceci :

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-pod"></a>Vérifier le pod de l’injecteur OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Un pod d’injecteur OSM sain ressemble à ceci :

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-service"></a>Vérifier le service de l’injecteur OSM

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Un service d’injecteur OSM sain ressemble à ceci :

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

### <a name="check-osm-endpoints"></a>Vérifier les points de terminaison OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Un point de terminaison OSM sain ressemble à ceci :

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

### <a name="check-validating-and-mutating-webhooks"></a>Vérifier les webhooks Validating et Mutating

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Un webhook Validating OSM sain ressemble à ceci :

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Un webhook Mutating OSM sain ressemble à ceci :

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Rechercher le service et le pack de l’autorité de certification du webhook Validating

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Validating bien configuré doit ressembler à ceci :

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Rechercher le service et le pack de l’autorité de certification du webhook Mutating

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Mutating bien configuré doit ressembler à ceci :

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Vérifiez si le contrôleur OSM a donné au webhook Validating (or Mutating) un pack d’autorité de certification.

> [!NOTE]
> À partir de la version 0.8.2, il est important de savoir qu’AKS RP installe le webhook Validating, que l’outil de rapprochement AKS s’assure de son existence, mais que le contrôleur OSM est celui qui remplit le pack de l’autorité de certification.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Ce nombre indique le nombre d’octets ou la taille du pack de l’autorité de certification. S’il est vide, égal à 0 ou inférieur à 1000, cela signifie que le pack de l’autorité de certification n’est pas correctement approvisionné. Sans un pack d’autorité de certification correct, le webhook Validating émettrait une erreur et interdirait à l’utilisateur d’apporter des modifications au ConfigMap osm-config dans l’espace de noms kube-system.

Exemple d’erreur lorsque le pack de l’autorité de certification est incorrect :

- Tentative de modification du ConfigMap osm-config :

  ```azurecli-interactive
  kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```

- Erreur :

  ```
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

Solution de contournement lorsque la configuration du webhook **Validating** a un certificat incorrect :

- Option 1 : Redémarrer le contrôleur OSM. Cette option permet de redémarrer le contrôleur OSM. Au démarrage, il remplacera le pack de l’autorité de certification des webhooks Mutating et Validating.

  ```azurecli-interactive
  kubectl rollout restart deployment -n kube-system osm-controller
  ```

- Option 2 : Supprimer le webhook Validating. La suppression du webhook Validating invalide les mutations du ConfigMap `osm-config`. Tous les patchs seront concernés. L’outil de rapprochement AKS s’assurera à un moment donné que le webhook Validating existe et le recréera. Il peut être nécessaire de redémarrer le contrôleur OSM pour réécrire rapidement le pack de l’autorité de certification.

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
  ```

- Option 3 : Supprimer et corriger. La commande suivante permet de supprimer le webhook Validating, ce qui nous permet d’ajouter des valeurs et de tenter immédiatement d’appliquer un patch. Il est fort probable que l’outil de rapprochement AKS n’ait pas suffisamment de temps pour rapprocher et restaurer le webhook Validating, ce qui nous permet d’appliquer une modification en dernier recours :

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```

### <a name="check-the-osm-mesh-config-resource"></a>Vérifiez la `osm-mesh-config` ressource

Vérifiez son existence :

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

Vérifier le contenu de la MeshConfig OSM

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

`osm-mesh-config` Valeurs de ressources :

| Clé | Type | Valeur par défaut | Exemples de commandes patch Kubectl |
|-----|------|---------------|--------------------------------|
| spec.traffic.enableEgress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | tableau | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundPortExclusionList":6379,8080}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | tableau | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":"10.0.0.0/32,1.1.1.1/24"}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | string | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"serviceCertValidityDuration":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `"jaeger.osm-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.osm-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.enable | string | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.endpoint | string | `false`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.port | int | `9411`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | string | `"error"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | int | `0` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | string | `"envoyproxy/envoy-alpine:v1.17.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"envoyImage":"envoyproxy/envoy-alpine:v1.17.2"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | string | `"openservicemesh/init:v0.9.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"initContainerImage":"openservicemesh/init:v0.9.2"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | string | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |

### <a name="check-namespaces"></a>Vérifier les espaces de noms

> [!NOTE]
> L’espace de noms kube-system ne fait jamais partie d’un maillage de services et n’est jamais étiqueté ou annoté avec les clés/valeurs ci-dessous.

Nous utilisons la commande `osm namespace add` pour joindre des espaces de noms à un maillage de services donné.
Lorsqu’un espace de noms k8s fait partie du maillage (ou pour qu’il fasse partie du maillage), les conditions suivantes doivent être remplies :

Affichez les annotations à l’aide de la commande suivante :

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

L’annotation suivante doit être présente :

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Affichez les étiquettes à l’aide de la commande suivante :

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

L’étiquette suivante doit être présente :

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Si un espace de noms n’est pas annoté avec `"openservicemesh.io/sidecar-injection": "enabled"` ou n’a pas l’étiquette `"openservicemesh.io/monitored-by": "osm"`, l’injecteur OSM n’ajoutera pas les side-cars Envoy.

> [!NOTE]
> Une fois que `osm namespace add` est appelé, seuls les **nouveaux** pods sont injectés avec un side-car Envoy. Les pods existants doivent être redémarrés à l’aide de la commande `kubectl rollout restart deployment ...`.

### <a name="verify-the-smi-crds"></a>Vérifier les CRD SMI :

Vérifiez si le cluster possède les CRD requis :

```azurecli-interactive
kubectl get crds
```

Les éléments suivants doivent être installés sur le cluster :

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Récupérez les versions des CRD installés avec cette commande :

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Sortie attendue :

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

Le contrôleur OSM v0.8.2 requiert les versions suivantes :

- traffictargets.access.smi-spec.io : [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io : [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io : [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io : Non pris en charge
- trafficsplits.split.smi-spec.io : [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io : [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Si des CRD sont manquants, utilisez les commandes suivantes pour les installer sur le cluster :

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```