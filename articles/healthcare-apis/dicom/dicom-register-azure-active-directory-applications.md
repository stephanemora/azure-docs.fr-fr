---
title: inscrire des applications Azure Active Directory pour le service DICOM-api de santé Azure
description: cet article décrit l’inscription de Azure Active Directory application pour le service DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1db5ba11d4a3bd8380561ae493c69b809edf5135
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434008"
---
# <a name="register-azure-active-directory-applications-for-the-dicom-service"></a>inscrire des applications Azure Active Directory pour le service DICOM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Vous avez le choix entre plusieurs options lorsque vous configurez le service DICOM ou le serveur FHIR pour Azure (OSS). Pour le projet open source, vous devez créer votre propre inscription d’application de ressource. Pour l’API Azure pour FHIR, cette application de ressource est créée automatiquement.

## <a name="application-registrations"></a>Inscriptions d’applications

Pour qu’une application puisse interagir avec Azure AD, elle doit être inscrite. Dans le contexte du service DICOM, il existe trois types d’inscriptions d’application cliente à aborder.

## <a name="client-applications"></a>Applications clientes

Les applications clientes sont des inscriptions des clients qui vont demander des jetons. Souvent, dans OAuth 2,0, nous faisons la distinction entre au moins trois types d’applications différents.

### <a name="confidential-clients"></a>Clients confidentiels

Les clients confidentiels sont également appelés applications Web dans Azure AD. Les clients confidentiels sont des applications qui utilisent le [flux de code d’autorisation](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) afin d’obtenir un jeton pour le compte d’un utilisateur connecté présentant des informations d’identification valides. elles sont appelées clients confidentiels, car elles peuvent contenir un secret et présenter ce secret à Azure AD lors de l’échange du code d’authentification d’un jeton. Étant donné que les clients confidentiels peuvent s’authentifier à l’aide de la clé secrète client, ils sont approuvés plus que les clients publics et peuvent avoir des jetons plus longs et recevoir un jeton d’actualisation. Pour plus d’informations, consultez [inscrire une application cliente confidentielle dans Azure Active Directory](dicom-register-confidential-client-application.md). Il est important d’enregistrer l’URL de réponse à laquelle le client recevra le code d’autorisation.

### <a name="public-clients"></a>Clients publics

Il s’agit de clients qui ne peuvent pas conserver un secret. En règle générale, il s’agit d’une application d’appareil mobile ou d’une application JavaScript d’une seule page, dans laquelle une clé secrète du client peut être détectée par un utilisateur. Les clients publics utilisent également le flux de code d’autorisation. Toutefois, ils ne sont pas autorisés à présenter un secret au moment de l’obtention d’un jeton. De plus, ils peuvent avoir des jetons dont la durée de vie est plus courte. Il est possible qu’ils ne disposent d’aucun jeton d’actualisation. Lisez les détails sur l' [inscription d’une application cliente publique dans Azure Active Directory](dicom-register-public-application.md).

### <a name="service-clients"></a>Clients de service

Ces clients obtiennent des jetons en leur nom (et non au nom d’un utilisateur) à l’aide du [flux d’informations d’identification du client](.././../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Ils représentent généralement les applications qui accèdent au service DICOM de manière non interactive. Le processus d’ingestion en est l’illustration. Lors de l’utilisation d’un client de service, il n’est pas nécessaire de démarrer le processus d’obtention d’un jeton avec un appel au point de terminaison/Authorize. Un client de service peut accéder directement au point de terminaison/Token et présenter l’ID client et la clé secrète client pour obtenir un jeton. Pour plus d’informations, consultez [inscrire une application cliente de service dans Azure Active Directory](dicom-register-service-client-application.md).

## <a name="next-steps"></a>Étapes suivantes

Cet article de présentation vous a guidé tout au long du processus d’inscription de l’application pour les applications clientes et de ressources afin qu’elles fonctionnent avec le service DICOM. Une fois que vous avez inscrit vos applications, vous pouvez déployer le service DICOM.

>[!div class="nextstepaction"]
>[Inscrire une application cliente confidentielle dans Azure Active Directory](dicom-register-confidential-client-application.md)

>[!div class="nextstepaction"]
>[Inscrire une application cliente publique dans Azure Active Directory](dicom-register-public-application.md)

>[!div class="nextstepaction"]
>[Inscrire une application cliente de service dans Azure Active Directory](dicom-register-service-client-application.md)