---
title: Sécurité de Notification Hubs
description: Cette rubrique décrit la sécurité des hubs de notification Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/23/2019
ms.author: jowargo
ms.openlocfilehash: 2ba3be0d51758cf7afd9f85258403bf79ca8401f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239414"
---
# <a name="security-model-of-azure-notification-hubs"></a>Modèle de sécurité d’Azure Notification Hubs

## <a name="overview"></a>Vue d'ensemble

Cette rubrique décrit le modèle de sécurité d'Azure Notification Hubs. Notification Hubs étant une entité Service Bus, elle implémente le même modèle de sécurité que Service Bus. Pour plus d'informations, consultez les rubriques [Authentification Service Bus](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

## <a name="shared-access-signature-security-sas"></a>Sécurité Signature d'accès partagé (SAP)

Notification Hubs implémente un modèle de sécurité de niveau entité appelé SAP (Signature d'accès partagé). Ce schéma permet aux entités de messagerie de déclarer dans leur description jusqu'à 12 règles d'autorisation accordant des droits sur cette entité.

Chaque règle contient un nom, une valeur clé (secret partagé) et un ensemble de droits, comme expliqué dans la section « Revendications de sécurité ». Lorsque vous créez un concentrateur de notification, deux règles sont automatiquement créées : une avec des droits d'écoute (utilisée par l'application cliente) et l'autre avec tous les droits (utilisé par le serveur principal d'application).

Lors de la gestion de l'inscription à partir d’applications clientes, si les informations envoyées via des notifications ne sont pas sensibles (par exemple, des mises à jour météorologiques), une méthode courante pour accéder à un concentrateur de notification consiste à attribuer la valeur clé de la règle d'accès en écoute uniquement à l'application cliente et à attribuer la valeur clé de la règle d’accès complet au serveur principal.

Il est déconseillé d'incorporer la valeur clé dans les applications clientes Windows Store. Pour éviter l'incorporation de la valeur clé, l'application cliente peut la récupérer à partir du serveur principal d'application au démarrage.

Il est important de comprendre que la clé avec un accès en écoute permet à une application cliente de s’inscrire pour n’importe quelle balise. Si votre application doit limiter les inscriptions à des balises spécifiques pour des clients spécifiques (par exemple, lorsque les balises représentent des ID utilisateur), votre serveur principal d’application doit effectuer ces inscriptions. Pour plus d'informations, consultez Gestion des inscriptions. Notez que de cette façon, l'application cliente ne disposera pas d'un accès direct à Notification Hubs.

## <a name="security-claims"></a>Revendications de sécurité

Comme pour les autres entités, les opérations Notification Hubs sont autorisées pour trois revendications de sécurité : Écoute, Envoyer et Gérer.

| Revendication   | Description                                          | Opérations autorisées |
| ------- | ---------------------------------------------------- | ------------------ |
| Écouter  | Créer/mettre à jour, lire et supprimer des inscriptions uniques | Créer/mettre à jour une inscription<br><br>Lire une inscription<br><br>Lire toutes les inscriptions pour un handle<br><br>Supprimer une inscription |
| Envoyer    | Envoyer de messages au concentrateur de notification                | Envoyer un message |
| gérer  | Opérations CRUD sur Notification Hubs (y compris la mise à jour des informations d'identification PNS et les clés de sécurité) et lecture des inscriptions en fonction des balises |Créer/Mettre à jour/Lire/Supprimer des hubs de notification<br><br>Lire des inscriptions par balise |

Notification Hubs accepte la signature de jetons générés avec partagé clés configurées directement sur le concentrateur de Notification.

Il n’est pas possible d’envoyer une notification à plus d’un espace de noms. Espaces de noms sont un conteneur logique pour les concentrateurs de notification et ne sont pas impliqués avec envoi de notifications.
Les stratégies d’accès au niveau de l’espace de noms (informations d’identification) peuvent être utilisées pour les opérations au niveau de l’espace de noms, par exemple : répertorier les concentrateurs de notification, la création ou suppression de hubs de notification, etc. Uniquement les stratégies d’accès au niveau du hub vous permet de vous envoyer des notifications.
