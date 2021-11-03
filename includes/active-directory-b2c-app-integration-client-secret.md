---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 80f8f4080b068d840460dfd9acb811174c267d04
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007778"
---
Créez une clé secrète client pour l’application web inscrite. L’application Web utilise la clé secrète client pour prouver son identité lorsqu’elle demande des jetons.

1. Sous **Gérer**, sélectionnez **Certificats et secrets**.
1. Sélectionnez **Nouveau secret client**.
1. Entrez une description pour la clé secrète client dans la zone **Description** (par exemple, *clientsecret1*).
1. Sous **Expire**, sélectionnez une durée pendant laquelle le secret est valide, puis sélectionnez **Ajouter**.
1. Enregistrez la **Valeur** du secret. Vous utiliserez cette valeur pour la configuration dans une étape ultérieure.