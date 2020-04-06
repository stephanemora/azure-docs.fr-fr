---
title: Configurer l’attribution d’applications en libre-service | Microsoft Docs
description: Activer l’accès aux applications en libre-service pour permettre aux utilisateurs de rechercher leurs propres applications
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcccc64e0c766164a06932e9b65a4459816f9deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409114"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Configurer l’attribution d’applications en libre-service

Pour permettre à vos utilisateurs de découvrir eux-mêmes des applications depuis leur panneau d’accès Mes applications, vous devez activer l’option d’**accès aux applications en libre-service** pour toutes les applications pour lesquelles vous souhaitez autoriser les utilisateurs à les découvrir eux-mêmes et en demander l’accès. Cette fonctionnalité est disponible pour les applications qui ont été ajoutées à partir d’[Azure AD Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), d’[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ou par l’intermédiaire du [consentement utilisateur ou administrateur](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Cette fonctionnalité est un excellent moyen pour un groupe informatique d’économiser du temps et de l’argent, et elle est recommandée dans le cadre d’un déploiement d’applications modernes avec Azure Active Directory.

À l’aide de cette fonctionnalité, vous pouvez :

-   Autoriser les utilisateurs à découvrir eux-mêmes des applications depuis le [panneau d’accès Mes applications](https://myapps.microsoft.com/) sans déranger le groupe informatique.

-   Ajouter ces utilisateurs à un groupe préconfiguré pour vous permettre de voir qui a demandé l’accès, de retirer l’accès et de gérer les rôles qui leur ont été attribués.

-   Éventuellement, autoriser un approbateur d’entreprise à approuver les demandes d’accès aux applications à la place du groupe informatique.

-   Éventuellement, configurer jusqu'à 10 personnes qui peuvent autoriser l’accès à cette application.

-   Éventuellement, autoriser un approbateur d’entreprise à définir des mots de passe dont ces utilisateurs peuvent se servir pour se connecter à l’application, directement depuis le [panneau d’accès aux applications](https://myapps.microsoft.com/) de l’approbateur d’entreprise.

-   Éventuellement, attribuer automatiquement directement un rôle d’application à des utilisateurs affectés en libre-service.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Activer l’accès aux applications en libre-service pour permettre aux utilisateurs de rechercher leurs propres applications

L’accès aux applications en libre-service est un excellent moyen pour permettre aux utilisateurs de découvrir eux-mêmes des applications et éventuellement de permettre au groupe d’entreprise d’approuver l’accès à ces applications. Pour les applications nécessitant une authentification unique par mot de passe, vous pouvez également autoriser le groupe d’entreprise à gérer les informations d’identification affectées à ces utilisateurs depuis leurs propres panneaux d’accès Mes applications.

Pour activer l’accès en libre-service à une application, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

2. Sélectionnez **Azure Active Directory**. Dans le menu de navigation gauche, sélectionnez **Applications d’entreprise**.

3. Sélectionnez l’application dans la liste. Si vous ne voyez pas l’application, commencez à taper son nom dans la zone de recherche. Vous pouvez également utiliser des contrôles de filtre pour sélectionner le type, l’état ou la visibilité de l’application, puis sélectionner **Appliquer**.

4. Dans le menu de navigation gauche, sélectionnez **Libre-service**.

5. Pour activer l’accès en libre-service à cette application, définissez l’option **Autoriser les utilisateurs à demander l’accès à cette application ?** sur **Oui.**

6. En regard de **À quel groupe les utilisateurs attribués doivent-ils être ajoutés ?** , cliquez sur **Sélectionner un groupe**. Choisissez un groupe, puis cliquez sur **Sélectionner**. Quand la demande d’un utilisateur est approuvée, il est ajouté à ce groupe. Lorsque vous affichez l’appartenance à ce groupe, vous pouvez voir qui a obtenu l’accès à l’application par le biais de l’accès en libre-service.
  
    > [!NOTE]
    > Ce paramètre ne prend pas en charge les groupes synchronisés localement.

7. **Facultatif :** Pour exiger une approbation d’entreprise avant d’accorder l’accès aux utilisateurs, définissez l’option **Demander une approbation avant d’accorder l’accès à cette application ?** sur **Oui**.

8. **Facultatif : Pour les applications utilisant uniquement l’authentification unique par mot de passe,** pour autoriser les approbateurs d’entreprise à spécifier les mots de passe envoyés à cette application pour les utilisateurs approuvés, définissez l’option **Autoriser les approbateurs à définir les mots de passe de l’utilisateur pour cette application ?** sur **Oui**.

9. **Facultatif :** Pour spécifier les approbateurs d’entreprise autorisés à approuver l’accès à cette application, en regard de **Qui est autorisé à approuver l’accès à cette application ?** , cliquez sur **Sélectionner des approbateurs**, puis sélectionnez jusqu’à 10 approbateurs d’entreprise. Puis cliquez sur **Sélectionner**.

    >[!NOTE]
    >Les groupes ne sont pas pris en charge. Vous pouvez sélectionner jusqu’à 10 approbateurs d’entreprise individuels. Si vous spécifiez plusieurs approbateurs, chaque approbateur peut approuver une demande d’accès.

10. **Facultatif :** **Pour les applications qui exposent des rôles**, pour attribuer un rôle à des utilisateurs approuvés en libre-service, en regard de l’option **À quel rôle attribuer des utilisateurs dans cette application ?** , cliquez sur **Sélectionner un rôle**, puis choisissez le rôle auquel ces utilisateurs doivent être affectés. Puis cliquez sur **Sélectionner**.

11. Cliquez sur le bouton **Enregistrer** en haut du volet pour terminer.

Lorsque vous avez terminé la configuration d’applications en libre-service, les utilisateurs peuvent accéder à leur [volet d’accès Mes applications](https://myapps.microsoft.com/) et cliquer sur le bouton **Ajouter des applications en libre-service** pour trouver les applications pour lesquelles vous avez activé l’accès en libre-service. Les approbateurs d’entreprise reçoivent également une notification dans leur [volet d’accès Mes applications](https://myapps.microsoft.com/). Vous pouvez leur envoyer une notification par e-mail les avertissant qu’un utilisateur a demandé l’accès à une application nécessitant leur approbation.

## <a name="next-steps"></a>Étapes suivantes
[Configuration d’Azure Active Directory pour la gestion de groupe en libre-service](../users-groups-roles/groups-self-service-management.md)
