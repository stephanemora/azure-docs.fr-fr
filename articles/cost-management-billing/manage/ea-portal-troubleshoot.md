---
title: Résoudre les problèmes d’accès au portail Azure EA
description: Cet article décrit certains problèmes courants qui peuvent se produire avec un Contrat Entreprise Azure (EA) dans le portail Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 03/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 852fb85607318772870a8b6826c934997b84ff6d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726463"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Résoudre les problèmes d’accès au portail Azure EA

Cet article décrit certains problèmes courants qui peuvent se produire avec un Contrat Entreprise Azure (EA). Le portail Azure EA permet de gérer les utilisateurs et les coûts d’un contrat Entreprise. Vous pouvez rencontrer ces problèmes lors de la configuration ou de la mise à jour de l’accès au portail Azure EA.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problèmes lors de l’ajout d’un administrateur à une inscription

Il existe différents types de niveaux d’authentification pour les inscriptions Entreprise. Lorsque les niveaux d’authentification sont appliqués de manière incorrecte, vous pouvez rencontrer des problèmes lorsque vous essayez de vous connecter au portail Azure EA.

Vous utilisez le portail Azure EA pour accorder l’accès aux utilisateurs avec différents niveaux d’authentification. Un administrateur d’entreprise peut mettre à jour le niveau d’authentification pour répondre aux exigences de sécurité de son organisation.

### <a name="authentication-level-types"></a>Types de niveau d’authentification

- Compte Microsoft uniquement - Pour les organisations qui souhaitent utiliser, créer et gérer des utilisateurs via des comptes Microsoft.
- Compte professionnel ou scolaire - Pour les organisations qui ont configuré Active Directory avec la fédération dans le cloud et qui ont tous les comptes sur un seul locataire.
- Compte professionnel ou scolaire inter-locataires - Pour les organisations qui ont configuré Active Directory avec la fédération dans le cloud et qui auront des comptes sur plusieurs locataires.
- Compte mixte - Vous permet d’ajouter des utilisateurs avec un compte Microsoft et/ou avec un compte professionnel ou scolaire.

Le premier compte professionnel ou scolaire ajouté à l’inscription détermine le domaine _par défaut_. Pour ajouter un compte professionnel ou scolaire avec un autre locataire, vous devez modifier le niveau d’authentification sous l’inscription en spécifiant l’authentification inter-locataires.

Pour mettre à jour le niveau d’authentification :

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
2. Cliquez sur **Gérer** dans le volet de navigation de gauche.
3. Cliquez sur l’onglet **Inscription**.
4. Sous **Détails de l’inscription**, sélectionnez **Niveau d’authentification**.
5. Cliquez sur le symbole de crayon.
6. Cliquez sur **Enregistrer**.

![Exemple illustrant les niveaux d’authentification ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Les comptes Microsoft doit avoir un ID associé, créé à l’adresse [https://signup.live.com](https://signup.live.com/).

Des comptes professionnels ou scolaires sont disponibles pour les organisations qui ont configuré Active Directory avec la fédération et dont tous les comptes figurent sur un seul locataire. Les utilisateurs peuvent être ajoutés avec l’authentification des utilisateurs fédérés professionnels ou scolaires si le service Active Directory interne de l’entreprise est fédéré.

Si votre organisation n’utilise pas la fédération Active Directory, vous ne pouvez pas utiliser votre adresse e-mail professionnelle ou scolaire. Au lieu de cela, inscrivez ou créez une nouvelle adresse e-mail et inscrivez-la en tant que compte Microsoft.

## <a name="unable-to-access-the-azure-ea-portal"></a>Impossible d’accéder au portail Azure EA

Si vous recevez un message d’erreur lorsque vous essayez de vous connecter au portail Azure EA, suivez les étapes de dépannage suivantes :

- Assurez-vous d’utiliser l’URL correcte du portail Azure EA, à savoir https://ea.azure.com.
- Déterminez si votre accès au portail Azure EA a été ajouté en tant que compte professionnel ou scolaire ou en tant que compte Microsoft.
  - Si vous utilisez votre compte professionnel, entrez votre adresse e-mail professionnelle et votre mot de passe professionnel. Votre mot de passe professionnel vous est fourni par votre organisation. Vous pouvez vérifier auprès de votre service informatique comment réinitialiser ce mot de passe en cas de problème.
  - Si vous utilisez un compte Microsoft, entrez l’adresse e-mail et le mot de passe de votre compte Microsoft. Si vous avez oublié le mot de passe de votre compte Microsoft, vous pouvez le réinitialiser à l’adresse [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Utilisez une session de navigateur InPrivate ou Incognito pour vous connecter afin de ne conserver aucun cookie ni aucune information en cache des sessions précédentes ou existantes. Effacez le cache de votre navigateur et utilisez une fenêtre InPrivate ou Incognito pour ouvrir https://ea.azure.com.
- Si vous recevez une erreur _Utilisateur non valide_ lors de l’utilisation d’un compte Microsoft, cela peut être dû au fait que vous possédez plusieurs comptes Microsoft. Celui avec lequel vous essayez de vous connecter ne correspond pas à l’adresse e-mail principale.
Ou, si vous recevez une erreur _Utilisateur non valide_, cela peut être dû au fait qu’un type de compte incorrect a été utilisé lorsque l’utilisateur a été ajouté à l’inscription. Par exemple, un compte professionnel ou scolaire à la place d’un compte Microsoft. Dans cet exemple, un autre administrateur EA doit ajouter le compte correct ou vous devez contacter le [support technique](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Si vous devez vérifier l’alias principal, accédez à [https://account.live.com](https://account.live.com). Cliquez ensuite sur **Vos informations**, puis sur **Gérer la manière dont vous vous connectez à Microsoft**. Suivez les invites pour vérifier une autre adresse e-mail et obtenir un code pour accéder aux informations sensibles. Entrez le code de sécurité. Sélectionnez **Configurer ultérieurement** si vous ne souhaitez pas configurer l’authentification à deux facteurs.
  - Vous verrez la page **Gérer la manière dont vous vous connectez à Microsoft**, dans laquelle vous pouvez consulter vos alias de compte. Vérifiez que l’alias principal est celui que vous utilisez pour vous connecter au portail Azure EA. Si ce n’est pas le cas, vous pouvez en faire votre alias principal. Ou, vous pouvez utiliser l’alias principal pour le portail Azure EA à la place.

## <a name="next-steps"></a>Étapes suivantes

- Les administrateurs du portail Azure EA doivent lire la [documentation consacrée à l’administration du portail Azure EA](ea-portal-administration.md) pour découvrir les tâches d’administration courantes.
- Consultez les [questions fréquentes (FAQ) sur Cost Management + Billing](../cost-management-billing-faq.yml) pour voir les questions et réponses sur les problèmes courants avec l’activation d’Azure EA.
