---
title: Créer un locataire Azure AD pour Azure Red Hat OpenShift | Microsoft Docs
description: Voici comment créer un locataire Azure Active Directory (Azure AD) pour héberger votre cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306459"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Créer un locataire Azure AD pour Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift nécessite un [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) client dans lequel créer votre cluster. Un *locataire* est une instance dédiée d’Azure AD un développeur d’application ou organisation reçoit lorsqu’ils créent une relation avec Microsoft en vous inscrivant pour Azure, Microsoft Intune ou Microsoft 365. Chaque client Azure AD est distinct et séparé à partir d’autres répertoires Azure AD locataires possède son propre travail et les identités de l’école et inscriptions d’application.

Si vous ne disposez pas d’un locataire Azure AD, suivez ces instructions pour créer un.

## <a name="create-a-new-azure-ad-tenant"></a>Créer un nouveau locataire Azure AD

Pour créer un locataire :

1. Se connecter à la [portail](https://portal.azure.com/) en utilisant le compte que vous souhaitez associer à votre cluster Azure Red Hat OpenShift.
2. Ouvrez le [panneau Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) pour créer un nouveau client (également appelé un nouveau *Azure Active Directory*).
3. Fournir un **Nom_organisation**.
4. Fournir un **nom de domaine Initial**. Cela aura *onmicrosoft.com* est ajoutée. Vous pouvez réutiliser la valeur de *Nom_organisation* ici.
5. Choisir un pays ou une région où le client doit être créé.
6. Cliquez sur **Créer**.
7. Une fois que votre client Azure AD est créé, sélectionnez le **cliquez ici pour gérer votre nouveau répertoire** lien. Le nom de votre nouveau locataire doit être affiché dans l’angle supérieur droit du portail Azure :  

    ![Capture d’écran du portail montrant le nom du client dans l’angle supérieur droit][tenantcallout]  

8. Prenez note de la *id_locataire* afin de pouvoir spécifier plus tard où créer votre cluster Azure Red Hat OpenShift. Dans le portail, vous devez maintenant voir le panneau de vue d’ensemble d’Azure Active Directory pour votre nouveau locataire. Sélectionnez **propriétés** et copiez la valeur de votre **ID de répertoire**. Nous faisons référence à cette valeur en tant que `TENANT` dans le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Ressources

Découvrez [documentation Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) pour plus d’informations sur [locataires Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer un principal de service, générer une URL de rappel client secret et l’authentification et créer un nouvel utilisateur Active Directory pour tester des applications sur votre cluster Azure Red Hat OpenShift.

[Créer un objet d’application Azure AD et l’utilisateur](howto-aad-app-configuration.md)