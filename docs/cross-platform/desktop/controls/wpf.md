---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'WPF et Xamarin. Forms : similarités & différences'
description: Ce document compare et contraste WPF avec Xamarin. Forms. Il traite des modèles de contrôle, du XAML, de l’infrastructure de liaison, des modèles de données, d’ItemsControl, de UserControl, de la navigation et de la navigation dans les URL.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: de8e3445679e185b488311e58221ae5f4c28f85c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71106029"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF et Xamarin. Forms : similarités & différences

## <a name="control-templates"></a>Modèles de contrôle

WPF prend en charge le concept de *modèles de contrôle* qui fournissent des instructions de visualisation pour un contrôle (`Button`, `ListBox`, etc.). Comme indiqué ci-dessus, Xamarin. Forms utilise des classes de _rendu_ concrètes pour ce qui interagissent avec la plateforme native (iOS, Android, etc.) pour visualiser le contrôle.

Toutefois, Xamarin _. Forms a un_ type de `ControlTemplate`, il est utilisé pour les thèmes `Page` objets. Il fournit une définition pour une `Page` qui fournit du contenu cohérent, mais permet à l’utilisateur de la page de modifier les couleurs, les polices, etc., et même d’ajouter des éléments pour le rendre unique à l’application.

Les utilisations courantes de ce sont des tâches telles que les boîtes de dialogue d’authentification, les invites et la fourniture d’un aspect de page standardisé, mais pouvant être personnalisé, qui peut être personnalisé au sein de l’application. Dans le cadre de cette prise en charge, de nombreux contrôles familiers nommés WPF sont utilisés :

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

Mais il est important de savoir qu’elles _ne servent pas_ le même objectif dans Xamarin. Forms. Pour plus d’informations sur cette fonctionnalité, consultez la [page de documentation](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML est utilisé comme langage de balisage déclaratif pour WPF et Xamarin. Forms. Pour l’essentiel, la syntaxe est identique : la principale différence est celle des objets définis/créés par les graphiques XAML.

- Xamarin. Forms prend en charge la [spécification XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); Il est ainsi plus facile de définir des données telles que des `string`s, des `int`s, etc., ainsi que de définir des types génériques et de passer des arguments aux constructeurs.

- Il n’existe actuellement aucun moyen de charger dynamiquement XAML, comme WPF, avec `XamlReader`. Vous pouvez cependant obtenir les mêmes fonctionnalités de base avec un [package NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) .

### <a name="markup-extensions"></a>Extensions de balisage

Xamarin. Forms prend en charge l’extension du code XAML par le biais des extensions de balisage, comme WPF. Les mêmes blocs de construction de base sont les mêmes :

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

En outre, il comprend `{x:Reference}` de la spécification XAML 2009 et une extension de balisage `{TemplateBinding}` qui est utilisée pour la version spécialisée de `ControlTemplate` prise en charge par Xamarin. Forms.

> [!WARNING]
> La prise en charge de la `ControlTemplate` n’est pas la même, même si elle porte le même nom.

Xamarin. Forms prend également en charge les extensions de balisage personnalisées, mais l’implémentation est légèrement différente. Dans WPF, vous devez dériver de `MarkupExtension`-une classe de base abstraite. Dans Xamarin. Forms, qui est remplacé par une interface `IMarkupExtension` ou `IMarkupExtension<T>` qui est plus flexible.

Tout comme WPF, la méthode unique requise est une méthode `ProvideValue` pour retourner la valeur de l’extension de balisage.

## <a name="binding-infrastructure"></a>Infrastructure de liaison

L’un des concepts de base reportés est une infrastructure de liaison de données permettant de connecter des propriétés visuelles à des propriétés de données .NET. Cela permet d’activer des modèles architecturaux tels que MVVM. La conception de base est identique : vous avez une classe de base pouvant être liée [bindableobject](xref:Xamarin.Forms.BindableObject), dans WPF, il s’agit de la classe [DependencyObject](xref:System.Windows.DependencyObject) . Cette classe de base est utilisée comme ancêtre racine pour tous les objets qui feront partie de cibles dans la liaison de données. Les classes dérivées exposent ensuite les objets [BindableProperty](xref:Xamarin.Forms.BindableProperty) qui jouent le rôle de stockage de sauvegarde pour les valeurs de propriété (ils sont définis en tant qu’objets [DependencyProperty](xref:System.Windows.DependencyProperty) dans WPF).

### <a name="defining-bindable-properties"></a>Définition des propriétés pouvant être liées

La définition d’une propriété pouvant être liée dans Xamarin. Forms est la même que WPF :

1. L’objet doit dériver de `BindableObject`.
2. Il doit y avoir un champ public static de type `BindableProperty` déclaré pour définir la clé de stockage de sauvegarde pour la propriété.
3. Il doit y avoir un wrapper de propriété d’instance publique qui utilise `GetValue` et `SetValue` pour récupérer et modifier la valeur des propriétés.

Pour obtenir un exemple complet, consultez [propriétés pouvant être liées dans Xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Propriétés jointes

Les propriétés jointes sont un sous-ensemble de la propriété pouvant être liée et fonctionnent de la même façon que dans WPF. La principale différence réside dans le fait que le wrapper de propriété est ommitted dans ce cas et remplacé par un ensemble de méthodes d’extraction/définition statiques sur la classe propriétaire. Pour plus d’informations, consultez [propriétés jointes dans Xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) .

### <a name="using-the-binding-engine"></a>Utilisation du moteur de liaison

Le processus d’utilisation du moteur de liaison est le même que dans WPF. Il peut être utilisé dans le code-behind en créant un objet `Binding` lié à un objet source (tout type .NET) et une valeur de propriété facultative (si ommitted, il traite l’objet source comme la propriété elle-même, comme WPF). Vous pouvez ensuite utiliser `SetBinding` sur n’importe quel `BindableObject` pour associer la liaison à un `BindableProperty`.

Vous pouvez également définir la relation de liaison en XAML à l’aide de l' `BindingExtension`. Il a les mêmes valeurs de base que l’extension dans WPF.

La prise en charge de la liaison et le moteur sont plus similaires à l’implémentation de Silverlight que WPF. Il existe plusieurs fonctionnalités manquantes qui n’ont pas été implémentées dans Xamarin. Forms :

- Il n’existe aucune prise en charge pour les fonctionnalités suivantes dans les liaisons :
  - BindingGroupName
  - BindsDirectlyToSource
  - IsAsync
  - MultiBinding
  - NotifyOnSourceUpdated
  - NotifyOnTargetUpdated
  - NotifyOnValidationError
  - UpdateSourceTrigger
  - UpdateSourceExceptionFilter
  - ValidatesOnDataErrors
  - ValidatesOnExceptions
  - Collection ValidationRules
  - XPath
  - XmlNamespaceManager

#### <a name="relativesource"></a>RelativeSource

Il n’existe aucune prise en charge pour les liaisons de `RelativeSource`. Dans WPF, elles vous permettent d’effectuer une liaison à d’autres éléments visuels définis en XAML. Dans Xamarin. Forms, cette même fonctionnalité peut être obtenue à l’aide de l’extension de balisage `{x:Reference}`. Par exemple, en supposant que nous avons un contrôle portant le nom « otherControl » qui a une propriété de texte, nous pouvons y créer une liaison de ce type :

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

La même fonctionnalité peut être utilisée pour la fonctionnalité `{RelativeSource Self}`. Toutefois, il n’existe pas de prise en charge pour localiser les ancêtres par type (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contexte de liaison

Dans WPF, vous pouvez définir une valeur de propriété `DataContext` qui reprents la source de liaison par défaut. Si la source d’une liaison n’est pas définie, cette valeur de propriété est utilisée. La valeur est héritée de l’arborescence d’éléments visuels, ce qui lui permet d’être définie à un niveau supérieur, puis utilisée par les enfants.

Dans Xamarin. Forms, cette même fonctionnalité est disponibles, mais le nom de la propriété est `BindingContext`.

#### <a name="value-converters"></a>convertisseurs de valeurs ;

Les convertisseurs de valeurs sont entièrement pris en charge dans Xamarin. Forms, tout comme WPF. La même forme interface est utilisée, mais Xamarin. Forms a l’interface définie dans l’espace de noms `Xamarin.Forms`.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM est entièrement pris en charge par WPF et Xamarin. Forms.

WPF comprend un `RoutedCommand` intégré qui est parfois utilisé ; Xamarin. Forms n’a pas de prise en charge des commandes intégrées au-delà de la définition d’interface `ICommand`. Vous pouvez inclure diverses infrastructures MVVM pour ajouter les classes de base nécessaires pour implémenter MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged et INotifyCollectionChanged

Les deux interfaces sont entièrement prises en charge dans les liaisons Xamarin. Forms. Contrairement à de nombreuses infrastructures basées sur XAML, les notifications de modification de propriété peuvent être déclenchées sur les threads d’arrière-plan dans Xamarin. Forms (tout comme WPF) et le moteur de liaison passera correctement au thread d’interface utilisateur.

En outre, les deux environnements prennent en charge `SynchronziationContext` et `async` / `await` pour effectuer un marshaling de thread approprié. WPF inclut la classe `Dispatcher` sur tous les éléments visuels, Xamarin. Forms a une méthode statique `Device.BeginInvokeOnMainThread` qui peut également être utilisée (même si `SynchronizationContext` est préféré pour le codage multiplateforme).

- Xamarin. Forms comprend une `ObservableCollection<T>` qui prend en charge les notifications de modification de collection.
- Vous pouvez utiliser `BindingBase.EnableCollectionSynchronization` pour activer les mises à jour inter-threads pour une collection. L’API est légèrement différente de la variante WPF. [pour plus d’informations sur l’utilisation, consultez la documentation](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Modèles de données

Les modèles de données sont pris en charge dans Xamarin. Forms pour personnaliser le rendu d’un `ListView` ligne (cellule). Contrairement à WPF qui peut utiliser `DataTemplate`s pour tout contrôle orienté contenu, Xamarin. Forms les utilise actuellement uniquement pour les `ListView`. La définition de modèle peut être définie en ligne (affectée à la propriété `ItemTemplate`) ou en tant que ressource dans une `ResourceDictionary`.

En outre, ils ne sont pas aussi flexibles que leurs équivalents WPF.

1. L’élément racine de l' `DataTemplate` doit _toujours_ être un objet `ViewCell`.
2. Les déclencheurs de données sont entièrement pris en charge dans un modèle de données, mais doivent inclure une `DataType` propriété indiquant le type de la propriété à laquelle le déclencheur est associé.
3. `DataTemplateSelector` est également pris en charge, mais dérive de `DataTemplate` et est donc simplement assigné directement à la propriété `ItemTemplate` (par rapport à  `ItemTemplateSelector` dans WPF).

## <a name="itemscontrol"></a>ItemsControl

Il n’existe aucun équivalent intégré à un `ItemsControl` dans Xamarin. Forms ; mais il existe un [personnalisé pour Xamarin. Forms disponible ici](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Contrôles utilisateur

Dans WPF, les `UserControl`s sont utilisées pour fournir une section de l’interface utilisateur associée au comportement. Dans Xamarin. Forms, nous utilisons le `ContentView` dans le même but. Les deux prennent en charge la liaison et l’inclusion en XAML.

## <a name="navigation"></a>Navigation

WPF comprend un `NavigationService` rarement utilisé qui peut être utilisé pour fournir une fonctionnalité de navigation « de type navigateur ». La plupart des applications ne se sont pas encore dérangées, mais utilisaient différents éléments de `Window`, ou différentes sections de la fenêtre pour afficher les données.

Sur les appareils téléphoniques, les différents _écrans_ sont souvent la solution et donc Xamarin. Forms prend en charge plusieurs formes de navigation :

| Style de navigation | Type de page |
|--- |--- |
|Basée sur la pile (push/pop)|NavigationPage|
|Maître/détail|MasterDetailPage|
|Tabulations|TabbedPage|
|Balayer vers la gauche/droite|CarouselView|

La `NavigationPage` est l’approche la plus courante, et chaque page a une propriété `Navigation` qui peut être utilisée pour pousser ou dépiler des pages sur la pile de navigation. Il s’agit de l’équivalent le plus proche de la `NavigationService` trouvée dans WPF.

### <a name="url-navigation"></a>Navigation dans les URL

WPF est une technologie orientée Bureau qui peut accepter des paramètres de ligne de commande pour diriger le comportement de démarrage. Xamarin. Forms peut utiliser la [liaison d’URL profonde](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) pour accéder à une page au démarrage.
