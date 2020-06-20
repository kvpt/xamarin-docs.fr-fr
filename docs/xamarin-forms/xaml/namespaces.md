---
title: Espaces de noms XAML dansXamarin.Forms
description: XAML utilise l’attribut XML xmlns pour les déclarations d’espace de noms. Cet article présente la syntaxe de l’espace de noms XAML et montre comment déclarer un espace de noms XAML pour accéder à un type.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f35342134767ccdadfab086bfa14f6b610b325d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130375"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Espaces de noms XAML dansXamarin.Forms

_XAML utilise l’attribut XML xmlns pour les déclarations d’espace de noms. Cet article présente la syntaxe de l’espace de noms XAML et montre comment déclarer un espace de noms XAML pour accéder à un type._

## <a name="overview"></a>Vue d’ensemble

Il existe deux déclarations d’espaces de noms XAML qui se trouvent toujours dans l’élément racine d’un fichier XAML. Le premier définit l’espace de noms par défaut, comme indiqué dans l’exemple de code XAML suivant :

```xaml
xmlns="http://xamarin.com/schemas/2014/forms"
```

L’espace de noms par défaut spécifie que les éléments définis dans le fichier XAML sans préfixe font référence aux Xamarin.Forms classes, telles que [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

La deuxième déclaration d’espace de noms utilise le `x` préfixe, comme indiqué dans l’exemple de code XAML suivant :

```xaml
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML utilise des préfixes pour déclarer des espaces de noms autres que ceux par défaut, avec le préfixe utilisé lors du référencement de types dans l’espace de noms. La `x` déclaration d’espace de noms spécifie que les éléments définis dans le XAML avec un préfixe de `x` sont utilisés pour les éléments et les attributs qui sont intrinsèques à XAML (en particulier la spécification XAML 2009).

Le tableau suivant présente les `x` attributs d’espace de noms pris en charge par Xamarin.Forms :

|Construction|Description|
|--- |--- |
|`x:Arguments`|Spécifie les arguments de constructeur pour un constructeur non défini par défaut, ou pour une déclaration d’objet de méthode de fabrique.|
|`x:Class`|Spécifie l’espace de noms et le nom de classe d’une classe définie en XAML. Le nom de la classe doit correspondre au nom de la classe du fichier code-behind. Notez que cette construction ne peut apparaître que dans l’élément racine d’un fichier XAML.|
|`x:DataType`|Spécifie le type de l’objet auquel l’élément XAML est lié, et auquel il est enfant.|
|`x:FactoryMethod`|Spécifie une méthode de fabrique qui peut être utilisée pour initialiser un objet.|
|`x:FieldModifier`|Spécifie le niveau d’accès pour les champs générés pour les éléments XAML nommés.|
|`x:Key`|Spécifie une clé définie par l’utilisateur unique pour chaque ressource dans un `ResourceDictionary` . La valeur de la clé est utilisée pour récupérer la ressource XAML et est généralement utilisée comme argument pour l' `StaticResource` extension de balisage.|
|`x:Name`|Spécifie un nom d’objet d’exécution pour l’élément XAML. `x:Name`Le paramètre est semblable à la déclaration d’une variable dans le code.|
|`x:TypeArguments`|Spécifie les arguments de type générique pour le constructeur d’un type générique.|

Pour plus d’informations sur l' `x:DataType` attribut, consultez [liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md). Pour plus d’informations sur l' `x:FieldModifier` attribut, consultez [modificateurs de champ](~/xamarin-forms/xaml/field-modifiers.md). Pour plus d’informations sur `x:Arguments` les `x:FactoryMethod` attributs et, consultez [passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md). Pour plus d’informations sur l' `x:TypeArguments` attribut, consultez [génériques dans XAML avec Xamarin.Forms ](generics.md).

> [!NOTE]
> En plus des attributs d’espace de noms indiqués ci-dessus, Xamarin.Forms comprend également des extensions de balisage qui peuvent être utilisées par le `x` préfixe d’espace de noms. Pour plus d’informations, consultez [utilisation des extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

En XAML, les déclarations d’espaces de noms héritent de l’élément parent de l’élément enfant. Par conséquent, lors de la définition d’un espace de noms dans l’élément racine d’un fichier XAML, tous les éléments de ce fichier héritent de la déclaration d’espace de noms.

## <a name="declaring-namespaces-for-types"></a>Déclaration d’espaces de noms pour les types

Les types peuvent être référencés en XAML en déclarant un espace de noms XAML avec un préfixe, avec la déclaration d’espace de noms spécifiant le nom de l’espace de noms CLR (Common Language Runtime) et éventuellement un nom d’assembly. Pour cela, vous devez définir des valeurs pour les mots clés suivants dans la déclaration d’espace de noms :

- **clr-namespace :** ou **using ::** espace de noms CLR déclaré dans l’assembly qui contient les types à exposer en tant qu’éléments XAML. Ce mot clé est obligatoire.
- **assembly =** : assembly qui contient l’espace de noms CLR référencé. Cette valeur est le nom de l’assembly, sans l’extension de fichier. Le chemin d’accès à l’assembly doit être établi en tant que référence dans le fichier projet qui contient le fichier XAML qui fera référence à l’assembly. Ce mot clé peut être omis si la valeur de l' **espace de noms CLR** est dans le même assembly que le code d’application qui référence les types.

Notez que le caractère qui sépare `clr-namespace` le `using` jeton ou de sa valeur est un signe deux-points, alors que le caractère qui sépare le `assembly` jeton de sa valeur est un signe égal. Le caractère à utiliser entre les deux jetons est un point-virgule.

L’exemple de code suivant montre une déclaration d’espace de noms XAML :

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

Elle peut également être écrite de la façon suivante :

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

Le `local` préfixe est une convention utilisée pour indiquer que les types dans l’espace de noms sont locaux à l’application. Sinon, si les types se trouvent dans un assembly différent, le nom de l’assembly doit également être défini dans la déclaration d’espace de noms, comme illustré dans l’exemple de code XAML suivant :

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

Le préfixe d’espace de noms est ensuite spécifié lors de la déclaration d’une instance d’un type à partir d’un espace de noms importé, comme illustré dans l’exemple de code XAML suivant :

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Pour plus d’informations sur la définition d’un schéma d’espace de noms personnalisé, consultez [schémas d’espace de noms personnalisés XAML](custom-namespace-schemas.md).

## <a name="summary"></a>Résumé

Cet article a introduit la syntaxe d’espace de noms XAML et a montré comment déclarer un espace de noms XAML pour accéder à un type. XAML utilise l' `xmlns` attribut XML pour les déclarations d’espace de noms, et les types peuvent être référencés en XAML en déclarant un espace de noms XAML avec un préfixe.

## <a name="related-links"></a>Liens connexes

- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Génériques en XAML avecXamarin.Forms](generics.md)
