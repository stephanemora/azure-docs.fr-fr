---
title: Configurer le protocole HTTPS pour votre domaine personnalisé dans une configuration SKU Standard/Premium d’Azure Front Door
description: Dans cet article, vous allez apprendre à intégrer un domaine personnalisé à un SKU Standard/Premium Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740815"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Configurer le protocole HTTPS sur un domaine personnalisé de SKU Standard/Premium (préversion) à l’aide du portail Azure

> [!NOTE]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Azure Front Door Standard/Premium permet un chiffrement TLS sécurisé par défaut à vos applications lorsqu’un domaine personnalisé est ajouté. En utilisant le protocole HTTPS sur votre domaine personnalisé, vous vous assurez que vos données sensibles sont remises en toute sécurité via le chiffrement TLS/SSL lors de l’envoi sur Internet. Lorsque votre navigateur web est connecté à un site web par le biais de HTTPS, ce protocole valide le certificat de sécurité du site et vérifie qu’il est fourni par une autorité de certification légitime. Ce processus assure la sécurité et protège également vos applications web contre les attaques.

Azure Front Door Standard/Premium prend en charge le certificat géré par Azure et les certificats gérés par le client. Par défaut, Azure Front Door active automatiquement le protocole HTTPS sur tous vos domaines personnalisés à l’aide de certificats gérés par Azure. Aucune étape supplémentaire n’est requise pour l’obtention d’un certificat géré par Azure. Un certificat est créé au cours du processus de validation du domaine. Vous pouvez également utiliser votre propre certificat en intégrant Azure Front Door Standard/Premium avec Key Vault.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Avant de pouvoir configurer le protocole HTTPS pour votre domaine personnalisé, vous devez créer un profil Azure Front Door Standard/Premium. Pour plus d’informations, consultez la section [Démarrage rapide : créer un profil Azure Front Door Standard/Premium](create-front-door-portal.md).

* Si vous n’avez pas encore de domaine personnalisé, vous devez tout d’abord en acheter un auprès d’un fournisseur de domaine. Par exemple, consultez [Acheter un nom de domaine personnalisé](../../app-service/manage-custom-dns-buy-domain.md).

* Si vous utilisez Azure pour héberger vos [domaines DNS](../../dns/dns-overview.md), vous devez déléguer le DNS (Domain Name System) du fournisseur de domaine à Azure DNS. Pour plus d’informations, voir [Délégation de domaine à Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Si, au contraire, vous utilisez un fournisseur de domaine pour gérer votre domaine DNS, vous devez valider manuellement le domaine en entrant les enregistrements TXT DNS demandés.

## <a name="azure-managed-certificates"></a>Certificats managés Azure

1. Sous la rubrique Paramètres de votre profil Azure Front Door Standard/Premium, sélectionnez **Domaines**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau domaine.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Capture d’écran de la page d’accueil de la configuration de domaine.":::

1. Sur la page **Ajouter un domaine**, pour *Gestion DNS*, sélectionnez l’option **DNS managé par Azure**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Capture d’écran de la page Ajouter un domaine avec le DNS managé par Azure sélectionné.":::

1. Validez le domaine personnalisé et associez-le à un point de terminaison en suivant les étapes de la section activation d’un [domaine personnalisé](how-to-add-custom-domain.md).

1. Une fois que le domaine personnalisé a été associé au point de terminaison, un certificat managé par Azure est déployé sur Front Door. Ce processus peut prendre quelques minutes.

## <a name="using-your-own-certificate"></a>Utiliser votre propre certificat

Vous pouvez également choisir d’utiliser votre propre certificat TLS. Ce certificat doit être importé dans un Azure Key Vault avant de pouvoir être utilisé avec Azure Front Door Standard/Premium. Consultez [Importer un certificat](../../key-vault/certificates/tutorial-import-certificate.md) dans Azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Préparer votre compte et votre certificat Azure Key Vault
 
1. Vous devez disposer d’un compte Azure Key Vault en cours d’exécution sous le même abonnement que Azure Front Door Standard/Premium pour activer le protocole HTTPS personnalisé. Si ce n’est déjà fait, créez un compte Azure Key Vault.

    > [!WARNING]
    > Azure Front Door ne prend actuellement en charge que les comptes Key Vault dans le même abonnement que la configuration Front Door. Choisir un Key Vault sous un autre abonnement que votre celui d’Azure Front Door Standard/Premium entraînera un échec.

1. Si vous avez déjà un certificat, chargez-le directement sur votre compte Azure Key Vault. Dans le cas contraire, créez un certificat directement par le biais d’Azure Key Vault à partir de l’une des autorités de certification du partenaire qu’Azure Key Vault intègre. Chargez votre certificat en tant qu’objet de **certificat** et non en tant que **secret**.

    > [!NOTE]
    > Pour votre propre certificat TLS/SSL, Front Door ne prend pas en charge les certificats avec des algorithmes de chiffrement EC.

#### <a name="register-azure-front-door"></a>Inscrire Azure Front Door

Inscrivez le principal du service pour Azure Front Door en tant qu’application dans Azure Active Directory par le biais de PowerShell.

> [!NOTE]
> Cette action nécessite des autorisations d’administrateur général et doit être exécutée **une seule fois** par locataire.

1. Si nécessaire, installez [Azure PowerShell](/powershell/azure/install-az-ps) dans PowerShell sur votre ordinateur local.

1. Dans PowerShell, exécutez la commande suivante :

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Accorder à Azure Front Door l’accès à votre coffre de clés
 
Accordez à Azure Front Door l’autorisation d’accéder aux certificats de votre compte Azure Key Vault.

1. Dans votre compte Key Vault, sous PARAMÈTRES, sélectionnez **Stratégies d’accès**. Sélectionnez ensuite **Ajouter** pour créer une nouvelle stratégie.

1. Dans **Sélectionner principal**, recherchez **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** et choisissez **Microsoft.AzureFrontDoor-Cdn**. Cliquez sur **Sélectionner**.

1. Sous **Autorisations du secret**, sélectionnez **Obtenir** pour permettre à Front Door de récupérer le certificat.

1. Sous **Autorisations de certificat**, sélectionnez **Obtenir** pour permettre à Front Door de récupérer le certificat.

1. Sélectionnez **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Sélectionner le certificat à déployer pour Azure Front Door
 
1. Revenez à Azure Front Door Standard/Premium dans le portail.

1. Accédez à **Secrets** sous *Paramètres*, puis sélectionnez **Ajouter un certificat**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Capture d’écran de la page d’arrivée d’Azure Front Door.":::

1. Sur la page **Ajouter un certificat**, activez la case à cocher du certificat que vous souhaitez ajouter à Azure Front Door Standard/Premium. Laissez la sélection de version « La plus récente » et sélectionnez **Ajouter**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Capture d’écran de la page d’ajout d’un certificat.":::

1. Une fois que le certificat est correctement approvisionné, vous pouvez l’utiliser lorsque vous ajoutez un nouveau domaine personnalisé.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Capture d’écran du certificat correctement ajouté aux secrets.":::

1. Accédez à **Domaines** sous *Paramètre*, puis sélectionnez **+ Ajouter** pour ajouter un nouveau domaine personnalisé. Sur la page **Ajouter un domaine**, choisissez « Apportez votre propre certificat (BYOC) » pour *HTTPS*. Pour *Secret*, sélectionnez le certificat que vous souhaitez utiliser dans la liste déroulante. 

    > [!NOTE]
    > Le certificat sélectionné doit avoir un nom commun identique au domaine personnalisé ajouté.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Capture d’écran de l’ajout d’une page de domaine personnalisé avec HTTPS.":::

1. Suivez les étapes de l’écran pour valider le certificat. Associez ensuite le domaine personnalisé que vous venez de créer à un point de terminaison, comme indiqué dans le [guide de création d’un domaine personnalisé](how-to-add-custom-domain.md).

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Passer de la gestion Azure à « Apportez votre propre certificat » (BYOC)

1. Vous pouvez modifier un certificat managé par Azure existant en certificat managé par l’utilisateur en sélectionnant l’état du certificat pour ouvrir la page **Détails du certificat**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Capture d’écran de l’état du certificat sur la page d’arrivée des domaines." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Sur la page **Détails du certificat**, vous pouvez passer de l’option « Managé par Azure » à l’option « Apportez votre propre certificat (BYOC) ». Suivez ensuite les mêmes étapes que précédemment pour choisir un certificat. Sélectionnez **Mettre à jour** pour modifier le certificat associé à l’aide d’un domaine.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Capture d’écran de la page des détails du certificat.":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez [la mise en cache Azure Front Door Standard/Premium](concept-caching.md).
