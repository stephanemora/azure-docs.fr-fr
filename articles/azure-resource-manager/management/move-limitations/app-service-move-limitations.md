---
title: Déplacer des ressources Azure App Service
description: Utilisez Azure Resource Manager pour déplacer des ressources App Service vers un nouveau groupe de ressources ou abonnement.
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 34f86cfd3a3822b77992f5d1ae77afaf14fdc7ab
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251452"
---
# <a name="move-guidance-for-app-service-resources"></a>Conseils de déplacement de ressources App Service

Cet article décrit les étapes à suivre pour déplacer des ressources App Service. Il existe des exigences spécifiques pour déplacer des ressources App Service vers un nouvel abonnement.

## <a name="move-across-subscriptions"></a>Déplacer entre différents abonnements

Lors du déplacement d’une application web entre des abonnements, les instructions suivantes s’appliquent :

- Le déplacement d’une ressource vers un nouveau groupe de ressources ou un nouvel abonnement est une modification de métadonnées qui ne doit avoir aucun impact sur le fonctionnement des ressources. Par exemple, l’adresse IP entrante d’un service d’application ne change pas lors du déplacement de l’app service.
- Le groupe de ressources de destination ne doit avoir aucune ressource App Service existante. Les ressources App Service comprennent :
    - Web Apps
    - Plans App Service
    - Certificats TLS/SSL chargés ou importés
    - Environnements App Service
- Toutes les ressources App Service du groupe de ressources doivent être déplacées simultanément.
- Les environnements ASE ne peuvent pas être déplacés vers un nouveau groupe de ressources ou un nouvel abonnement. Toutefois, vous pouvez déplacer une application web et un plan App Service vers un nouvel abonnement sans déplacer l’environnement ASE. Après le déplacement, l’application web n’est plus hébergée dans l’environnement ASE.
- Vous pouvez déplacer un certificat lié à une application web sans supprimer les liaisons TLS, tant que le certificat est déplacé avec toutes les autres ressources du groupe de ressources. Toutefois, vous ne pouvez pas déplacer un certificat géré par App Service gratuit. Pour ce scénario, consultez [Déplacer avec des certificats gérés gratuits](#move-with-free-managed-certificates).
- Les ressources App Service ne peuvent être déplacées qu’à partir du groupe de ressources dans lequel elles ont été créées à l’origine. Si une ressource App Service ne se trouve plus dans son groupe de ressources d’origine, redéplacez-la vers ce groupe. Déplacez ensuite la ressource entre les abonnements. Pour obtenir de l’aide sur la recherche du groupe de ressources d’origine, consultez la section suivante.

## <a name="find-original-resource-group"></a>Rechercher le groupe de ressources d’origine

Si vous ne connaissez pas le groupe de ressources d’origine, recherchez-le dans les diagnostics. Pour votre application web, sélectionnez **Diagnostiquer et résoudre les problèmes**. Ensuite, sélectionnez **Configuration et gestion**.

![Sélectionner les diagnostics](./media/app-service-move-limitations/select-diagnostics.png)

Sélectionnez **Options de migration**.

![Sélectionner les options de migration](./media/app-service-move-limitations/select-migration.png)

Sélectionnez l’option des étapes recommandées pour déplacer l’application web.

![Sélectionner les étapes recommandées](./media/app-service-move-limitations/recommended-steps.png)

Vous voyez alors les actions recommandées à faire avant de déplacer les ressources. Les informations indiquent le groupe de ressources d’origine de l’application web.

![Capture d’écran montrant les étapes recommandées pour déplacer des ressources web Microsoft .NET.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-hidden-resource-types-in-portal"></a>Déplacer des types de ressources masqués dans le portail

Lorsque vous utilisez le portail pour déplacer vos ressources App Service, il se peut qu’une erreur s’affiche, indiquant que vous n’avez pas déplacé toutes les ressources. Si vous voyez cette erreur, vérifiez s’il existe des types de ressources que le portail n’a pas affichés. Sélectionnez **Afficher les types masqués**. Ensuite, sélectionnez toutes les ressources à déplacer.

:::image type="content" source="./media/app-service-move-limitations/show-hidden-types.png" alt-text="Afficher les types masqués":::

## <a name="move-with-free-managed-certificates"></a>Déplacer avec des certificats gérés gratuits

Vous ne pouvez pas déplacer un certificat géré par App Service gratuit. Au lieu de cela, supprimez le certificat, puis recréez-le après le déplacement de l’application web. Pour obtenir des instructions sur la suppression du certificat, utilisez l’outil **Opérations de migration**.

Si votre certificat géré par App Service gratuit est créé dans un groupe de ressources inattendu, essayez de ramener le plan App Service dans son groupe de ressources d’origine. Ensuite, recréez le certificat géré gratuit. Ce problème sera corrigé prochainement.

## <a name="move-support"></a>Prise en charge du déplacement

Pour connaître les ressources App Service pouvant être déplacées, consultez l'état de la prise en charge pour :

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../move-resource-group-and-subscription.md).
