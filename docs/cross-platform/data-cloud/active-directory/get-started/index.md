---
title: Azure Active Directory
description: Ce document décrit les étapes qui doivent être remplies pour autoriser une application mobile auprès d’Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318296"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Inscrire une application pour utiliser Azure Active Directory_

Azure Active Directory permet aux développeurs de sécuriser les ressources telles que des fichiers, les liens et les API Web avec le même compte d’organisation qu’employés utilisent pour se connecter à leurs systèmes ou vérifier leurs e-mails.

Développement d’applications mobiles qui peuvent s’authentifier avec Azure Active Directory implique trois étapes.
Les deux premières étapes sont généralement les mêmes, quel que soit les services que vous envisagez d’utiliser. La troisième étape est différente pour chaque type de service :

  1. [L’inscription à Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) sur le *windowsazure.com* portail, puis
  2. [Configurer les services](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Développer des applications mobiles à l’aide des services.

Voici quelques exemples de services différents, vous pouvez accéder à :

- [API Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API web
- Office365


## <a name="conclusion"></a>Conclusion

À l’aide de la procédure ci-dessus, vous pouvez authentifier vos applications mobiles avec Azure Active Directory. Active Directory Authentication Library (ADAL) rend beaucoup plus facile avec moins de lignes de code, tout en conservant la plupart du code de la même et ce qui rend partageable entre plateformes.



## <a name="related-links"></a>Liens associés

- [Exemple de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
