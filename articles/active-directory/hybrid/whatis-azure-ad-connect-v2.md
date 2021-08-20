---
title: Qu’est-ce qu’Azure AD Connect v2.0 ? | Microsoft Docs
description: En savoir plus sur la prochaine version d’Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/24/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c117e7f1a5d605e1abe42474b24b8fbf6491c43
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481368"
---
# <a name="introduction-to-azure-ad-connect-v20"></a>Présentation d’Azure AD Connect v 2.0 

Azure AD Connect a été publié il y a plusieurs années.  Depuis lors, plusieurs des composants utilisés par Azure AD Connect ont été déconseillés et mis à jour vers des versions plus récentes.  Tenter de mettre à jour tous ces composants individuellement demanderait du temps et de la planification.  

Pour remédier à ce problème, nous avons voulu regrouper un maximum de ces nouveaux composants dans une nouvelle version unique, de sorte que vous ne devez effectuer qu’une seule mise à jour. Cette version sera Azure AD Connect v2.0.  Il s’agit d’une nouvelle version du même logiciel utilisé pour atteindre vos objectifs en matière d’identité hybride et qui est construite à l’aide des composants fondamentaux les plus récents. 

## <a name="what-are-the-major-changes"></a>Quelles sont les principales modifications apportées ? 

### <a name="sql-server-2019-localdb"></a>SQL Server 2019 Base de données locale 

Les versions précédentes d’Azure AD Connect étaient livrées avec SQL Server 2012 Base de données locale. La version 2.0 est livrée avec SQL Server 2019 Base de données locale, qui promet une stabilité et des performances accrues et comporte plusieurs correctifs de bogues liés à la sécurité. SQL Server 2012 ne fera plus l’objet d’un support étendu en juillet 2022. Pour plus d’informations, consultez [Microsoft SQL 2019](https://www.microsoft.com/sql-server/sql-server-2019).

### <a name="msal-authentication-library"></a>Bibliothèque d’authentification MSAL 

Les versions précédentes d’Azure AD Connect étaient livrées avec la bibliothèque d’authentification ADAL. Cette bibliothèque sera dépréciée en juin 2022. La version 2.0 est livrée avec la bibliothèque MSAL, plus récente. Pour plus d’informations, consultez [Vue d’ensemble de la bibliothèque MSAL](../../active-directory/develop/msal-overview.md).

### <a name="visual-c-redist-14"></a>Visual C++ Redist 14 

SQL Server 2019 requiert le runtime Visual C++ Redist 14. Nous mettons donc à jour la bibliothèque du runtime C++ pour utiliser cette version. Celle-ci sera installée avec le package Azure AD Connect v2.0. Vous n’avez donc aucune action à entreprendre pour la mise à jour du runtime C++. 

### <a name="tls-12"></a>TLS 1.2 

TLS 1.0 et TLS 1.1 sont des protocoles jugés peu sûrs et sont déconseillés par Microsoft. Cette version d’Azure AD Connect prend en charge uniquement TLS 1.2. Si votre serveur ne prend pas en charge TLS 1.2, vous devez l’activer avant de pouvoir déployer Azure AD Connect v2.0. Pour plus d’informations, consultez [Application de TLS 1.2 pour Azure AD Connect](reference-connect-tls-enforcement.md).

### <a name="all-binaries-signed-with-sha2"></a>Tous les binaires signés avec SHA-2 

Nous avons remarqué que certains composants avaient des binaires signés SHA-1. Nous ne prenons plus en charge SHA-1 pour les binaires téléchargeables et nous avons mis à niveau tous les binaires avec la signature SHA-2. Les signatures numériques sont utilisées pour garantir que les mises à jour proviennent directement de Microsoft et qu’elles n’ont pas été falsifiées pendant la livraison. En raison des faiblesses de l’algorithme SHA-1 et pour nous aligner sur les normes du secteur, nous avons modifié la signature des mises à jour de Windows pour utiliser l’algorithme SHA-2, plus sûr.  

Aucune action n’est nécessaire de votre part. 

### <a name="windows-server-2012-and-windows-server-2012-r2-are-no-longer-supported"></a>Windows Server 2012 et Windows Server 2012 R2 ne sont plus pris en charge 

SQL Server 2019 requiert Windows Server 2016 ou une version plus récente comme système d’exploitation de serveur. Dans la mesure où AAD Connect v2 contient des composants SQL Server 2019, nous ne pouvons plus prendre en charge les versions antérieures de Windows Server.  

Vous ne pouvez pas installer cette version sur une version antérieure de Windows Server. Nous vous suggérons de mettre à niveau votre serveur Azure AD Connect vers Windows Server 2019, qui est la version la plus récente du système d’exploitation Windows Server. 

Cet [article](https://docs.microsoft.com/windows-server/get-started-19/install-upgrade-migrate-19) décrit la mise à niveau de versions antérieures de Windows Server vers Windows Server 2019. 

### <a name="powershell-50"></a>PowerShell 5.0 

Cette version d’Azure AD Connect contient plusieurs cmdlets qui nécessitent PowerShell 5.0. Cette exigence est donc un nouveau prérequis pour Azure AD Connect.  

Vous trouverez plus d’informations sur les prérequis de PowerShell [ici](https://docs.microsoft.com/powershell/scripting/windows-powershell/install/windows-powershell-system-requirements?view=powershell-7.1#windows-powershell-50).

 >[!NOTE]
 >PowerShell 5 fait déjà partie de Windows Server 2016. Vous n’avez donc probablement pas besoin d’agir tant que vous utilisez une version récente de Window Server. 

## <a name="what-else-do-i-need-to-know"></a>Que dois-je savoir d’autre ? 


**Pourquoi cette mise à niveau est-elle importante pour moi ?** </br>
L’année prochaine, plusieurs des composants de vos installations actuelles de serveurs Azure AD Connect ne seront plus pris en charge. Si vous utilisez des produits non pris en charge, il sera plus difficile pour notre équipe de support de vous fournir l’expérience de support dont votre organisation a besoin. Nous recommandons donc à tous les clients de mettre à niveau vers cette version plus récente dès que possible. 

Cette mise à niveau est d’autant plus importante que nous avons dû mettre à jour nos prérequis pour Azure AD Connect et que vous aurez peut-être besoin de plus de temps pour planifier et mettre à jour vos serveurs vers les versions plus récentes de ces prérequis. 

**Existe-t-il des nouvelles fonctionnalités que je dois connaître ?** </br>
Non, cette version ne contient aucune nouvelle fonctionnalité. Cette version contient uniquement des mises à jour de certains composants fondamentaux d’Azure AD Connect. 

**Puis-je effectuer une mise à niveau de n’importe quelle version précédente vers la version 2.0 ?** </br>
Oui, les mises à niveau d’une version précédente d’Azure AD Connect vers Azure AD Connect 2.0 sont prises en charge. Suivez l’aide fournie dans cet article pour déterminer quelle est la meilleure stratégie de mise à niveau pour vous. 

**Puis-je exporter la configuration de mon serveur actuel et l’importer dans Azure AD Connect v2.0 ?** </br>
Oui, c’est possible et c’est un excellent moyen de migrer vers Azure AD Connect v2.0, en particulier si vous effectuez également une mise à niveau vers une nouvelle version du système d’exploitation. Pour plus d’informations sur la fonctionnalité Importer/exporter la configuration et sur son utilisation, consultez cet [article](how-to-connect-import-export-config.md). 

**J’ai activé la mise à niveau automatique pour Azure AD Connect. Vais-je recevoir automatiquement cette nouvelle version ?** </br> Non, Azure AD Connect v2.0 n’est pas disponible pour la mise à niveau automatique pour l’instant. 

**Je ne suis pas encore prêt à effectuer la mise à niveau. De combien de temps est-ce que je dispose ?** </br>
Vous devez effectuer la mise à niveau vers Azure AD Connect v2.0 dès que possible. Pour le moment, nous continuons à prendre en charge les versions antérieures d’Azure AD Connect, mais il peut s’avérer difficile de fournir une bonne expérience de support si certains des composants d’Azure AD Connect ne sont plus pris en charge. Cette mise à niveau est particulièrement importante pour ADAL et TLS 1.0/1.1, car ces services peuvent cesser de fonctionner de manière inattendue après leur dépréciation. 

**J’utilise une base de données SQL externe et n’utilise pas SQL Server 2012 Base de données locale. Dois-je quand même effectuer la mise à niveau ?** </br>
Oui, vous devez effectuer la mise à niveau pour conserver un état pris en charge, même si vous n’utilisez pas SQL Server 2012, en raison de la dépréciation de TLS 1.0/1.1 et d’ADAL. 

**Que se passe-t-il si je ne fais pas la mise à niveau ?** </br>
Jusqu’à ce que l’un des composants en cours de mise hors service devienne réellement déconseillé, vous ne verrez aucun impact. Azure AD Connect continuera à fonctionner. 

Nous prévoyons que TLS 1.0/1.1 sera déconseillé en janvier 2022. Vous devez vous assurer de ne plus utiliser ces protocoles avant cette date, car votre service pourrait cesser de fonctionner de manière inattendue. Vous pouvez cependant configurer manuellement votre serveur pour TLS 1.2, ce qui ne nécessite pas de mise à jour d’Azure AD Connect vers la version 2.0. 

En juin 2022, la bibliothèque ADAL ne sera plus prise en charge. Lorsque ADAL ne sera plus prise en charge, l’authentification pourra cesser de fonctionner de manière inattendue, ce qui empêchera le serveur Azure AD Connect de fonctionner correctement. Nous vous conseillons vivement d’effectuer la mise à niveau vers Azure AD Connect v2.0 avant juin 2022. Vous ne pouvez pas effectuer de mise à niveau vers une bibliothèque d’authentification prise en charge avec votre version actuelle d’Azure AD Connect. 

**Après la mise à niveau vers la version 2.0, les cmdlets PowerShell ADSync ne fonctionnent pas ?** </br>
Il s’agit d’un problème connu.  Pour le résoudre, redémarrez votre session PowerShell après l’installation ou la mise à niveau vers la version 2.0, puis réimportez le module.  Suivez les instructions suivantes pour importer le module.
 
 1.  Ouvrez Windows PowerShell avec des privilèges d’administrateur.
 2.  Tapez ou collez le code suivant : 
    ``` powershell
              Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync"
    ```
 

## <a name="license-requirements-for-using-azure-ad-connect-v20"></a>Licences requises pour Azure AD Connect v2.0

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Licences requises pour Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Matériel et conditions préalables](how-to-connect-install-prerequisites.md) 
- [Paramètres Express](how-to-connect-install-express.md)
- [Paramètres personnalisés](how-to-connect-install-custom.md)

Cet article décrit la mise à niveau de versions antérieures de Windows Server vers Windows Server 2019. 