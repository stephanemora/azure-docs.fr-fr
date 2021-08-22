---
title: Fichier Include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef0ad097f0dff99de71096cb24eba511a21bf06a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122262021"
---
## <a name="protect-your-access-keys"></a>Protégez vos clés d’accès

Vos clés d’accès de compte de stockage sont similaires au mot de passe racine pour votre compte de stockage. Veillez toujours à protéger vos clés d’accès. Utilisez Azure Key Vault pour gérer et effectuer la rotation de vos clés en toute sécurité. Évitez de distribuer des clés d’accès à d’autres utilisateurs, de les coder en dur ou de les enregistrer en texte brut dans un emplacement accessible à d’autres personnes. Effectuez une rotation de vos clés si vous pensez qu’elles ont pu être compromises.

> [!NOTE]
> Microsoft recommande d’utiliser dans la mesure du possible Azure Active Directory (Azure AD) plutôt que des clés partagées pour autoriser les demandes effectuées sur les données Blob et File d’attente. Azure AD offre une sécurité et une facilité d’utilisation supérieures sur les clés partagées. Pour plus d’informations sur l’autorisation de l’accès aux données avec Azure AD, consultez [Autoriser l’accès aux objets blob et files d’attente Azure à l’aide d’Azure Active Directory](../articles/storage/blobs/authorize-access-azure-active-directory.md).