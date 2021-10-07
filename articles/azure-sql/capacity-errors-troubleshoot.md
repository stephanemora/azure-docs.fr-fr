---
title: Résoudre des erreurs de capacité avec des ressources Azure SQL
description: Découvrez comment résoudre des erreurs de capacité que vous pouvez rencontrer lorsque vous tentez de déployer ou de mettre à l’échelle des ressources Azure SQL Database ou Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 09/03/2021
ms.custom: references_regions
ms.openlocfilehash: 885be735055eaf65d67466694e65d6cf7fdf00da
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123482027"
---
# <a name="resolve-capacity-errors-with-azure-sql-database-or-azure-sql-managed-instance"></a>Résoudre des erreurs de capacité avec Azure SQL Database ou Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Cet article explique comment résoudre des erreurs de capacité lors du déploiement de ressources Azure SQL Database ou Azure SQL Managed Instance. 

## <a name="exceeded-quota"></a>Quota dépassé 

Si vous rencontrez l’une des erreurs suivantes en tentant de déployer votre ressource Azure SQL, [demandez à augmenter votre quota](database/quota-increase-request.md) : 

- `Server quota limit has been reached for this location. Please select a different location with lower server count.`
- `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx.`
- Au cours d’une opération de mise à l’échelle, vous pouvez rencontrer l’erreur suivante :    
  `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx. `. 

## <a name="subscription-access"></a>Accès aux abonnements

Il se peut que votre abonnement n’ait pas accès à la création d’un serveur dans la région sélectionnée si votre abonnement n’a pas été inscrit auprès du fournisseur de ressources SQL.  

Si vous rencontrez les erreurs suivantes, [inscrivez votre abonnement auprès du fournisseur de ressources SQL](#register-with-sql-rp) :
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`


## <a name="enable-region"></a>Activer la région 

Il se peut que votre abonnement n’ait pas accès à la création d’un serveur dans la région sélectionnée si celle-ci n’a pas été activée. Pour résoudre ce problème, soumettez une [demande d’assistance pour activer une région spécifique](database/quota-increase-request.md#region) pour votre abonnement. 

Si vous rencontrez les erreurs suivantes, ouvrez un ticket de support pour activer une région spécifique : 
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`



## <a name="register-with-sql-rp"></a>S’inscrire auprès du fournisseur de ressources SQL

Pour déployer des ressources Azure SQL, inscrivez votre abonnement auprès du fournisseur de ressources SQL. 

Vous pouvez inscrire votre abonnement à l’aide du portail Azure, d’[Azure CLI](/cli/azure/install-azure-cli) ou d’[Azure PowerShell](/powershell/azure/install-az-ps). 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour inscrire votre abonnement dans le portail Azure, procédez comme suit : 

1. Ouvrez le portail Azure et accédez à **Tous les services**.
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.
1. Sur la page **Abonnements**, sélectionnez **Fournisseurs de ressources** sous **Paramètres**.
1. Entrez **sql** dans le filtre pour afficher les extensions liées à SQL.
1. Sélectionnez **Inscrire**, **Réinscrire** ou **Désinscrire** pour le fournisseur **Microsoft.Sql**, selon l’action souhaitée.

   ![Modifier le fournisseur](./media/capacity-errors-troubleshoot/register-with-sql-rp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Pour inscrire votre abonnement à l’aide d’[Azure CLI](/cli/azure/install-azure-cli), exécutez la cmdlet suivante :

```azurecli-interactive
# Register the SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMac 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Pour inscrire votre abonnement à l’aide d’[Azure PowerShell](/powershell/azure/install-az-ps), exécutez la cmdlet suivante : 

```powershell-interactive
# Register the SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.Sql

```

---

## <a name="additional-provisioning-issues"></a>Autres problèmes d’approvisionnement

Si vous continuez de rencontrer des problèmes d’approvisionnement, ouvrez une demande d’accès à une **Région** via la rubrique de support de SQL Database, en spécifiant le nombre de DTU ou de vCore que vous souhaitez consommer sur Azure SQL Database ou Azure SQL Managed Instance. 

## <a name="azure-program-regions"></a>Régions du programme Azure 

Les offres du Programme Azure (Pass Azure, Imagine, Azure for Students, MPN, BizSpark, BizSpark Plus, Microsoft for Startups/Offres de parrainage, Abonnements Visual Studio/MSDN) ont accès à un ensemble limité de régions. 

Si votre abonnement fait partie d’une offre du Programme Azure et si vous souhaitez demander l’accès à l’une des régions suivantes, envisagez d’utiliser plutôt une autre région : 

_Afrique du Sud Ouest, Allemagne Nord, Australie Centre, Australie Centre 2, Australie Sud-Est, Brésil Sud-Est, Canada Est, Chine Est, Chine Nord, Chine Nord 2, Corée Sud, Émirats arabes unis Centre, France Sud, Inde Centre JIO, Inde Ouest, Inde Ouest JIO, Inde Sud, Japon Ouest, Norvège Ouest, Royaume-Uni Ouest, Suisse Ouest, USA Centre-Ouest, US DoD Centre, US DoD Est, US Gov Arizona, US Gov Texas._ 

## <a name="next-steps"></a>Étapes suivantes

Une fois votre demande envoyée, elle est examinée. Vous recevrez une réponse basée sur les informations que vous avez fournies dans le formulaire.

Pour plus d’informations sur les autres limites d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-resource-manager/management/azure-subscription-service-limits.md).
