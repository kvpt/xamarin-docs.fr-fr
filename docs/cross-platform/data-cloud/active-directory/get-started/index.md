---
title: Azure Active Directory
description: Ce document décrit les étapes à suivre pour permettre à une application mobile de s’authentifier auprès de Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: b28fcea37d991879df0231609d09eeb2eca49505
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766348"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Inscrire une application pour utiliser Azure Active Directory_

Azure Active Directory permet aux développeurs de sécuriser des ressources telles que des fichiers, des liens et des API Web à l’aide du même compte professionnel que celui utilisé par les employés pour se connecter à leurs systèmes ou consulter leurs e-mails.

Le développement d’applications mobiles qui peuvent s’authentifier avec Azure Active Directory implique trois étapes.
Les deux premières étapes sont généralement les mêmes, quels que soient les services que vous prévoyez d’utiliser. La troisième étape est différente pour chaque type de service :

  1. L' [inscription auprès de Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) sur le portail *WindowsAzure.com* , puis
  2. [Configurez les services](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Développez des applications mobiles à l’aide des services.

Voici quelques exemples de différents services auxquels vous pouvez accéder :

- [API Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API Web
- office365

## <a name="conclusion"></a>Conclusion

À l’aide des étapes ci-dessus, vous pouvez authentifier vos applications mobiles sur Azure Active Directory. Le Bibliothèque d’authentification Active Directory (ADAL) facilite grandement la tâche avec moins de lignes de code, tout en conservant la plus grande partie du code et en le rendant ainsi partagé entre les plateformes.

## <a name="related-links"></a>Liens associés

- [Exemple Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
