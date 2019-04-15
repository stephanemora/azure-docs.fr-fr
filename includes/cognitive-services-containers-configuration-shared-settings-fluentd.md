---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741059"
---
Fluentd est un collecteur de données open source pour la journalisation unifiée. Les paramètres `Fluentd` gèrent la connexion du conteneur à un serveur [Fluentd](https://www.fluentd.org). Le conteneur comprend un fournisseur de journalisation Fluentd qui permet à votre conteneur d’écrire des données de journaux d’activité et, éventuellement, de métriques, sur un serveur Fluentd.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `Fluentd`.

| Nom | Type de données | Description |
|------|-----------|-------------|
| `Host` | Chaîne | Adresse IP ou nom d’hôte DNS du serveur Fluentd. |
| `Port` | Entier  | Port du serveur Fluentd.<br/> La valeur par défaut est 24224. |
| `HeartbeatMs` | Entier  | Intervalle de pulsation, en millisecondes. Si aucun trafic d’événement n’est envoyé avant l’expiration de cet intervalle, une pulsation est envoyée au serveur Fluentd. La valeur par défaut est de 60 000 millisecondes (1 minute). |
| `SendBufferSize` | Entier  | Espace de mémoire tampon réseau, en octets, alloué pour les opérations d’envoi. La valeur par défaut est de 32 768 octets (32 kilo-octets). |
| `TlsConnectionEstablishmentTimeoutMs` | Entier  | Délai d’attente, en millisecondes, pour établir une connexion SSL/TLS avec le serveur Fluentd. La valeur par défaut est de 10 000 millisecondes (10 secondes).<br/> Si `UseTLS` est défini sur false, cette valeur est ignorée. |
| `UseTLS` | Booléen | Indique si le conteneur doit utiliser SSL/TLS pour communiquer avec le serveur Fluentd. La valeur par défaut est false. |