---
title: Probes (diagnostics) Azure Service Fabric
description: Comment modéliser le diagnostic Probe Liveness dans Azure Service Fabric à l’aide de fichiers manifestes d’application et de service.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427441"
---
# <a name="liveness-probe"></a>Diagnostic Probe Liveness
À partir de la version 7.1 Service Fabric prend en charge le mécanisme Probe Liveness pour les applications [en conteneur][containers-introduction-link]. Le diagnostic Probe Liveness vous aide à annoncer l’activité de l’application en conteneur et lorsqu’il ne répond pas dans les délais, cela entraîne un redémarrage.
Cet article fournit une vue d’ensemble de la manière de définir un diagnostic Probe Liveness à l’aide de fichiers manifeste.

Avant de poursuivre la lecture de cet article, nous vous recommandons de vous familiariser avec le [modèle d’application Service Fabric][application-model-link] et le [modèle d’hébergement Service Fabric][hosting-model-link].

> [!NOTE]
> Le diagnostic Probe Liveness est uniquement pris en charge pour les conteneurs en mode de mise en réseau NAT.

## <a name="semantics"></a>Sémantique
Vous ne pouvez spécifier qu’un seul diagnostic Probe Liveness par conteneur et vous pouvez contrôler son comportement avec les champs suivants :

* `initialDelaySeconds`: Délai initial, en secondes, avant le début de l’exécution du diagnostic probe une fois que le conteneur a démarré. La valeur prise en charge est int. La valeur par défaut est 0. La valeur minimale est 0.

* `timeoutSeconds`: Période en secondes après laquelle nous considérons le diagnostic probe comme ayant échoué s’il ne s’est pas terminé avec succès. La valeur prise en charge est int. 1 constitue la valeur par défaut. Le minimum est 1.

* `periodSeconds`: Période en secondes pour spécifier la fréquence d’utilisation du diagnostic probe. La valeur prise en charge est int. La valeur par défaut est 10. Le minimum est 1.

* `failureThreshold`: Une fois que nous avons atteint FailureThreshold, le conteneur redémarre. La valeur prise en charge est int. La valeur par défaut est 3. Le minimum est 1.

* `successThreshold`: En cas d’échec, pour que le diagnostic probe soit considéré comme ayant réussi, il doit s’exécuter correctement pour SuccessThreshold. La valeur prise en charge est int. 1 constitue la valeur par défaut. Le minimum est 1.

Il y aura au maximum 1 diagnostic probe par conteneur à tout moment donné. Si le diagnostic probe ne se termine pas dans le délai **timeoutSeconds** nous continuons d’attendre et de l’inclure dans le décompte **failureThreshold**. 

En outre, ServiceFabric déclenche les [rapports d’intégrité][health-introduction-link] du diagnostic probe suivants sur DeployedServicePackage :

* `Ok`: Si le diagnostic probe réussit pour **successThreshold**, nous signalons l’intégrité comme OK.

* `Error`: Si failureCount pour le diagnostic probe == **failureThreshold**, avant de redémarrer le conteneur, nous signalons une erreur.

* `Warning`: 
    1. Si le diagnostic probe échoue et que failureCount < **failureThreshold** nous émettons un avertissement. Ce rapport d’intégrité reste jusqu’à ce que failureCount atteigne **failureThreshold** ou **successThreshold**.
    2. En cas de réussite après échec, nous émettons toujours un avertissement, mais avec une réussite consécutive mise à jour.

## <a name="specifying-liveness-probe"></a>Spécification du diagnostic Probe Liveness

Vous pouvez spécifier le diagnostic probe dans le fichier ApplicationManifest.xml sous ServiceManifestImport :

Le diagnostic probe peut être :

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>Probe HTTP

Pour le diagnostic probe HTTP, Service Fabric envoie une requête HTTP au port et au chemin d’accès spécifiés. Le code de retour supérieur ou égal à 200 et inférieur à 400 indique une réussite.

Voici un exemple de spécification d’un diagnostic probe HttpGet :

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

Le diagnostic probe HttpGet contient des propriétés supplémentaires que vous pouvez définir :

* `path`: Chemin d’accès à la requête HTTP.

* `port`: Port auquel accéder pour les diagnostics probes. La plage est comprise entre 1 et 65535. Mandatory.

* `scheme`: Schéma à utiliser pour la connexion au package de code. Si la valeur est HTTPS, la vérification du certificat est ignorée. La valeur par défaut est HTTP

* `httpHeader`: En-têtes à définir dans la requête. Vous pouvez en spécifier plusieurs.

* `host`: IP hôte à laquelle se connecter.

## <a name="tcp-probe"></a>Probe TCP

Pour le diagnostic probe TCP, Service Fabric essaye d’ouvrir un socket sur le conteneur avec le port spécifié. S’il peut établir une connexion, le diagnostic probe est considéré comme ayant réussi. Voici un exemple de spécification du diagnostic probe qui utilise le socket TCP :

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Probe Exec

Ce diagnostic probe émet un exec dans le conteneur et attend que la commande se termine.

> [!NOTE]
> La commande exec prend une chaîne séparée par des virgules. Dans l’exemple, la commande suivante fonctionne pour le conteneur Linux.
> Si vous essayez un conteneur Windows, utilisez <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Étapes suivantes
Pour accéder à des informations connexes, consultez les articles suivants.
* [Service Fabric et conteneurs.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

