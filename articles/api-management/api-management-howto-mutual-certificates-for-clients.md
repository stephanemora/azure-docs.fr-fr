---
title: Sécuriser les API avec l’authentification de certificat client dans la gestion des API - Gestion des API Azure | Microsoft Docs
description: Apprenez à sécuriser l’accès aux API à l’aide des certificats clients
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427353"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Comment sécuriser les API à l'aide d'une authentification par certificat client dans la Gestion des API

La Gestion des API permet de sécuriser l'accès aux API (par ex. client à gestion des API) en utilisant des certificats client. Vous pouvez valider le certificat entrant et vérifier les propriétés du certificat sur les valeurs souhaitées à l’aide d’expressions de stratégie.

Pour plus d’informations sur la sécurisation des accès pour le service back-end d’une API à l’aide de certificats clients (par exemple, gestion des API au serveur principal), consultez [comment sécuriser les services principaux à l’aide du client de l’authentification par certificat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Pour recevoir et vérifier les certificats de client dans le niveau de consommation, vous devez tout d’abord activer sur « Certificat de client demande « définition sur le panneau « Domaines personnalisés » comme indiqué ci-dessous.

![Demander le certificat client](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Vérification de l’émetteur et du sujet

Les stratégies suivantes peuvent être configurées pour vérifier l’émetteur et le sujet d’un certificat client :

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Pour désactiver la vérification utilisation de liste de révocation de certificats `context.Request.Certificate.VerifyNoRevocation()` au lieu de `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, racine (ou intermédiaire) ou les certificats d’autorité de certification doivent être [téléchargé](api-management-howto-ca-certificates.md) à la gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` fonctionne.

## <a name="checking-the-thumbprint"></a>Vérification de l’empreinte numérique

Les stratégies suivantes peuvent être configurées pour vérifier l’empreinte d’un certificat client :

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Pour désactiver la vérification utilisation de liste de révocation de certificats `context.Request.Certificate.VerifyNoRevocation()` au lieu de `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, racine (ou intermédiaire) ou les certificats d’autorité de certification doivent être [téléchargé](api-management-howto-ca-certificates.md) à la gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` fonctionne.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Vérification d’une empreinte par rapport aux certificats téléchargés dans la gestion des API

L’exemple suivant montre comment vérifier l’empreinte d’un certificat client par rapport aux certificats téléchargés dans la gestion des API :

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Pour désactiver la vérification utilisation de liste de révocation de certificats `context.Request.Certificate.VerifyNoRevocation()` au lieu de `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, racine (ou intermédiaire) ou les certificats d’autorité de certification doivent être [téléchargé](api-management-howto-ca-certificates.md) à la gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` fonctionne.

> [!TIP]
> Problème de blocage de certificat client décrit dans cet [article](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) peut se manifester de différentes manières, par exemple, les demandes figer, résultats de requêtes `403 Forbidden` code d’état après expiration du délai, `context.Request.Certificate` est `null`. Ce problème affecte généralement `POST` et `PUT` demandes avec la longueur du contenu d’environ 60 Ko ou plus.
> Pour éviter ce problème se produise activer paramètre « Certificat de client Negotiate » pour les noms d’hôte souhaité dans le panneau « Domaines personnalisés » comme indiqué ci-dessous. Cette fonctionnalité n’est pas disponible dans le niveau de consommation.

![Négocier le certificat client](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Étapes suivantes

-   [Comment sécuriser les services principaux à l'aide d'une authentification par certificat client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Comment télécharger des certificats](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
