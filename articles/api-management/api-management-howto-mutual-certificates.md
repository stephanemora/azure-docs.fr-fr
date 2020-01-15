---
title: Sécuriser les services back-end à l’aide d’une authentification par certificat client
titleSuffix: Azure API Management
description: Découvrez comment sécuriser des services principaux à l'aide d'une authentification par certificat client dans la Gestion des API Azure
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 70c1e22fc7f1fb1cda3fd4af1c2d3aa2cd257201
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442652"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Comment sécuriser les services principaux à l'aide d'une authentification par certificat client dans la Gestion des API Azure

La gestion des API permet de sécuriser l’accès au service principal d’une API en utilisant des certificats clients. Ce guide montre comment gérer les certificats de l’instance de service Gestion des API Azure dans le Portail Azure. Il explique également comment configurer une API pour utiliser un certificat et ainsi accéder à un service principal.

Pour en savoir plus sur la gestion des certificats à l’aide de l’API REST de gestion des API, consultez <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Entité de certificat API REST de gestion des API Azure</a>.

## <a name="prerequisites"> </a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ce guide explique comment configurer votre instance de service de gestion des API afin d'utiliser l'authentification par certificat pour accéder au service principal d'une API. Avant de suivre les étapes décrites dans cet article, votre service principal doit être configuré pour l’authentification de certificat client ([pour configurer l’authentification de certificat dans Sites Web Azure, reportez-vous à cet article][to configure certificate authentication in Azure WebSites refer to this article]). Vous devez avoir accès au certificat et au mot de passe pour le charger sur le service Gestion des API.

## <a name="step1"> </a>Charger un certificat

![Ajouter des certificats clients](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Suivez les étapes ci-dessous pour charger un nouveau certificat client. Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez le tutoriel [Créer une instance du service Gestion des API Azure][Create an API Management service instance].

1. Accédez à votre instance de service Gestion des API Azure dans le Portail Azure.
2. Sélectionnez **Certificats** dans le menu.
3. Cliquez sur le bouton **+ Ajouter** .  
    ![Ajouter des certificats clients](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Recherchez le certificat et indiquez son ID et mot de passe.  
5. Cliquez sur **Créer**.

> [!NOTE]
> Le certificat doit être au format **.pfx** . Les certificats auto-signés sont autorisés.

Lorsque le certificat est chargé, il s’affiche dans **Certificats**.  Si vous avez beaucoup de certificats, notez l’empreinte du certificat souhaité pour [configurer une API afin qu’elle utilise un certificat client pour l’authentification de la passerelle][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Pour désactiver la validation des chaînes de certificat lorsque vous utilisez, par exemple, un certificat auto-signé, suivez les étapes décrites dans cet [élément](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) de FAQ.

## <a name="step1a"> </a>Supprimer un certificat client

Pour supprimer un certificat, cliquez sur le menu contextuel **...** , puis sélectionnez **Supprimer** à côté du certificat.

![Supprimer des certificats clients](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Si le certificat est en cours d'utilisation par une API, un écran d'avertissement s'affiche. Pour supprimer le certificat, vous devez d’abord le supprimer de toutes les API configurées pour l’utiliser.

![Échec de suppression des certificats clients](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Configurer une API afin d’utiliser un certificat client pour l’authentification de passerelle

1. Cliquez sur **API** dans le menu **Gestion des API** à gauche, puis recherchez l’API.  
    ![Activer des certificats clients](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Dans l’onglet **Conception**, cliquez sur l’icône de crayon de la section **Principal**. 
3. Modifiez la valeur de **Gateway credentials** (Informations d’identification de passerelle) pour la définir sur **Client cert** (Certi client) et sélectionnez votre certificat dans la liste déroulante.  
    ![Activer des certificats clients](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Cliquez sur **Enregistrer**. 

> [!WARNING]
> Cette modification s'applique immédiatement, et les appels d'opérations de cette API utiliseront désormais ce certificat pour s'authentifier sur le serveur principal.


> [!TIP]
> Lorsqu’un certificat est spécifié pour l’authentification passerelle d’un service principal d’une API, il est intégré à la stratégie de cette API et peut être affiché dans l’éditeur de stratégies.

## <a name="self-signed-certificates"></a>Certificats auto-signés

Si vous utilisez des certificats auto-signés, vous devrez désactiver la validation de chaîne d’approbation afin que le service Gestion des API puisse communiquer avec le système principal. Dans le cas contraire, un code d’erreur 500 est généré. Pour configurer ce paramètre, vous pouvez utiliser les applets de commande PowerShell [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (pour un nouveau serveur principal) ou [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (pour un serveur principal existant) et configurez le paramètre `-SkipCertificateChainValidation` sur `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
