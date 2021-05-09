---
author: baanders
description: Fichier include décrivant une solution à jeton pour la limitation interlocataire avec Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 08c48b3600a975f43a5ad94e2cc6efd88047d0d8
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205650"
---
Une façon de le faire est d’utiliser la commande CLI suivante, où `<home-tenant-ID>` est l’ID du locataire Azure AD qui contient l’instance Azure Digital Twins :

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Après avoir fait cette demande, l’identité recevra un jeton émis pour la ressource Azure AD `https://digitaltwins.azure.net` , qui a une revendication d’ID de locataire correspondant à l’instance Azure Digital Twins. L’utilisation de ce jeton dans les demandes d’API ou avec votre code `Azure.Identity` doit permettre à l’identité fédérée d’accéder à la ressource Azure Digital Twins.
