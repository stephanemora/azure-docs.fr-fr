---
title: Ajouter un certificat d’autorité de certification personnalisé - Gestion des API Azure | Microsoft Docs
description: Découvrez comment ajouter un certificat d’autorité de certification personnalisé dans la Gestion des API Azure. Vous pouvez également consulter les instructions pour supprimer un certificat.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: how-to
ms.date: 06/01/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1cb09f24f12d8c4480833995a95e1e08b5e7bbe
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111812216"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Guide pratique pour ajouter un certificat d’autorité de certification personnalisé dans la Gestion des API Azure

La Gestion des API Azure permet d’installer des certificats d’autorité de certification dans les magasins de certificats racines et intermédiaires approuvés, présents sur l’ordinateur. Vous devez utiliser cette fonctionnalité si vos services nécessitent un certificat d’autorité de certification personnalisé.

Cet article montre comment gérer les certificats d’autorité de certification de l’instance de service Gestion des API Azure dans le portail Azure. Par exemple, si vous utilisez des certificats clients auto-signés, vous pouvez charger des certificats racines approuvés personnalisés dans la Gestion des API. 

Les certificats d’autorité de certification chargés dans la Gestion des API peuvent être uniquement utilisés pour la validation des certificats par la passerelle Gestion des API managée. Si vous utilisez la [passerelle auto-hébergée](self-hosted-gateway-overview.md), découvrez comment [créer une autorité de certification personnalisée pour la passerelle auto-hébergée](#create-custom-ca-for-self-hosted-gateway), plus loin dans cet article.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Charger un certificat d’autorité de certification

:::image type="content" source="media/api-management-howto-ca-certificates/00.png" alt-text="Certificats d’autorité de certification dans le portail Azure":::

Suivez les étapes ci-dessous pour charger un nouveau certificat d’autorité de certification. Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez le tutoriel [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

1. Accédez à votre instance de service Gestion des API Azure dans le Portail Azure.

1. Dans le menu, sous **Sécurité**, sélectionnez **Certificats > Autorités de certification > + Ajouter**.

1. Recherchez le fichier de certificat .cer, puis choisissez un magasin de certificats. Seule la clé publique doit être fournie, le mot de passe est donc facultatif.

    :::image type="content" source="media/api-management-howto-ca-certificates/02.png" alt-text="Ajouter un certificat d’autorité de certification dans le portail Azure"::: 

1. Sélectionnez **Enregistrer**. Cette opération peut prendre quelques minutes.

> [!NOTE]
> Vous pouvez également charger un certificat d’autorité de certification à l’aide de la commande PowerShell `New-AzApiManagementSystemCertificate`.

## <a name="delete-a-ca-certificate"></a><a name="step1a"> </a>Supprimer un certificat d’autorité de certification

Sélectionnez le certificat, puis sélectionnez **Supprimer** dans le menu contextuel ( **...** ).

## <a name="create-custom-ca-for-self-hosted-gateway"></a>Créer une autorité de certification personnalisée pour la passerelle auto-hébergée 

Si vous utilisez une [passerelle auto-hébergée](self-hosted-gateway-overview.md), la validation des certificats serveur et client au moyen des certificats racines de l’autorité de certification chargés dans le service Gestion des API n’est pas prise en charge. Pour établir l’approbation, configurez un certificat client particulier afin qu’il soit approuvé par la passerelle en tant qu’autorité de certification personnalisée.

Utilisez les API REST de l’[Autorité de certification de la passerelle](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority) afin de créer et de gérer des autorités de certification personnalisées pour une passerelle auto-hébergée. Pour créer une autorité de certification personnalisée :

1. [Ajoutez un fichier de certificat](api-management-howto-mutual-certificates.md) .pfx à votre instance Gestion des API.
1. Utilisez l’API REST [Autorité de certification de la passerelle - Créer ou mettre à jour](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority/create-or-update) pour associer le certificat à la passerelle auto-gérée.

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
