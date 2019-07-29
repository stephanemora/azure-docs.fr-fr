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
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431254"
---
# <a name="notification-hubs-security"></a>Sécurité de Notification Hubs

## <a name="overview"></a>Vue d'ensemble

Cette rubrique décrit le modèle de sécurité d'Azure Notification Hubs.

## <a name="shared-access-signature-security-sas"></a>Sécurité Signature d'accès partagé (SAP)

Notification Hubs implémente un modèle de sécurité de niveau entité appelé SAP (Signature d'accès partagé). Ce schéma permet aux entités de messagerie de déclarer dans leur description jusqu'à 12 règles d'autorisation accordant des droits sur cette entité.

Chaque règle contient un nom, une valeur clé (secret partagé) et un ensemble de droits, comme expliqué dans [Revendications de sécurité](#security-claims). Lorsque vous créez un hub de notification, deux règles sont automatiquement créée  : une avec des droits d'**écoute** (utilisée par l'application cliente) et l'autre avec **tous** les droits (utilisée par le serveur principal d'application).

Lors de la gestion de l'inscription à partir d’applications clientes, si les informations envoyées via des notifications ne sont pas sensibles (par exemple, des mises à jour météorologiques), une méthode courante pour accéder à un concentrateur de notification consiste à attribuer la valeur clé de la règle d'accès en écoute uniquement à l'application cliente et à attribuer la valeur clé de la règle d’accès complet au serveur principal.

Les applications ne doivent pas incorporer la valeur de la clé dans les applications clientes Windows Store ; le client doit être invité à la récupérer sur le serveur principal d'applications au démarrage.

La clé avec un accès en **écoute** permet à une application cliente de s’inscrire pour n’importe quelle balise. Si votre application doit limiter les inscriptions à des balises spécifiques pour des clients spécifiques (par exemple, lorsque les balises représentent des ID d'utilisateur), votre serveur principal d’application doit effectuer ces inscriptions. Pour plus d'informations, consultez [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md). Notez que de cette façon, l'application cliente ne disposera pas d'un accès direct à Notification Hubs.

## <a name="security-claims"></a>Revendications de sécurité

Comme pour les autres entités, les opérations Notification Hubs sont autorisées pour trois revendications de sécurité : **Écoute**, **Envoyer** et **Gérer**.

| Revendication   | Description                                          | Opérations autorisées |
| ------- | ---------------------------------------------------- | ------------------ |
| Écouter  | Créer/mettre à jour, lire et supprimer des inscriptions uniques | Créer/mettre à jour une inscription<br><br>Lire une inscription<br><br>Lire toutes les inscriptions pour un handle<br><br>Supprimer une inscription |
| Envoyer    | Envoyer de messages au concentrateur de notification                | Envoyer un message |
| gérer  | Opérations CRUD sur Notification Hubs (y compris la mise à jour des informations d'identification PNS et les clés de sécurité) et lecture des inscriptions en fonction des balises |Créer/Mettre à jour/Lire/Supprimer des hubs de notification<br><br>Lire des inscriptions par balise |

Notification Hubs accepte les jetons de signature générés avec des clés partagées configurées directement sur le hub de notification.

Il n’est pas possible d’envoyer une notification à plusieurs espaces de noms. Les espaces de noms constituent un conteneur logique pour les hubs de notification et ne sont pas impliqués dans l'envoi de notifications.
Les stratégies d’accès au niveau de l’espace de noms (informations d’identification) peuvent être utilisées pour les opérations au niveau de l’espace de noms, par exemple : répertorier les hubs de notification, créer ou supprimer des hubs de notification, etc. Seules les stratégies au niveau du hub vous permettent d'envoyer des notifications.
