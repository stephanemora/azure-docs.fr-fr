---
title: 'Échec de la requête Azure Digital Twins avec l’état : 404 sous-domaine introuvable'
description: 'Causes et résolutions du message « Échec de la demande de service. État : 404 sous-domaine introuvable » sur Azure Digital Twins.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590539"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>Échec de la demande de service. État : 404 sous-domaine introuvable

Cet article décrit les causes et les étapes de résolution relatives à la réception d’une erreur 404 des demandes de service à Azure Digital Twins. 

## <a name="symptoms"></a>Symptômes

Cette erreur peut se produire lors de l’accès à une instance Azure Digital Twins à l’aide d’un principal de service ou d’un compte d’utilisateur appartenant à un autre [locataire Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) à partir de l’instance. Les [rôles](concepts-security.md) appropriés semblent être affectés à l’identité, mais les demandes d’API échouent avec l’état d’erreur `404 Sub-Domain not found`.

## <a name="causes"></a>Causes

### <a name="cause-1"></a>Cause no 1

Azure Digital Twins nécessite que tous les utilisateurs qui s’authentifient appartiennent au même locataire Azure AD que l’instance Azure Digital Twins.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Solutions

### <a name="solution-1"></a>Solution no 1

Vous pouvez résoudre ce problème en faisant en sorte que chaque identité fédérée d’un autre locataire demande un **jeton** à partir du locataire « d’origine » de l’instance Azure Digital Twins. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Solution no 2

Si vous utilisez la classe `DefaultAzureCredential` dans votre code et que vous continuez à rencontrer ce problème après avoir obtenu un jeton, vous pouvez spécifier le locataire d’origine dans les options `DefaultAzureCredential` pour clarifier le locataire même lorsque l’authentification passe à un autre type par défaut.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la sécurité et les autorisations sur Azure Digital Twins :
* [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md)
