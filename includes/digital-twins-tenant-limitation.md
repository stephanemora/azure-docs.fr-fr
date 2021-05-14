---
author: baanders
description: Fichier include décrivant la limitation interlocataire avec Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589333"
---
Par conséquent, les demandes envoyées aux API Azure Digital Twins nécessitent un utilisateur ou un principal de service qui fait partie du même locataire que celui où réside l’instance Azure Digital Twins. Pour empêcher l’analyse malveillante des points de terminaison Azure Digital Twins, les demandes avec des jetons d’accès qui ne proviennent pas du locataire d’origine recevront le message d’erreur « 404 Sous-domaine introuvable ». Cette erreur est retournée *même si* l’utilisateur ou le principal de service a reçu le [rôle](../articles/digital-twins/concepts-security.md) Propriétaire des données Azure Digital Twins ou Lecteur des données Azure Digital Twins par le biais de la collaboration [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md). 