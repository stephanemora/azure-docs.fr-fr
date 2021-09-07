---
title: Devenir un fournisseur de clés de sécurité FIDO2 compatibles Microsoft pour la connexion à Azure AD
description: Décrit le processus pour devenir un partenaire matériel FIDO2
ms.date: 08/02/2021
services: active-directory
ms.service: active-directory
ms.subservice: authentication
author: knicholasa
ms.author: nichola
ms.topic: conceptual
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d21b99fd1def56b60896d157c8a35b9381a003
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562807"
---
# <a name="become-a-microsoft-compatible-fido2-security-key-vendor"></a>Devenir fournisseur de clés de sécurité FIDO2 compatible Microsoft

La plupart des violations liées au piratage impliquent des mots de passe volés ou faibles. Souvent, les services Informatique imposent une complexité de mot de passe renforcée ou des modifications de mot de passe fréquentes afin de réduire le risque d’incident de sécurité. Toutefois, cela augmente les coûts du support technique et entraîne une dégradation de l’expérience utilisateur, car ceux-ci sont tenus de mémoriser ou de stocker de nouveaux mots de passe complexes.

Les clés de sécurité FIDO2 offrent une alternative. Elles peuvent remplacer les informations d’identification faibles par des informations d’identification à clé privée/publique associées au matériel et qui ne peuvent pas être réutilisées, relues ou partagées entre les services. Les clés de sécurité prennent en charge les scénarios d’appareils partagés, ce qui vous permet de garder vos informations d’identification avec vous et de vous authentifier de manière sécurisée auprès d’un appareil Windows 10 Azure Active Directory joint qui fait partie de votre organisation.

Microsoft établit des partenariats avec des fournisseurs de clés de sécurité FIDO2 afin de garantir que les appareils de sécurité fonctionnent sur Windows, le navigateur Microsoft Edge et les comptes Microsoft en ligne, et bénéficient de l’authentification renforcée sans mot de passe.

Vous pouvez devenir fournisseur de clés de sécurité FIDO2 compatible Microsoft par le biais du processus suivant.  Microsoft ne s’engage pas à effectuer d’activités Go-to-Market avec le partenaire, et évaluera la priorité des partenaires en fonction de la demande des clients.

1. Tout d’abord, votre authentificateur doit avoir une certification FIDO2. Nous ne pourrons pas travailler avec des fournisseurs qui n’ont pas de certification FIDO2. Pour en savoir plus sur la certification, consultez ce site web :  [https://fidoalliance.org/certification/](https://fidoalliance.org/certification/)
2. Une fois la certification FIDO2 acquise, renseignez votre demande dans le formulaire suivant : [https://forms.office.com/r/NfmQpuS9hF](https://forms.office.com/r/NfmQpuS9hF). Notre équipe d’ingénierie testera uniquement la compatibilité de vos appareils FIDO2. Nous ne testerons pas la sécurité de vos solutions.
3. Une fois que nous aurons confirmé le passage à la phase de test, le processus prendra généralement de trois à six mois. Les étapes sont généralement les suivantes :
    - Discussion initiale entre Microsoft et votre équipe
        - Vérification de la certification FIDO Alliance, ou du chemin de la certification si celle-ci n’est pas terminée
        - Réception d’une vue d’ensemble de l’appareil de la part du fournisseur
    - Microsoft partagera ses scripts de test avec vous. Notre équipe d’ingénierie sera en mesure de répondre à vos questions si vous avez des besoins spécifiques
    - Vous effectuerez les tests et enverrez tous les résultats à l’équipe d’ingénierie Microsoft
    - Une fois la confirmation par Microsoft obtenue, vous enverrez plusieurs exemples de matériel/solution de chaque appareil à l’équipe d’ingénierie de Microsoft
        - Dès réception, l’équipe d’ingénierie de Microsoft effectuera une vérification du script de test et du flux d’expérience utilisateur
4. En cas de réussite de tous les tests menés par l’équipe d’ingénierie de Microsoft, Microsoft confirmera l’inclusion de l’appareil du fournisseur dans le service [FIDO MDS](https://fidoalliance.org/metadata/).
5. Microsoft ajoutera votre clé de sécurité FIDO2 au back-end Azure AD et à sa liste de fournisseurs FIDO2 approuvés.

## <a name="current-partners"></a>Partenaires actuels

Le tableau suivant liste les partenaires qui sont des fournisseurs de clés de sécurité FIDO2 compatibles Microsoft.

| **Fournisseur** | **Lien** |
| --- | --- |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| Go-Trust ID | [https://www.gotrustid.com/](https://www.gotrustid.com/idem-key) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Kensington  | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Nymi   | [https://www.nymi.com/product](https://www.nymi.com/product) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| Thales | [https://cpl.thalesgroup.com/access-management/authenticators/fido-devices](https://cpl.thalesgroup.com/access-management/authenticators/fido-devices) |
| Thetis | [https://thetis.io/collections/fido2](https://thetis.io/collections/fido2) |
| Token2 Switzerland | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| Solutions TrustKey | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net/) |
| Yubico | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |

## <a name="next-steps"></a>Étapes suivantes

[Compatibilité FIDO2](fido2-compatibility.md)

