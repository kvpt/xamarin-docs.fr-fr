---
title: Prise en charge de .NET Standard 2,0 dans Xamarin. Forms
description: Cet article explique comment convertir une application Xamarin. Forms pour utiliser .NET Standard 2,0. .NET Standard est une spécification d’API .NET qui sont destinées à être disponibles sur toutes les implémentations de .NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: eaef607e3e6095ccc7dfb1f5831d2384d11cab5f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760064"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Prise en charge de .NET Standard 2,0 dans Xamarin. Forms

_Cet article explique comment convertir une application Xamarin. Forms pour utiliser .NET Standard 2,0._

.NET Standard est une spécification d’API .NET qui sont destinées à être disponibles sur toutes les implémentations de .NET. Il facilite le partage de code entre les applications de bureau, les applications mobiles et les jeux, et les services de Cloud Computing, en plaçant des API identiques sur les différentes plateformes. Pour plus d’informations sur les plateformes prises en charge par .NET Standard, consultez [prise en charge de l’implémentation .net](/dotnet/standard/net-standard#net-implementation-support).

Les bibliothèques de classes .NET Standard remplacent les bibliothèques de classes portables (PCL). Toutefois, une bibliothèque qui cible .NET Standard est toujours un PCL et est appelée PCL .NET Standard. Certains profils PCL sont mappés à des versions .NET Standard et, pour les profils qui ont un mappage, les deux types de bibliothèques peuvent se référencer mutuellement. Pour plus d’informations, consultez [compatibilité PCL](/dotnet/standard/net-standard#pcl-compatibility).

Xamarin. Forms 2,4 permet aux applications Xamarin. Forms de cibler .NET Standard 2,0 en remplaçant le PCL par une bibliothèque .NET Standard 2,0. Pour ce faire, procédez comme suit :

- Vérifiez que [.net Core 2,0](https://www.microsoft.com/net/download/core) est installé.
- Mettez à jour la solution Xamarin. Forms pour utiliser Xamarin. Forms 2,4 ou une version ultérieure.
- Ajoutez une .NET Standard bibliothèque à la solution, qui cible .NET Standard 2,0.
- Supprimez la classe qui est ajoutée à la bibliothèque de .NET Standard.
- Ajoutez le package NuGet Xamarin. Forms 2,4 (ou version ultérieure) à la bibliothèque .NET Standard.
- Dans les projets de plateforme, ajoutez une référence à la bibliothèque de .NET Standard et supprimez la référence au projet PCL qui contient la logique de l’interface utilisateur Xamarin. Forms.
- Copiez les fichiers du projet PCL dans la bibliothèque de .NET Standard.
- Supprimez le projet PCL qui contient la logique de l’interface utilisateur Xamarin. Forms.

## <a name="related-links"></a>Liens associés

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Options de partage de code](~/cross-platform/app-fundamentals/code-sharing.md)
