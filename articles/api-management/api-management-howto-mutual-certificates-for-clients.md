---
title: Sécuriser les API à l’aide d’une authentification par certificat client dans Gestion des API
titleSuffix: Azure API Management
description: Apprenez à sécuriser l’accès aux API à l’aide de certificats clients. Vous pouvez utiliser des expressions de stratégie pour valider des certificats entrants.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2021
ms.author: apimpm
ms.openlocfilehash: d000b9db658c76b5d7cdb586599f04d9078dde5d
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111812154"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Comment sécuriser les API à l'aide d'une authentification par certificat client dans la Gestion des API

La Gestion des API permet de sécuriser l'accès aux API (par ex. client à gestion des API) en utilisant des certificats client. Vous pouvez valider les certificats présentés par le client qui se connecte, et vérifier les propriétés de certificat par rapport aux valeurs souhaitées à l’aide d’expressions de stratégie.

Pour savoir comment sécuriser l’accès au service back-end d’une API à l’aide de certificats clients (par exemple, de la Gestion des API vers le back-end), consultez [Comment sécuriser les services principaux à l’aide d’une authentification par certificat client dans la Gestion des API Azure](./api-management-howto-mutual-certificates.md).

> [!IMPORTANT]
> Pour recevoir et vérifier des certificats clients via HTTP/2 dans les niveaux Developer, Basic, Standard ou Premium, vous devez activer le paramètre « Négocier le certificat client » dans le panneau « Domaines personnalisés », comme indiqué ci-dessous.

![Négocier le certificat client](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Pour recevoir et vérifier des certificats clients dans le niveau Consommation, vous devez activer le paramètre « Demander un certificat client » dans le panneau « Domaines personnalisés », comme indiqué ci-dessous.

![Demander un certificat client](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="policy-to-validate-client-certificates"></a>Stratégie pour valider le certificat client

Utilisez la stratégie [validate-client-certificate](api-management-access-restriction-policies.md#validate-client-certificate) pour valider un ou plusieurs attributs d’un certificat client utilisé pour accéder aux API hébergées dans votre instance Gestion des API.

Configurez la stratégie pour valider un ou plusieurs attributs, notamment l’émetteur du certificat, l’objet, l’empreinte, si le certificat est validé par rapport à la liste de révocation en ligne, etc.

Pour plus d’informations, consultez [Stratégies de restriction des accès de la Gestion des API](api-management-access-restriction-policies.md).

## <a name="certificate-validation-with-context-variables"></a>Validation de certificat avec des variables de contexte

Vous pouvez également créer des expressions de stratégie avec la [`context` variable](api-management-policy-expressions.md#ContextVariables) pour vérifier les certificats clients. Les exemples des sections suivantes montrent des expressions qui utilisent la propriété `context.Request.Certificate` et d’autres propriétés `context`.

> [!IMPORTANT]
> À compter de mai 2021, la propriété `context.Request.Certificate` exige le certificat uniquement lorsque [`hostnameConfiguration`](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) de l’instance de Gestion des API affecte la valeur True à la propriété `negotiateClientCertificate`. Par défaut, la valeur de `negotiateClientCertificate` est définie sur False.

### <a name="checking-the-issuer-and-subject"></a>Vérification de l’émetteur et du sujet

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
> Pour désactiver la vérification de la liste de révocation de certificats, utilisez `context.Request.Certificate.VerifyNoRevocation()` et non `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, le ou les certificats de l’autorité de certification racine (ou intermédiaire) doivent être [chargés](api-management-howto-ca-certificates.md) dans la Gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` pour fonctionner.

### <a name="checking-the-thumbprint"></a>Vérification de l’empreinte numérique

Les stratégies suivantes peuvent être configurées pour vérifier l’empreinte d’un certificat client :

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Pour désactiver la vérification de la liste de révocation de certificats, utilisez `context.Request.Certificate.VerifyNoRevocation()` et non `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, le ou les certificats de l’autorité de certification racine (ou intermédiaire) doivent être [chargés](api-management-howto-ca-certificates.md) dans la Gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` pour fonctionner.

### <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Vérification d’une empreinte par rapport aux certificats téléchargés dans la gestion des API

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
> Pour désactiver la vérification de la liste de révocation de certificats, utilisez `context.Request.Certificate.VerifyNoRevocation()` et non `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, le ou les certificats de l’autorité de certification racine (ou intermédiaire) doivent être [chargés](api-management-howto-ca-certificates.md) dans la Gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` pour fonctionner.

> [!TIP]
> Le problème de blocage de certificat client décrit dans cet [article](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) peut se manifester de différentes manières, notamment par des demandes qui se figent, des demandes qui génèrent un code d’état `403 Forbidden` après une expiration du délai, `context.Request.Certificate` qui a la valeur `null`. Ce problème affecte généralement les demandes `POST` et `PUT` avec une longueur de contenu d’environ 60 Ko ou plus.
> Pour éviter que ce problème ne se reproduise, activez le paramètre « Négocier le certificat client » pour les noms d’hôte souhaités dans le panneau « Domaines personnalisés », comme indiqué dans la première image de ce document. Cette fonctionnalité n’est pas disponible dans le niveau Consommation.

## <a name="next-steps"></a>Étapes suivantes

-   [Comment sécuriser les services principaux à l'aide d'une authentification par certificat client](./api-management-howto-mutual-certificates.md)
-   [Comment télécharger des certificats](./api-management-howto-mutual-certificates.md)
