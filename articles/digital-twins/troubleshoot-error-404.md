---
title: 'Résolution de l’échec de demande de service : erreur 404 (Sub-Domain not found)'
titleSuffix: Azure Digital Twins
description: Découvrez comment diagnostiquer et résoudre les réponses d’état de l’erreur 404 (Sub-Domain not found) provenant d’Azure Digital Twins.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 9/23/2021
ms.openlocfilehash: 91125140632d689eaab18a74b7da75fe3c69800b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132243"
---
# <a name="troubleshooting-failed-service-request-error-404-sub-domain-not-found"></a>Résolution de l’échec de demande de service : erreur 404 (Sub-Domain not found)

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
* [Sécurité pour les solutions Azure Digital Twins](concepts-security.md)
