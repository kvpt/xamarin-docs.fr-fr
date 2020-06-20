---
title: Présentation des renderers personnalisés
description: Cet article présente les convertisseurs personnalisés et décrit leur processus de création.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2bed4d5e8f89efa2997fb085278c4b549870245
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135319"
---
# <a name="introduction-to-custom-renderers"></a>Présentation des renderers personnalisés

_Les convertisseurs personnalisés fournissent une approche puissante pour personnaliser l’apparence et le comportement des Xamarin.Forms contrôles. Ils peuvent être utilisés pour des modifications de style réduites ou une personnalisation sophistiquée de la disposition et du comportement des plateformes. Cet article fournit une introduction aux convertisseurs personnalisés et décrit le processus de création d’un convertisseur personnalisé._

Xamarin.Forms[Les pages, les dispositions et les contrôles](~/xamarin-forms/user-interface/controls/index.md) présentent une API commune pour décrire les interfaces utilisateur mobiles multiplateforme. Chaque page, disposition et contrôle est rendu différemment sur chaque plateforme, à l’aide d’une `Renderer` classe qui crée à son tour un contrôle natif (correspondant à la Xamarin.Forms représentation), le réorganise à l’écran et ajoute le comportement spécifié dans le code partagé.

Les développeurs peuvent implémenter leurs propres classes `Renderer` pour personnaliser l’apparence et/ou le comportement d’un contrôle. Vous pouvez ajouter des renderers personnalisés pour un type donné à un projet d’application afin de personnaliser le contrôle dans un emplacement tout en autorisant le comportement par défaut sur d’autres plateformes. Vous pouvez également ajouter différents renderers personnalisés à chaque projet d’application pour créer une apparence différente sur iOS, Android et la plateforme universelle Windows (UWP). Cependant, l’implémentation d’une classe de renderer personnalisée pour effectuer une simple personnalisation d’un contrôle est souvent une réponse trop lourde. Les effets simplifient ce processus et sont généralement utilisés pour les petits changements de style. Pour plus d’informations, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="examining-why-custom-renderers-are-necessary"></a>Pourquoi les renderers personnalisés sont-ils nécessaires ?

La modification de l’apparence d’un Xamarin.Forms contrôle, sans utiliser de convertisseur personnalisé, est un processus en deux étapes qui implique la création d’un contrôle personnalisé par le biais du sous-classement, puis l’utilisation du contrôle personnalisé à la place du contrôle d’origine. L’exemple de code suivant montre un exemple d’utilisation d’une sous-classe du contrôle `Entry` :

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

Le contrôle `MyEntry` est un contrôle `Entry` où `BackgroundColor` est de couleur grise. Vous pouvez le référencer en XAML en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur l’élément de contrôle. L’exemple de code suivant montre comment le contrôle personnalisé `MyEntry` peut être consommé par `ContentPage` :

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

Le préfixe d’espace de noms `local` peut varier. Toutefois, les valeurs `namespace` et `assembly` doivent correspondre aux détails du contrôle personnalisé. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer le contrôle personnalisé.

> [!NOTE]
> Il est beaucoup plus simple de définir `xmlns` dans des projets de bibliothèque .NET Standard que dans des projets partagés. Une bibliothèque .NET Standard étant compilée dans un assembly, il est facile de déterminer ce que la valeur `assembly=CustomRenderer` doit être. Quand vous utilisez des projets partagés, toutes les ressources partagées (y compris le XAML) sont compilées dans chacun des projets de référence. Si les projets iOS, Android et UWP ont leurs propres *noms d’assemblys*, il est alors impossible d’écrire la déclaration `xmlns` puisque la valeur doit être différente pour chaque application. Les contrôles personnalisés en XAML pour les projets partagés nécessitent que chaque projet d’application soit configuré avec le même nom d’assembly.

Le contrôle personnalisé `MyEntry` est ensuite restitué sur chaque plateforme, avec un arrière-plan de couleur grise, comme le montrent les captures d’écran suivantes :

![](introduction-images/screenshots.png "MyEntry Custom Control on each Platform")

Pour changer la couleur d’arrière-plan du contrôle sur chaque plateforme, nous avons recouru exclusivement à une sous-classe du contrôle. Le champ d’action de cette technique est toutefois limité, car il n’est pas possible de tirer parti des améliorations et des personnalisations spécifiques à la plateforme. Quand ils sont nécessaires, des renderers personnalisés doivent être implémentés.

## <a name="creating-a-custom-renderer-class"></a>Création d’une classe de renderer personnalisé

Le processus de création d’une classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe de renderer qui restitue le contrôle natif.
1. Remplacez la méthode qui restitue le contrôle natif et écrivez la logique pour le personnaliser. Souvent, la `OnElementChanged` méthode est utilisée pour restituer le contrôle natif, qui est appelé lorsque le Xamarin.Forms contrôle correspondant est créé.
1. Ajoutez un `ExportRenderer` attribut à la classe de convertisseur personnalisée pour spécifier qu’elle sera utilisée pour restituer le Xamarin.Forms contrôle. Cet attribut est utilisé pour inscrire le convertisseur personnalisé avec Xamarin.Forms .

> [!NOTE]
> Pour la plupart des Xamarin.Forms éléments, il est facultatif de fournir un convertisseur personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé. Toutefois, des renderers personnalisés sont nécessaires dans chaque projet de plateforme au moment de la restitution d’un élément [View](xref:Xamarin.Forms.View) ou [ViewCell](xref:Xamarin.Forms.ViewCell).

Les rubriques de cette série fournissent des démonstrations et des explications sur ce processus pour différents Xamarin.Forms éléments.

## <a name="troubleshooting"></a>Dépannage

Si un contrôle personnalisé est contenu dans un projet de bibliothèque .NET Standard qui a été ajouté à la solution (autrement dit, pas la bibliothèque .NET Standard créée par le modèle de projet d’application Visual Studio pour Mac/Visual Studio Xamarin.Forms ), une exception peut se produire dans iOS lors de la tentative d’accès au contrôle personnalisé. Si ce problème se produit, vous pouvez le résoudre en créant une référence au contrôle personnalisé à partir de la classe `AppDelegate` :

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Le compilateur est ainsi forcé à reconnaître le type `ClassInPCL` en le résolvant. Vous pouvez également ajouter l’attribut `Preserve` à la classe `AppDelegate` pour obtenir le même résultat :

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Une référence au type `ClassInPCL` est alors créée, indiquant son caractère obligatoire au moment de l’exécution. Pour plus d’informations, consultez [Conservation du code](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Résumé

Dans cet article, nous avons examiné les renderers personnalisés et décrit leur processus de création. Les convertisseurs personnalisés fournissent une approche puissante pour personnaliser l’apparence et le comportement des Xamarin.Forms contrôles. Ils peuvent être utilisés aussi bien pour les petits changements de style que pour la personnalisation de la disposition sophistiquée d’une plateforme et du comportement.

## <a name="related-links"></a>Liens connexes

- [Effets](~/xamarin-forms/app-fundamentals/effects/index.md)
