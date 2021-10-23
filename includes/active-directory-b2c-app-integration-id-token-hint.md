---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: bf7295f4cc7b2010e7c700fbde25840f7a7f0df1
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037088"
---
## <a name="pass-an-id-token-hint"></a>Transmission d’indicateur de jeton d’ID

Une application par partie de confiance peut envoyer un Jeton Web JSON (JWT) entrant dans le cadre de la demande d’autorisation OAuth2.  Le jeton entrant est une indication de l’utilisateur ou de la demande d’autorisation. Azure AD B2C valide le jeton puis extrait la demande.

Pour inclure un indicateur de jeton d’ID dans la requête d’authentification, procédez comme suit :