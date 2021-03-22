---
title: Exporter des données de coût avec une clé SAP de compte de stockage Azure
description: Cet article aide les partenaires à créer une clé SAP et à configurer les exportations de Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509613"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Exporter des données de coût avec une clé SAP de compte de stockage Azure

Les informations suivantes s’appliquent uniquement aux partenaires Microsoft.

Souvent, les partenaires n’ont pas leurs propres abonnements Azure dans le locataire qui est associé à leur Contrat Partenaire Microsoft. Les partenaires ayant un plan Contrat Partenaire Microsoft et qui sont des administrateurs généraux de leur compte de facturation peuvent exporter et copier les données de coût dans le compte de stockage d’un autre locataire à l’aide d’une clé de service d’accès partagé (SAP). En d’autres termes, un compte de stockage avec une clé SAP permet au partenaire d’utiliser un compte de stockage en dehors de son contrat partenaire pour recevoir les informations exportées. Cet article aide les partenaires à créer une clé SAP et à configurer les exportations de Cost Management.

## <a name="requirements"></a>Configuration requise

- Vous devez être un partenaire avec un Contrat Partenaire Microsoft et avoir des clients sur le plan Azure.
- Vous devez être administrateur général pour le compte de facturation de votre organisation partenaire.
- Vous devez avoir accès à la configuration d’un compte de stockage qui se trouve dans un locataire différent de votre organisation partenaire. Vous êtes responsable de la gestion des autorisations et de l’accès aux données lorsque vous exportez des données vers votre compte de stockage.

## <a name="configure-azure-storage-with-a-sas-key"></a>Configurer Stockage Azure avec une clé SAP

Obtenez un jeton SAP de compte de stockage ou créez-en un à l’aide du portail Azure. Pour en créer un dans le portail Azure, procédez comme suit. Pour en savoir plus sur les clés SAP, consultez [Accorder un accès limité aux données à l’aide des signatures d’accès partagé (SAP)](../../storage/common/storage-sas-overview.md).

1. Accédez au compte de stockage dans le portail Azure.
    - Si votre compte a accès à plusieurs locataires, changez de répertoire pour accéder au compte de stockage. Sélectionnez votre compte dans le coin supérieur droit du portail Azure, puis sélectionnez **Changer de répertoire**.
    - Vous devrez peut-être vous connecter au portail Azure avec le compte de locataire correspondant pour accéder au compte de stockage.
1. Dans le menu de gauche, sélectionnez **Signature d’accès partagé**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Capture d’écran montrant une signature d’accès partagé de stockage Azure configurée." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Configurez le jeton avec les mêmes paramètres que ceux identifiés dans l’image précédente.
    1. Sélectionnez **Blob** pour _Services autorisés_.
    1. Sélectionnez **Service**, **Conteneur** et **Objet** pour _Types de ressources autorisés_.
    1. Sélectionnez **Lire**, **Écrire**, **Supprimer**, **Lister**, **Ajouter** et **Créer** pour _Permissions autorisées_.
    1. Choisissez l’expiration et les dates. Veillez à mettre à jour votre jeton SAP d’exportation avant son expiration. Plus la période que vous configurez avant l’expiration est longue, plus votre exportation fonctionnera longtemps avant de nécessiter un nouveau jeton SAP.
1. Sélectionnez **HTTPS uniquement** pour _Protocoles autorisés_.
1. Sélectionnez **De base** pour _Niveau de routage préféré_.
1. Sélectionnez **key1** pour _Clé de signature_. Si vous permutez ou mettez à jour la clé utilisée pour signer le jeton SAP, vous devez régénérer un nouveau jeton SAP pour votre exportation.
1. Sélectionnez **Générer la chaîne de connexion et SAP**.
    La valeur de **Jeton SAP** indiquée désigne le jeton dont vous avez besoin lorsque vous configurez des exportations.

## <a name="create-a-new-export-with-a-sas-token"></a>Créer une exportation avec un jeton SAP

Accédez à **Exportations** dans l’étendue du compte de facturation et créez une nouvelle exportation en procédant comme suit.

1. Sélectionnez **Create** (Créer).
1. Configurez les détails de l’exportation comme vous le feriez pour une exportation normale. Vous pouvez configurer l’exportation de manière à utiliser un répertoire ou un conteneur existant, ou vous pouvez spécifier un nouveau répertoire ou conteneur et les exportations les créeront pour vous.
1. Quand vous configurez Stockage, sélectionnez **Utiliser un jeton SAP**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Capture d’écran montrant la nouvelle exportation dans laquelle vous sélectionnez le jeton SAP." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Entrez le nom du compte de stockage et collez-le dans votre jeton SAP.
1. Spécifiez un conteneur ou un répertoire existant ou identifiez-en de nouveaux à créer.
1. Sélectionnez **Create** (Créer).

L’exportation basée sur un jeton SAP fonctionne uniquement tant que le jeton reste valide. Réinitialisez le jeton actuel avant son expiration ou votre exportation cessera de fonctionner. Puisque le jeton permet d’accéder à votre compte de stockage, protégez-le aussi soigneusement que vous le feriez pour toute autre information sensible. Vous êtes responsable de la gestion des autorisations et de l’accès aux données lorsque vous exportez des données vers votre compte de stockage.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Résoudre les problèmes d’exportation à l’aide de jetons SAP

Voici les problèmes courants qui peuvent se produire lorsque vous configurez ou utilisez des exportations basées sur un jeton SAP.

- L’option de clé SAP ne s’affiche pas dans le portail Azure.
  - Vérifiez que vous êtes un partenaire ayant un Contrat Partenaire Microsoft et que vous disposez d’une autorisation d’administrateur général pour le compte de facturation. Ce sont les seules personnes qui peuvent exporter avec une clé SAP.

- Vous recevez le message d’erreur suivant lorsque vous tentez de configurer votre exportation :

    **Assurez-vous que le jeton SAP est valide pour le service blob, qu’il est valide pour les types de ressources conteneur et objet et qu’il dispose des autorisations suivantes : ajouter, créer, lire, écrire et supprimer. (Code d’erreur du service de stockage : AuthorizationResourceTypeMismatch)**

    - Assurez-vous que vous configurez et générez correctement la clé SAP dans Stockage Azure.

- Vous ne pouvez plus voir la clé SAP complète après avoir créé une exportation.
  - Ce comportement est normal. Une fois l’exportation SAP configurée, la clé est masquée pour des raisons de sécurité.

- Vous ne pouvez pas accéder au compte de stockage à partir du locataire dans lequel l’exportation est configurée.
  - Ce comportement est normal. Si le compte de stockage se trouve dans un autre locataire, vous devez d’abord accéder à ce locataire dans le portail Azure pour trouver le compte de stockage.

- Votre exportation échoue en raison d’une erreur liée au jeton SAP.
  - Votre exportation fonctionne uniquement tant que le jeton SAP reste valide. Créez une nouvelle clé et exécutez l’exportation.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’exportation des données de Cost Management, consultez [Créer et exporter des données](tutorial-export-acm-data.md).
- Pour plus d’informations sur l’exportation de grandes quantités de données d’utilisation, consultez [Récupérer de grands jeux de données avec des exportations](ingest-azure-usage-at-scale.md).
