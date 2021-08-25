---
title: Provisionnement et configuration du routage direct Azure - Azure Communication Services
description: Découvrez comment ajouter un contrôleur SBC (Session Border Controller) et configurer le routage vocal pour le routage direct Azure Communication Services
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 27b1b08196ef76bba92bf1f2ff518585c896178a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743528"
---
# <a name="session-border-controllers-and-voice-routing"></a>Contrôleurs SBC et routage vocal
Le routage direct Azure Communication Services vous permet de connecter votre infrastructure de téléphonie existante à Azure. Cet article liste les étapes principales nécessaires pour connecter un contrôleur SBC (Session Border Controller) pris en charge au routage direct et comment fonctionne le routage vocal pour la ressource de communication activée. 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]
 
Pour savoir si le routage direct Azure Communication Services est la solution adaptée à votre organisation, consultez [Concepts de la téléphonie Azure](./telephony-concept.md). Pour plus d’informations sur les prérequis et la planification de votre déploiement, consultez [Exigences pour l’infrastructure du routage direct Communication Services](./direct-routing-infrastructure.md).

## <a name="connect-the-sbc-with-azure-communication-services"></a>Connecter le contrôleur SBC avec Azure Communication Services

### <a name="configure-using-azure-portal"></a>Effectuer la configuration avec le portail Azure 
1. Dans le volet de navigation de gauche, sélectionnez Routage direct sous Appel vocal - RTC, puis sélectionnez l’onglet Configurer sous l’onglet Contrôleur SBC.
1. Entrez un nom de domaine complet et un port de signalisation pour le contrôleur SBC.
 
Si vous utilisez Office 365, assurez-vous que la partie domaine du nom de domaine complet du contrôleur SBC est différente du domaine que vous avez enregistré dans le portail d’administration Office 365 sous Domaines. 
- Par exemple, si `contoso.com` est un domaine inscrit dans O365, vous ne pouvez pas utiliser `sbc.contoso.com` pour Communication Services. Toutefois, vous pouvez utiliser un domaine de niveau supérieur s’il n’en existe pas dans O365 : vous pouvez créer un domaine `acs.contoso.com` et utiliser le nom de domaine complet `sbc.acs.contoso.com` comme nom de contrôleur SBC.
- Le certificat SBC doit correspondre au nom ; les certificats génériques sont pris en charge.
- Le domaine *.onmicrosoft.com ne peut pas être utilisé pour le nom de domaine complet du contrôleur SBC.
Pour découvrir la liste complète des exigences, consultez [Exigences pour l’infrastructure du routage direct Azure](./direct-routing-infrastructure.md).

   :::image type="content" source="../media/direct-routing-provisioning/add-session-border-controller.png" alt-text="Ajout d’un contrôleur SBC":::
- Lorsque vous avez terminé, cliquez sur Suivant.
Si tout est correctement configuré, vous devriez voir un échange de messages OPTIONS entre Microsoft et votre contrôleur SBC ; utilisez vos dispositif de supervision/journaux SBC pour valider la connexion.

## <a name="voice-routing-considerations"></a>Considérations relatives au routage vocal

Le routage direct Azure Communication Services dispose d’un mécanisme de routage qui permet d’envoyer un appel à un contrôleur SBC spécifique basé sur le modèle de numéro appelé.
Quand vous ajoutez une configuration de routage direct à une ressource, tous les appels effectués à partir des instances de cette ressource (identités) essaient d’abord d’utiliser une jonction de routage direct. Le routage est basé sur un numéro composé et une correspondance dans les routes vocales configurés pour la ressource. En cas de correspondance, l’appel passe par la jonction de routage direct. En l’absence de correspondance, l’étape suivante consiste à traiter le paramètre alternateCallerId de la méthode callAgent.startCall. Si la ressource est activée pour l’appel vocal (RTC), qu’elle a au moins un numéro acheté auprès de Microsoft et que le paramètre alternateCallerId correspond à l’un des numéros achetés pour la ressource, l’appel est routé via l’appel vocal (RTC) au moyen de l’infrastructure Microsoft. Si le paramètre alternateCallerId ne correspond à aucun des numéros achetés, l’appel échoue. Le diagramme ci-dessous illustre la logique du routage vocal Azure Communication Services.

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-diagram.png" alt-text="Routage vocal sortant Communication Services":::

## <a name="voice-routing-examples"></a>Exemples de routage vocal
Les exemples suivants illustrent le routage vocal dans un flux d’appel.

### <a name="one-route-example"></a>Exemple avec une route :
Si vous créez une route vocale avec un modèle `^\+1(425|206)(\d{7})$` et que vous y ajoutez `sbc1.contoso.biz` et `sbc2.contoso.biz`, quand l’utilisateur effectue un appel à `+1 425 XXX XX XX` ou `+1 206 XXX XX XX`, l’appel est tout d’abord routé vers le contrôleur SBC `sbc1.contoso.biz` ou `sbc2.contoso.biz`. Si aucun des contrôleurs SBC n’est disponible, l’appel est abandonné.

### <a name="two-routes-example"></a>Exemple avec deux routes :
Supposons que vous créez une route vocale avec un modèle `^\+1(425|206)(\d{7})$`, que vous y ajoutez `sbc1.contoso.biz` et `sbc2.contoso.biz`, puis que vous créez une seconde route avec le même modèle avec `sbc3.contoso.biz` et `sbc4.contoso.biz`. Dans ce cas, quand l’utilisateur effectue un appel à `+1 425 XXX XX XX` ou `+1 206 XXX XX XX`, l’appel est tout d’abord routé vers le contrôleur SBC `sbc1.contoso.biz` ou `sbc2.contoso.biz`. Si aucun des deux contrôleurs sbc1 et sbc2 n’est disponible, la route ayant une priorité inférieure est essayée (`sbc3.contoso.biz` et `sbc4.contoso.biz`). Si aucun des contrôleurs SBC de la seconde route n’est disponible, l’appel est abandonné.

### <a name="three-routes-example"></a>Exemple avec trois routes :
Supposons que vous créez une route vocale avec un modèle `^\+1(425|206)(\d{7})$`, que vous y ajoutez `sbc1.contoso.biz` et `sbc2.contoso.biz`, que vous créez une deuxième route avec le même modèle avec `sbc3.contoso.biz` et `sbc4.contoso.biz`, puis que vous créez une troisième route avec `^+1(\d[10])$` avec `sbc5.contoso.biz`. Dans ce cas, quand l’utilisateur effectue un appel à `+1 425 XXX XX XX` ou `+1 206 XXX XX XX`, l’appel est tout d’abord routé vers le contrôleur SBC `sbc1.contoso.biz` ou `sbc2.contoso.biz`. Si aucun des deux contrôleurs sbc1 et sbc2 n’est disponible, la route ayant une priorité inférieure est essayée (`sbc3.contoso.biz` et `sbc4.contoso.biz`). Si aucun des contrôleurs SBC d’une deuxième route n’est disponible, la troisième route est essayée. Si le contrôleur sbc5 n’est pas non plus disponible, l’appel est abandonné. De plus, si un utilisateur compose `+1 321 XXX XX XX`, l’appel passe à `sbc5.contoso.biz` et, s’il n’est pas disponible, l’appel est abandonné.

> [!NOTE]
> Dans tous les exemples, alors que la route vocale supérieure a une priorité plus élevée, les contrôleurs SBC dans une route sont essayés dans un ordre aléatoire.

> [!NOTE]
> Dans tous les exemples, si le numéro composé ne correspond pas au modèle, l’appel est abandonné, sauf s’il existe un numéro acheté pour la ressource de communication et que ce numéro a été utilisé comme `alternateCallerId` dans l’application. 

## <a name="configure-voice-routing"></a>Configurer le routage vocal 

### <a name="configure-using-azure-portal"></a>Effectuer la configuration avec le portail Azure

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-configuration.png" alt-text="Configuration du routage vocal sortant Communication Services":::

Donnez un nom à votre route vocale, spécifiez le modèle de numéro avec des expressions régulières, puis sélectionnez le contrôleur SBC pour ce modèle. Voici quelques exemples simples d’expressions régulières :
- `^\+\d+$` : correspond à un numéro de téléphone comportant un ou plusieurs chiffres et commençant par un signe plus
- `^+1(\d[10])$` : correspond à un numéro de téléphone commençant par un `+1` suivi de dix chiffres
- `^\+1(425|206)(\d{7})$` : correspond à un numéro de téléphone qui commence par `+1425` ou `+1206` suivi de sept chiffres
- `^\+0?1234$` : correspond aux deux numéros de téléphone `+01234` et `+1234`.

Pour plus d’informations sur les expressions régulières, consultez [Vue d’ensemble des expressions régulières de .NET](/dotnet/standard/base-types/regular-expressions).

Vous pouvez sélectionner plusieurs contrôleurs SBC pour un même modèle. Dans ce cas, l’algorithme de routage les choisit dans un ordre aléatoire. Vous pouvez également spécifier plusieurs fois le modèle de nombre exact. La ligne supérieure a une priorité plus élevée et, si tous les contrôleurs SBC associés à cette ligne ne sont pas disponibles, la ligne suivante est sélectionnée. De cette façon, vous créez des scénarios de routage complexes.

## <a name="delete-direct-routing-configuration"></a>Supprimer la configuration de routage direct

### <a name="delete-using-azure-portal"></a>Effectuer la suppression avec le portail Azure

#### <a name="to-delete-a-voice-route"></a>Pour supprimer une route vocale :
1. Dans le volet de navigation de gauche, accédez à Routage direct sous Appel vocal - RTC, puis sélectionnez l’onglet Routes vocales.
1. Sélectionnez la ou les routes à supprimer avec une case à cocher.
1. Sélectionnez Supprimer.

#### <a name="to-delete-an-sbc"></a>Pour supprimer un contrôleur SBC :
1. Dans le volet de navigation de gauche, accédez à Routage direct sous Appel vocal - RTC.
1. Sous l’onglet Contrôleur SBC, sélectionnez Configurer.
1. Effacez les champs du nom de domaine complet et du port relatifs au contrôleur SBC que vous souhaitez supprimer, puis sélectionnez Suivant.
1. Sous l’onglet Routes vocales, vérifiez la configuration du routage vocal, puis apportez les modifications éventuellement nécessaires. Sélectionnez Enregistrer.

> [!NOTE]
> Quand vous supprimez le contrôleur SBC associé à une route vocale, vous pouvez choisir un autre contrôleur SBC pour la route sous l’onglet Routes vocales. La route vocale sans contrôleur SBC est supprimée.

## <a name="next-steps"></a>Étapes suivantes

### <a name="conceptual-documentation"></a>Documentation conceptuelle

- [Contrôleurs SBC certifiés pour le routage direct Azure Communication Services](./certified-session-border-controllers.md)
- [Tarification](../pricing.md)

### <a name="quickstarts"></a>Démarrages rapides

- [Appel à un téléphone](../../quickstarts/voice-video-calling/pstn-call.md)
