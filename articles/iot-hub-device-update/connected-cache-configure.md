---
title: Configurer le cache connecté Microsoft pour Device Update pour Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Présentation du cache connecté Microsoft pour Device Update pour Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660290"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Configurer le cache connecté Microsoft pour Device Update pour Azure IoT Hub

Le cache connecté Microsoft est déployé sur les passerelles Azure IoT Edge en tant que module Azure IoT Edge. Comme les autres modules Azure IoT Edge, les variables d’environnement de déploiement de module MCC et les options de création de conteneur sont utilisées pour configurer les modules Cache connecté Microsoft.  Cette section définit les variables d’environnement et les options de création de conteneur requises pour qu’un client déploie correctement le module Cache connecté Microsoft, afin qu’il soit utilisé par Device Update pour Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Détails du déploiement du module Azure IoT Edge du cache connecté Microsoft

Le nommage du module Cache connecté Microsoft est à la discrétion de l’administrateur. Aucune autre interaction de module ou de service ne repose sur le nom du module pour la communication. En outre, la relation parent-enfant des serveurs de cache connecté Microsoft ne dépend pas de ce nom de module, mais plutôt du nom de domaine complet ou de l’adresse IP de la passerelle Azure IoT Edge qui a été configurée comme indiqué précédemment.

Les variables d’environnement du module Azure IoT Edge du cache connecté Microsoft sont utilisées pour transmettre les informations d’identité de base et les paramètres fonctionnels du module au conteneur.

| Nom de la variable :                 | Format de la valeur                           | Obligatoire ou facultatif | Fonctionnalité                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | GUID d’ID d’abonnement Azure             | Obligatoire          | Il s’agit de la clé du client, qui offre une authentification<br>sécurisée du nœud de cache aux services d’optimisation<br>de la distribution. Obligatoire pour que le module fonctionne. |
| CACHE_NODE_ID                 | GUID d’ID du nœud de cache                     | Obligatoire          | Identifie de façon unique le cache connecté Microsoft<br>Cache auprès des services d’optimisation de la distribution. Obligatoire pour que<br> le module fonctionne. |
| CUSTOMER_KEY                  | GUID de clé du client                     | Obligatoire          | Il s’agit de la clé du client, qui offre une authentification<br>sécurisée du nœud de cache aux services d’optimisation de la distribution.<br>Obligatoire pour que le module fonctionne.|
| STORAGE_ *N* _SIZE_GB           | Où N est le nombre de Go requis   | Obligatoire          | Spécifiez jusqu’à neuf lecteurs pour mettre en cache le contenu et spécifiez<br>l’espace maximal en Go à allouer au contenu sur chaque lecteur de cache. Exemples :<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Le numéro du lecteur doit correspondre aux valeurs de liaison de lecteur de cache spécifiées<br>dans la valeur MicrosoftConnectedCache *N* de l’option de création de conteneur|
| UPSTREAM_HOST                 | FQDN/IP                                | Facultatif          | Cette valeur peut spécifier un nœud de cache connecté<br>Microsoft en amont qui agit en tant que proxy si le nœud de cache connecté<br> est déconnecté d’Internet. Ce paramètre est utilisé pour prendre en charge<br> le scénario d’IoT imbriqué.<br>**Remarque :** Le cache connecté Microsoft est à l’écoute sur le port http par défaut 80.|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | Facultatif          | Le proxy Internet sortant.<br>Il peut également s’agir du proxy DMZ OT dans le cas d’un réseau ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>FQDN                        | Facultatif          | Requis pour prendre en charge les référentiels de packages personnalisés.<br>Les référentiels peuvent être hébergés localement ou sur Internet.<br>Aucune limite ne s’applique au nombre d’hôtes personnalisés qui peuvent être configurés.<br><br>Exemples :<br>Name = CACHEABLE_CUSTOM_1_HOST Value = packages.foo.com<br> Name = CACHEABLE_CUSTOM_2_HOST Value = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Facultatif          | Requis pour prendre en charge les référentiels de packages personnalisés.<br>Cette valeur peut être utilisée comme alias et sera utilisée par le serveur de cache pour référencer<br>les différents noms DNS. Par exemple, le nom d’hôte du contenu de référentiel peut être packages.foo.com,<br>mais, pour différentes régions, il peut y avoir un préfixe supplémentaire qui est ajouté au nom d’hôte<br>comme westuscdn.packages.foo.com et eastuscdn.packages.foo.com.<br>En définissant l’alias canonique, vous vous assurez que le contenu n’est pas dupliqué<br>pour le contenu provenant du même hôte mais de sources différentes du réseau CDN.<br>Le format de la valeur canonique n’est pas important, mais il doit être unique pour l’hôte.<br>Le plus simple peut être de définir la valeur de manière à ce qu’elle corresponde à la valeur de l’hôte.<br><br>Exemples basés sur les exemples d’hôtes personnalisés ci-dessus :<br>Name = CACHEABLE_CUSTOM_1_CANONICAL Value = foopackages<br> Name = CACHEABLE_CUSTOM_2_CANONICAL Value = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | True ou False                          | Facultatif          | Permet d’afficher le rapport de synthèse sur le réseau local ou sur Internet.<br>L’utilisation d’une clé API (décrite plus loin) est requise pour afficher le rapport de synthèse si la valeur est true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| True ou False                          | Facultatif          | Permet d’afficher un rapport de synthèse sur le réseau local ou sur Internet sans<br>utiliser la clé API de n’importe quel appareil du réseau. Utilisez cela si vous ne souhaitez pas verrouiller l’accès<br>pour afficher les données de synthèse du serveur de cache via le navigateur. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Options de création de conteneur du module Azure IoT Edge de cache connecté Microsoft

Les options de création de conteneur pour le déploiement du module MCC permettent de contrôler les paramètres relatifs au stockage et aux ports utilisés par le module MCC. Il s’agit de la liste des variables requises créées de conteneur utilisées pour déployer MCC.

### <a name="container-to-host-os-drive-mappings"></a>Conteneur d’hébergement des mappages de lecteur de système d’exploitation

Requis pour mapper l’emplacement de stockage du conteneur à l’emplacement de stockage sur le disque.< Jusqu’à neuf emplacements peuvent être spécifiés.

>[!Note]
>Le numéro du lecteur doit correspondre aux valeurs de liaison de lecteur de cache spécifiées dans la valeur STORAGE_ *N* _SIZE_GB de la variable d’environnement, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Conteneur d’hébergement des mappages de ports TCP

Cette option spécifie le port http de l’ordinateur externe sur lequel MCC écoute les demandes de contenu. Le port HostPort par défaut est le port 80 et les autres ports ne sont pas pris en charge pour le moment, car le client ADU effectue actuellement des demandes sur le port 80. Le port TCP 8081 est le port de conteneur interne sur lequel MCC est à l’écoute et ne peut pas être modifié.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>Mappages de ports TCP de service de conteneur

Le module Cache connecté Microsoft possède un service .NET Core, qui est utilisé par le moteur de mise en cache pour différentes fonctions.

>[!Note]
>Pour prendre en charge la périphérie imbriquée Azure IoT, le port HostPort ne doit pas être défini sur 5000 car le module proxy du Registre est déjà à l’écoute sur le port hôte 5000.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Rapport de synthèse de cache connecté Microsoft

Le rapport de synthèse est actuellement le seul moyen pour un client d’afficher les données de mise en cache pour les instances de cache connecté Microsoft déployées sur les passerelles Azure IoT Edge. Ce rapport est généré à 15 secondes d’intervalle et comprend des statistiques moyennes pour la période, ainsi que des statistiques agrégées pour la durée de vie du module. Les statistiques clés qui intéressent les clients sont les suivantes :

* **hitBytes** – Il s’agit de la somme des octets remis qui proviennent directement du cache.
* **missBytes** – Il s’agit de la somme des octets remis que le cache connecté Microsoft a dû télécharger à partir du réseau CDN pour voir le cache.
* **eggressBytes** – Il s’agit de la somme de hitBytes et de missBytes, et elle correspond au nombre total d’octets remis aux clients.
* **hitRatioBytes** – Il s’agit du taux de hitBytes par rapport à egressBytes.  Par exemple, si 100 % des eggressBytes remis au cours d’une période étaient égaux au hitBytes, ce paramètre aurait pour valeur 1.

Le rapport de synthèse est disponible dans `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (reportez-vous aux détails de variable d’environnement ci-dessous pour obtenir plus d’informations sur la visibilité de ce rapport).
