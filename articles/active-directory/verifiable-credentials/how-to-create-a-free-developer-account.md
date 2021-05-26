---
title: Comment créer un locataire de développeur Azure Active Directory gratuit
description: Cet article vous explique comment créer un compte de développeur
services: active-directory
author: barclayn
manager: davba
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 2a6b2f9b464a7776b3c0dd58736ca64878e0b65c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466143"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Comment créer un locataire de développeur Azure Active Directory gratuit

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> En préversion, une licence P2 est requise. 

Il existe deux moyens faciles de créer un locataire Azure Active Directory gratuite avec une licence d’évaluation P2. Vous pouvez donc installer le service émetteur de justificatifs vérifiable et tester la création et la validation des justificatifs vérifiables :

- [Participez](https://aka.ms/o365devprogram) au programme gratuit Microsoft 365 pour développeurs et obtenez gratuitement un bac à sable, des outils et d’autres ressources gratuites, comme un locataire Azure Active Directory avec des licences P2. Utilisateurs, groupes, boîtes aux lettres configurés, etc.
- Créez un nouveau [locataire](../develop/quickstart-create-new-tenant.md) et activez une [version d’évaluation gratuite](https://azure.microsoft.com/trial/get-started-active-directory/) d’Azure AD Premium P1 ou P2 dans votre nouveau locataire.

Si vous décidez de vous inscrire au programme gratuit Microsoft 365 pour développeurs, vous devez suivre quelques étapes simples :

1. Cliquez sur le bouton **Rejoindre maintenant** à l’écran.

2. Connectez-vous avec un nouveau compte Microsoft ou utilisez un compte (professionnel) existant.

3. Sur la page d’inscription, sélectionnez votre région, saisissez le nom de votre entreprise et acceptez les conditions générales du programme avant de cliquer sur **Suivant**.

4. Cliquez sur **Configurer l’abonnement**. Spécifiez la région dans laquelle vous souhaitez créer votre locataire, créez un nom d’utilisateur, un domaine, puis entrez un mot de passe. Cela créera un nouveau locataire et le premier administrateur du locataire.

5. Entrez les informations de sécurité nécessaires pour protéger le compte d’administrateur de votre nouveau locataire. Cela permet de configurer l’authentification multifacteur pour le compte.


À ce stade, vous avez créé un locataire avec 25 licences utilisateur E5. Les licences E5 incluent les licences Azure AD P2. Si vous le souhaitez, vous pouvez ajouter des exemples de packs de données avec des utilisateurs, des groupes, la messagerie et SharePoint pour vous aider à tester votre environnement de développement. Pour le service d’émission de justificatifs vérifiable, ils ne sont pas requis.

Pour plus de commodité, vous pouvez ajouter votre propre compte professionnel en tant qu’[invité](../external-identities/b2b-quickstart-add-guest-users-portal.md) dans le locataire nouvellement créé et utiliser ce compte pour administrer le locataire. Si vous souhaitez que le compte invité puisse gérer le service de justificatifs vérifiables, vous devez affecter le rôle « Administrateur général » à cet utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un compte de développeur, vous pouvez essayer notre [premier tutoriel](get-started-verifiable-credentials.md) pour en savoir plus sur les justificatifs vérifiables.