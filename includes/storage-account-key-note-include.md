---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460564"
---
## <a name="protect-your-access-keys"></a>Protégez vos clés d’accès

Vos clés d’accès de compte de stockage sont similaires au mot de passe racine pour votre compte de stockage. Veillez toujours à protéger vos clés d’accès. Utilisez Azure Key Vault pour gérer et effectuer la rotation de vos clés en toute sécurité. Évitez de distribuer des clés d’accès à d’autres utilisateurs, de les coder en dur ou de les enregistrer en texte brut dans un emplacement accessible à d’autres personnes. Effectuez une rotation de vos clés si vous pensez qu’elles ont pu être compromises.

Si possible, utilisez Azure Active Directory (Azure AD) pour autoriser les demandes au stockage Blob et File d’attente au lieu des clés partagées. Azure AD offre une sécurité et une facilité d’utilisation supérieures sur les clés partagées. Pour plus d’informations sur l’autorisation de l’accès aux données avec Azure AD, consultez [Autoriser l’accès aux objets blob et files d’attente Azure à l’aide d’Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
