---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f66eac86f4267a7b824eb551cdd877d3d34472a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801766"
---
## <a name="pass-an-id-token-hint"></a>Transmission d’indicateur de jeton d’ID

Une application par partie de confiance peut envoyer un Jeton Web JSON (JWT) entrant dans le cadre de la demande d’autorisation OAuth2.  Le jeton entrant est une indication de l’utilisateur ou de la demande d’autorisation. Azure AD B2C valide le jeton puis extrait la demande.

Pour inclure un indicateur de jeton d’ID dans la requête d’authentification, procédez comme suit :