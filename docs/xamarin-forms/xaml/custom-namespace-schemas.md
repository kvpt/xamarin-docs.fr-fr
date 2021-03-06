---
title: Schémas d’espace de noms personnalisé XAML dans Xamarin.Forms
description: Un schéma d’espace de noms personnalisé XAML peut être défini avec la classe XmlnsDefinitionAttribute, qui spécifie un mappage entre une URL personnalisée et un ou plusieurs espaces de noms CLR. Le schéma d’espace de noms personnalisé peut ensuite être utilisé dans les déclarations d’espace de noms XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f950db0694b21239b742867d519e893d9a62384c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374067"
---
# <a name="xaml-custom-namespace-schemas-in-no-locxamarinforms"></a>Schémas d’espace de noms personnalisé XAML dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)

Les types d’une bibliothèque peuvent être référencés en XAML en déclarant un espace de noms XAML pour la bibliothèque, avec la déclaration d’espace de noms qui spécifie le nom de l’espace de noms CLR (Common Language Runtime) et un nom d’assembly :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly=MyCompany.Controls">
    ...
</ContentPage>
```

Toutefois, la spécification d’un espace de noms CLR et d’un nom d’assembly dans une `xmlns` définition peut être difficile et sujette aux erreurs. En outre, plusieurs déclarations d’espaces de noms XAML peuvent être nécessaires si la bibliothèque contient des types dans plusieurs espaces de noms.

Une autre approche consiste à définir un schéma d’espace de noms personnalisé, tel que `http://mycompany.com/schemas/controls` , qui correspond à un ou plusieurs espaces de noms CLR. Cela permet à une déclaration d’espace de noms XAML unique de faire référence à tous les types d’un assembly, même s’ils se trouvent dans des espaces de noms différents. Il permet également à une déclaration d’espace de noms XAML unique de référencer des types dans plusieurs assemblys.

Pour plus d’informations sur les espaces de noms XAML, consultez [espaces Xamarin.Forms de noms XAML dans ](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Définition d’un schéma d’espace de noms personnalisé

L’exemple d’application contient une bibliothèque qui expose des contrôles simples, tels que `CircleButton` :

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

Tous les contrôles de la bibliothèque résident dans l' `MyCompany.Controls` espace de noms. Ces contrôles peuvent être exposés à un assembly appelant par le biais d’un schéma d’espace de noms personnalisé.

Un schéma d’espace de noms personnalisé est défini avec la `XmlnsDefinitionAttribute` classe, qui spécifie le mappage entre un espace de noms XAML et un ou plusieurs espaces de noms CLR. `XmlnsDefinitionAttribute`Accepte deux arguments : le nom de l’espace de noms XAML et le nom de l’espace de noms CLR. Le nom de l’espace de noms XAML est stocké dans la `XmlnsDefinitionAttribute.XmlNamespace` propriété, et le nom de l’espace de noms CLR est stocké dans la `XmlnsDefinitionAttribute.ClrNamespace` propriété.

> [!NOTE]
> La `XmlnsDefinitionAttribute` classe a également une propriété nommée `AssemblyName` , qui peut être définie éventuellement sur le nom de l’assembly. Cela est requis uniquement lorsqu’un espace de noms CLR référencé à partir d’un `XmlnsDefinitionAttribute` est dans un assembly externe.

`XmlnsDefinitionAttribute`Doit être défini au niveau de l’assembly dans le projet qui contient les espaces de noms CLR qui seront mappés dans le schéma d’espace de noms personnalisé. L’exemple suivant montre le fichier **AssemblyInfo.cs** de l’exemple d’application :

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Ce code crée un schéma d’espace de noms personnalisé qui mappe l' `http://mycompany.com/schemas/controls` URL à l' `MyCompany.Controls` espace de noms CLR. En outre, l' `Preserve` attribut est spécifié sur l’assembly, pour s’assurer que l’éditeur de liens conserve tous les types dans l’assembly.

> [!IMPORTANT]
> L' `Preserve` attribut doit être appliqué aux classes de l’assembly qui sont mappées via le schéma d’espace de noms personnalisé, ou appliquées à l’assembly entier.

Le schéma d’espace de noms personnalisé peut ensuite être utilisé pour la résolution de type dans les fichiers XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Utilisation d’un schéma d’espace de noms personnalisé

Pour consommer des types à partir du schéma d’espace de noms personnalisé, le compilateur XAML requiert une référence de code de l’assembly qui utilise les types, à l’assembly qui définit les types. Pour ce faire, vous pouvez ajouter une classe contenant une `Init` méthode à l’assembly qui définit les types qui seront utilisés par le XAML :

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

La `Init` méthode peut ensuite être appelée à partir de l’assembly qui consomme les types du schéma d’espace de noms personnalisé :

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> Si vous n’incluez pas une telle référence de code, le compilateur XAML ne pourra pas localiser l’assembly contenant les types de schéma d’espace de noms personnalisé.

Pour utiliser le `CircleButton` contrôle, un espace de noms XAML est déclaré, avec la déclaration d’espace de noms qui spécifie l’URL du schéma d’espace de noms personnalisé :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton` les instances peuvent ensuite être ajoutées au [`ContentPage`](xref:Xamarin.Forms.ContentPage) en les déclarant avec le `controls` préfixe d’espace de noms.

Pour rechercher les types de schémas d’espace de noms personnalisés, Xamarin.Forms recherche les instances référencées dans les assemblys `XmlnsDefinitionAttribute` . Si l' `xmlns` attribut d’un élément dans un fichier XAML correspond à la `XmlNamespace` valeur de propriété dans un `XmlnsDefinitionAttribute` , Xamarin.Forms tente d’utiliser la `XmlnsDefinitionAttribute.ClrNamespace` valeur de propriété pour la résolution du type. Si la résolution de type échoue, Xamarin.Forms continue d’essayer la résolution de type en fonction de toutes les instances correspondantes supplémentaires `XmlnsDefinitionAttribute` .

Le résultat est que deux `CircleButton` instances sont affichées :

![Boutons de cercle](custom-namespace-schemas-images/circle-buttons.png "Boutons de cercle")

## <a name="related-links"></a>Liens connexes

- [Schémas d’espace de noms personnalisés (exemple)](/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)
- [Préfixes recommandés pour les espaces de noms XAML](custom-prefix.md)
- [Espaces de noms XAML dans Xamarin.Forms](namespaces.md)