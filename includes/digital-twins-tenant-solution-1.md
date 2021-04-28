---
author: baanders
description: Fichier include décrivant une solution à jeton pour la limitation interlocataire avec Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589330"
---
Une façon de le faire est d’utiliser la commande CLI suivante, où `<home-tenant-ID>` est l’ID du locataire Azure AD qui contient l’instance Azure Digital Twins :

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Après avoir fait cette demande, l’identité recevra un jeton émis pour la ressource Azure AD *https://digitaltwins.azure.net* , qui a une revendication d’ID de locataire correspondant à l’instance Azure Digital Twins. L’utilisation de ce jeton dans les demandes d’API ou avec votre code `Azure.Identity` doit permettre à l’identité fédérée d’accéder à la ressource Azure Digital Twins.