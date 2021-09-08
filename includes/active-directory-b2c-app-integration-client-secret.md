---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 18120c126fafd03b6bb91901b33f8932644079c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641327"
---
Créez une clé secrète client pour l’application web inscrite. L’application Web utilise la clé secrète client pour prouver son identité lorsqu’elle demande des jetons.

1. Sous **Gérer**, sélectionnez **Certificats et secrets**.
1. Sélectionnez **Nouveau secret client**.
1. Entrez une description pour la clé secrète client dans la zone **Description** (par exemple, *clientsecret1*).
1. Sous **Expire**, sélectionnez une durée pendant laquelle le secret est valide, puis sélectionnez **Ajouter**.
1. Enregistrez la **Valeur** du secret. Vous utiliserez cette valeur pour la configuration dans une étape ultérieure.