---
title: Détails du proxy DNS du Pare-feu Azure
description: Découvrez comment fonctionne le proxy DNS du Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: victorh
ms.openlocfilehash: f6f17823b51200c21ee0975f50487ba1e3f58ced
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547676"
---
# <a name="azure-firewall-dns-proxy-details"></a>Détails du proxy DNS du Pare-feu Azure

Vous pouvez configurer le Pare-feu Azure pour qu’il agisse comme proxy DNS. Un proxy DNS est un intermédiaire pour les requêtes DNS entre des machines virtuelles clientes et un serveur DNS.

Les informations suivantes décrivent certains aspects de l’implémentation du proxy DNS du Pare-feu Azure.

## <a name="fqdns-with-multiple-a-records"></a>Noms de domaine complets avec plusieurs enregistrements A

Le Pare-feu Azure fonctionne comme un client DNS standard. Si la réponse comporte plusieurs enregistrements A, il les stocke tous dans le cache. S’il existe un enregistrement par réponse, il ne stocke qu’un seul enregistrement. Il n’existe aucun moyen pour le client de savoir à l’avance s’il doit s’attendre à un ou plusieurs enregistrements A dans les réponses.

## <a name="fqdn-time-to-live-ttl"></a>Durée de vie du nom de domaine complet

Lorsque la durée de vie (TTL, Time To Live) d’un nom de domaine complet est sur le point d’expirer, les enregistrements sont mis en cache et expirent en fonction de leur durée de vie. La prérécupération n’est pas utilisée. Par conséquent, le pare-feu n’effectue pas de recherche avant l’expiration de la durée de vie pour actualiser l’enregistrement.

## <a name="clients-not-configured-to-use-the-firewall-dns-proxy"></a>Clients non configurés pour utiliser le proxy DNS du pare-feu

Si un ordinateur client est configuré pour utiliser un serveur DNS différent du proxy DNS du pare-feu, les résultats peuvent être imprévisibles.

Prenons par exemple une charge de travail client située dans la région USA Est qui utilise un serveur DNS principal hébergé dans la même région. Les paramètres du serveur DNS du Pare-feu Azure sont configurés pour un serveur DNS secondaire hébergé dans la région USA Ouest. Le serveur DNS du pare-feu hébergé dans la région USA Ouest produit une réponse différente de celle du client qui se trouve dans la région USA Est.

Il s’agit d’un scénario courant, qui explique pourquoi les clients doivent utiliser la fonctionnalité de proxy DNS du pare-feu. Servez-vous du pare-feu comme programme de résolution si vous employez des noms de domaine complets dans les règles de réseau. Vous pouvez ainsi garantir la cohérence de la résolution des adresses IP par les clients et le pare-feu lui-même.

Dans cet exemple, si un nom de domaine complet est configuré dans les règles de réseau, le pare-feu résout le nom de domaine complet en IP1 (adresse IP 1) et met à jour les règles de réseau pour autoriser l’accès à IP1. Si le client résout le même nom de domaine complet en IP2 en raison d’une différence de réponse DNS, sa tentative de connexion est refusée. En effet, elle ne correspond pas aux règles du pare-feu. 

En ce qui concerne les noms de domaine complets HTTP/S configurés dans les règles d’application, le pare-feu les analyse à partir de l’en-tête de l’hôte ou de l’en-tête SNI, les résout, puis se connecte à l’adresse IP obtenue. L’adresse IP de destination à laquelle le client tente de se connecter est ignorée.

## <a name="next-steps"></a>Étapes suivantes

- [Paramètres DNS du Pare-feu Azure](dns-settings.md)